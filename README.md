# EC2 Image Builder Workshop

EC2 Image Builder is a fully managed AWS service that makes it easier to automate the creation, management, and deployment of customized, secure, and up-to-date “golden” server images that are pre-installed and pre-configured with software and settings to meet specific IT standards. 

When you use the EC2 Image Builder console to create a golden image, a wizard guides you through the following steps. 
- Select Source Image 
- Create Image Recipe 
- Output 
- Distribute 

In this lab, you will go through a process that involves selecting Amazon Linux 2 AMI as base image and creating custom Build/Test Components as development artifacts within AWS EC2 Image Builder (https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html)

## Step-1: Login to AWS Workshop Portal 
This workshop creates an AWS account. 

Connect to the portal by clicking the button or browsing to https://dashboard.eventengine.run/. You will need the Participant Hash to track your unique session and facilitators will be providing you a hash accordingly.

The following screen shows up.

![Event Engine](/images/event-engine-initial-screen.png)

Enter the provided hash in the text box. The button on the bottom right corner changes to **Accept Terms & Login**. Click on that button to continue.

![Event Engine Dashboard](/images/event-engine-dashboard.png)

Click on **AWS Console** on dashboard.

![Event Engine AWS Console](/images/event-engine-aws-console.png)

Take the defaults and click on **Open AWS Console**. This will open AWS Console in a new browser tab.

## Step-2: Create an Amazon S3 Bucket and Copy Artifacts 

In this step, we will create an S3 bucket and upload artifacts that will later be used within build component of EC2 Image Builder Pipeline. Artifacts include configuration files for CloudWatch Agent and CIS Benchmark hardening

