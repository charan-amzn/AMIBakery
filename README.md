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

1. Follow [this deep link to navigate to Amazon S3 console.] (https://s3.console.aws.amazon.com/s3/home?region=us-east-1#)
2. Click on Create Bucket and create a bucket with unique name such as "configuration-bucket-***teamhash***"
3. While creating bucket leave rest of settings untouched
4. Now navigate to **cloudwatch** and **cis-benchmarks** directories in the workshop module and copy all the files in s3 folder to bucket you created in step 3
![copy artifacts](/images/s3files.png)  

## Step-3: Create SSM Instance Role 
1. Follow [this deep link to create an EC2 Instance Role with SSM Access](https://console.aws.amazon.com/iam/home?region=us-east-1#/roles$new?step=type&commonUseCase=EC2%2BEC2&selectedUseCase=EC2)
2. Confirm that **AWS service** and **EC2** are selected, then click **Next** to view permissions.
3. Search for following 3 policies and select check box beside them 
   > AmazonInspectorFullAccess 

   > EC2InstanceProfileForImageBuilder

   > AmazonEC2RoleforSSM   

4. Go to next step, give role a name such as SSM_Instance_Role and click Create Role
![Create Role](/images/createrole.png)

This role is used by SSM agent running on EC2 Instance to execute custom scripts on base AMI and create golden AMI respectively.

## Step-4: Create Component Documents for EC2 Image Builder 
1. Follow [this deep link to navigate to EC2 Image Builder console.] (https://console.aws.amazon.com/imagebuilder/home?region=us-east-1#viewComponents)
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