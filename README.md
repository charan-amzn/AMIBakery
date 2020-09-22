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
4. Now navigate to **cloudwatch** and **cis-benchmark** directories in the workshop module and copy all the files in s3 folder to bucket you created in step 3
![copy artifacts](/images/s3files.png)  