1. Follow [this deep link to navigate to Amazon S3 console](https://s3.console.aws.amazon.com/s3/home?region=us-east-1#)
2. Click on Create Bucket and create a bucket with unique name such as "configuration-bucket-***teamhash***"
3. While creating bucket leave rest of settings untouched
4. Now navigate to **cloudwatch** and **cis-benchmarks** directories in the workshop module and copy all the files in s3 folder to bucket you created in step 3
![copy artifacts](/images/s3files.png)  

## Step-3: Create SSM Instance Role and SNS subscription

EC2 Image Builder leverages SSM Automation for Orchestrating Build/Test activities. In this step, we will create an SSM Instance Role that pipeline can leverage while building Golden Image. In addition, we will also create SNS topic and subscription to ensure we get notifications at each stage 

1. Follow [this deep link to create an EC2 Instance Role with SSM Access](https://console.aws.amazon.com/iam/home?region=us-east-1#/roles$new?step=type&commonUseCase=EC2%2BEC2&selectedUseCase=EC2)
2. Confirm that **AWS service** and **EC2** are selected, then click **Next** to view permissions.
3. Search for following 3 policies and select check box beside them 
   > AmazonInspectorFullAccess 

   > EC2InstanceProfileForImageBuilder

   > AmazonEC2RoleforSSM   

4. Go to next step, give role a name such as SSM_Instance_Role and click Create Role
![Create Role](/images/createrole.png)
5. Follow [this deep link to create IAM Policy Topic named SSMSendCommand](https://console.aws.amazon.com/iam/home?region=us-east-1#/policies$new?step=edit)
   
      ```json
            {
            "Version": "2012-10-17",
            "Statement": [
               {        
                  "Sid": "VisualEditor0",
                  "Effect": "Allow",
                  "Action": [
                     "ssm:SendCommand",
                     "ec2:CreateTags"
                  ],
                  "Resource": "*"
               }
            ]
            }
      ```

6. Navigate to IAM Roles tab and search for SSM_Instance_Role created above
7. Click Attach policies and attach the newly created SSMSendCommand policy

8. Follow [this deep link to create SNS Topic](https://console.aws.amazon.com/sns/v3/home?region=us-east-1#/create-topic)
9.  Give SNS Topic a name such as ImageBuilder and Create Topic 
10. Click Create Subscription, select Protocol as Email and give your email address as Endpoint
![Create SNS Subscription](/images/sns.png)
9. You will get an email to confirm subscription and confirm it accordingly

## Step-4: Create Component Documents for EC2 Image Builder 

In this step, we will create custom Components for Build phase of Pipeline.

1. Follow [this deep link to navigate to Components tab of EC2 Image Builder console.] (https://console.aws.amazon.com/imagebuilder/home?region=us-east-1#viewComponents)
2. Click on Create Component and select following settings -
   1. Compatible OS Versions "Amazon Linux 2"
   2. Component name as "setup-cloudwatch-agent"
   3. Component version as "1.0.0"
   4. Description as "Installing and Configuring CloudWatch Agent"
![Create Build Component I](/images/createcomponent-1.png)
3. In the "Definition document" section - copy contents from file named "cloudwatch.yaml" in **cloudwatch** directory of the workshop 
4. Replace '<s3_bucket>' in line 22 of YAML component file with bucket name you created in Step-2 above and click Create Component 
5. Click on Create component again and select following settings -
   1. Compatible OS Versions "Amazon Linux 2"
   2. Component name as "cis-benchmarks"
   3. Component version as "1.0.0"
   4. Description as "Component to build a hardened image according to CIS Amazon Linux 2 Benchmark version 1.0.0"
![Create Build Component II](/images/createcomponent-2.png) 
6. In the "Definition document" section - copy contents from file named "cis-benchmarks.yaml" in **cis-benchmarks** directory of the workshop 
7. Replace '<s3_bucket>' of YAML component file with bucket name you created in Step-2 above and click Create Component 
![Final Component Screen](/images/createcomponent-final.png) 

## Step-5: Create EC2 Image Builder Pipeline  

In this step, we will create a EC2 Image Builder Recipe and Pipeline by leveraging components, IAM Role and SNS topics built in previous steps. 

1. Follow [this deep link to navigate to Pipeline tab of EC2 Image Builder console](https://console.aws.amazon.com/imagebuilder/home?region=us-east-1#viewPipeline)
2. Click Create Image Pipeline and leave Select managed images bullet checked and pick Amazon Linux as Image OS. 
3. Click on Browse images and select "Amazon Linux 2 x86 | Version 2020.9.4" 
![Recipe definition I](/images/recipe-1.png) 
4. Leave Storage Section with default values
5. In Build Components section, click on Browse build components and select following components in order 
   1. update-linux
   2. update-linux-kernel-mainline
6. Without exiting search bar, click on drop down beside search, select "Created by me" and pick following components in order
   1. setup-cloudwatch-agent
   2. cis-benchmarks 
![Recipe definition II](/images/recipe-2.png) 
7. In Test Components section, click on Browse tests and select following components in order 
   1. chrony-time-configuration-test
   2. inspector-test-linux
![Recipe definition III](/images/recipe-3.png) 
8. Click Next and give the pipeline name of your choice such as "GoldenAMIPipeline"
9. For IAM Role, from drop down pick IAM Role created in Step-3 i.e. SSM_Instance_Role and leave Build Schedule as Manual 
![Recipe definition IV](/images/recipe-4.png) 
10. Under Infrastructure Settings, select Instance type as "t3.medium", SNS Topic as one you created in Step-3 above and S3 Location for Pipeline Logs as same bucket we created to store component files and click Next 
![Recipe definition V](/images/recipe-5.png) 
11. [**Optional step**] In the next page, under Output AMI - give a name for AMI, tags and under Distribution settings - you can add a colleague's account number as optional value to distribute AMI resulted as a part of pipeline 
12. Create Pipeline, select it and from Actions tab - click **Run Pipeline**
![Recipe definition VI](/images/recipe-6.png) 

## Step-6: Verifying AWS Inspector findings  
After you Run Pipeline - it will take about an hour for Inspector to Install agent, run assessment and provide findings.

If you navigate to [Inspector console](https://us-east-1.console.aws.amazon.com/inspector/home?region=us-east-1#/run) you can find Assessment run and findings accordingly

![Inspector I](/images/inspector-1.png)

![Inspector II](/images/inspector-2.png) 