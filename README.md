This is a small project that was required as part of the Digital Cloud Academy section on Infrastructure as Code. 
The cloudformation template deploys a VPC, EC2 instance and RDS database. The EC2 instance will run a Python script (supplied) that connects to RDS and creates/retrieves a record.

=======================
# Instructions to upload templates to S3: 

1. Upload files to cloudshell environment

2. Upload to s3 with the following CLI commend in Cloudwhell:

   aws s3 cp <file-name> s3://<bucketname>
----------------

aws s3 cp VPC-nested-template-challenge-25FEB2025.yaml s3://teamplate-challenge-dk25feb2025
aws s3 cp Public-Subnet-nested-template-challenge-25FEB2025.yaml s3://teamplate-challenge-dk25feb2025
aws s3 cp private-subnets-nested-template-challenge-25FEB2025.yaml s3://teamplate-challenge-dk25feb2025
aws s3 cp database-subnet-group-nested-template-challenge-25FEB2025.yaml s3://teamplate-challenge-dk25feb2025
aws s3 cp IGW-nested-stack-template-challenge-25FEB2025.yaml s3://teamplate-challenge-dk25feb2025
aws s3 cp Route-Tables-nested-template-challenge-25FEB2025.yaml s3://teamplate-challenge-dk25feb2025
aws s3 cp Public-SG-nested-template-challenge-25FEB2025.yaml s3://teamplate-challenge-dk25feb2025
aws s3 cp Private-SG-nested-template-challenge-25FEB2025.yaml s3://teamplate-challenge-dk25feb2025
aws s3 cp Public-Instance-nested-template-challenge-25FEB2025.yaml s3://teamplate-challenge-dk25feb2025
aws s3 cp RDS-Instance-nested-template-challenge-25FEB2025.yaml s3://teamplate-challenge-dk25feb2025
aws s3 cp Additional-SG-rules-nested-template-challenge-25FEB2025.yaml s3://teamplate-challenge-dk25feb2025


3. Retrieve the urls of each file in the bucket: 

aws s3api list-objects --bucket teamplate-challenge-dk25feb2025 --query "Contents[].{Key: Key}" --output text | awk '{ print "https://teamplate-challenge-dk25feb2025.s3.amazonaws.com/" $1 }'

3. Create a file named main.yaml with the following content (replace your-bucket-name with the name of the S3 bucket where you uploaded the templates)

NOTE: the resources have parameters that references other stack components.

```yaml
AWSTemplateFormatVersion: '2010-09-09'
Resources:
  VPCStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: https://<bucketname>.s3.amazonaws.com/vpc.yaml

  Subnet1Stack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: https://<bucketname>.s3.amazonaws.com/subnet1.yaml
      Parameters:
        VpcId: !GetAtt VPCStack.Outputs.VpcId

  Subnet2Stack:
    Type: AWS::CloudFormation::Stack
    Properties:
      TemplateURL: https://<bucketname>.s3.amazonaws.com/subnet2.yaml
      Parameters:
        VpcId: !GetAtt VPCStack.Outputs.VpcId
```
4. Validate the template

aws cloudformation validate-template --template-body file://main-nested-template-challenge-25FEB2025.yaml 

5. Deploy the main stack using the AWS CloudFormation CLI

aws cloudformation create-stack --stack-name nested-template-challenge-13 --template-body file://main-nested-template-challenge-25FEB2025.yaml --capabilities CAPABILITY_NAMED_IAM

6. The stack can be deleted with the following command

aws cloudformation delete-stack --stack-name <Stack Name>

--Additional Validations

aws cloudformation validate-template --template-body file://VPC-nested-template-challenge-25FEB2025.yaml @$s3
aws cloudformation validate-template --template-body file://Public-Subnet-nested-template-challenge-25FEB2025.yaml @$s3
aws cloudformation validate-template --template-body file://private-subnets-nested-template-challenge-25FEB2025.yaml @$s3
aws cloudformation validate-template --template-body file://database-subnet-group-nested-template-challenge-25FEB2025.yaml 
aws cloudformation validate-template --template-body file://IGW-nested-stack-template-challenge-25FEB2025.yaml 
aws cloudformation validate-template --template-body file://Route-Tables-nested-template-challenge-25FEB2025.yaml 
aws cloudformation validate-template --template-body file://Public-SG-nested-template-challenge-25FEB2025.yaml 
aws cloudformation validate-template --template-body file://Private-SG-nested-template-challenge-25FEB2025.yaml 
aws cloudformation validate-template --template-body file://Public-Instance-nested-template-challenge-25FEB2025.yaml @$s3
aws cloudformation validate-template --template-body file://RDS-Instance-nested-template-challenge-25FEB2025.yaml @$s3
aws cloudformation validate-template --template-body file://Additional-SG-rules-nested-template-challenge-25FEB2025.yaml
