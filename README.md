# AMIBakery

1. Create SSM Parameter named latestAMZNLinuxAMI with random or no value 
1. Create Python 3.8 Lambda Function named Automation-SSMParameter and copy code in lambda_function.py
2. Create Test Event with following details and invoke function
  {
   "parameterName":"latestAMZNLinuxAmi",
   "parameterValue":"latest-id-from-EC2-Console"
}
3. Update IAM Role fields in JSON file and create Automation document in SSM
4. Verify that SSM Parameter value is updated with new Golden AMI
