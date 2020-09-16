# ec2-image-builder
Custom Build and Test Components developed for AWS EC2 Image Builder (https://docs.aws.amazon.com/imagebuilder/latest/userguide/what-is-image-builder.html)

## Configuration
1. Create a S3 bucket (configuration-bucket) and place files in `S3` folder of component.
2. Create EC2 Instance Role and attach following required policies:
- arn:aws:iam::aws:policy/service-role/AmazonEC2RoleforSSM
- arn:aws:iam::aws:policy/EC2InstanceProfileForImageBuilder
3. Replace `<bucket-name>` with your newly created bucket name (configuration-bucket) in yaml component file
4. [Create EC2 Image Builder](https://console.aws.amazon.com/imagebuilder/home#createPipeline) pipeline with Amazon Linux 2
5. [Create Components](https://console.aws.amazon.com/imagebuilder/home#createComponent) that you want to use by coping yaml component file
6. Select IAM Role that you have created on Step 2
7. Name your pipeline
  - For build components pick update-linux, update-linux-kernel-mainline, setup-cloudwatch-agent and cis-benchmarks
  - For test components pick chrony-time-configuration-test and inspector-test-linux
8. Name your AMI
9. Trigger pipeline

## Troubleshooting
- You can disable `Terminate instance on failure` option and select a keypair while creating a pipeline to connect and troubleshoot any errors on the build instance.
- You can follow and check AWS Systems Manager logs from, https://console.aws.amazon.com/systems-manager/automation/executions
