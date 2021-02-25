# Minimum Web Service Architecture

## Introduction

A frontend template file (frontend_template.yml) and a backend template file (backend_template.yml) are provided. The frontend template file will create a S3 bucket hosting frontend html and related files, frontend distribution, and Route53 frontend record when custom domain is specified. The backend template file will create two EC2 instances hosting RESTAPI service, one RDS instance, an Application Load Balancer, backend distribution, and Route53 backend record when custom domain is specified.

## Components

Use the following two template files to setup Web service.

- Frontend template file: frontend_template.yml
- Backend template file: backend_template.yml

### frontend template:

Frontend template will create the following resources.
- S3 bucket for frontend page hosting
- Bucket policy related to the S3 bucket
- CloudFront Origin Access Identity
- CloudFront Distribution
- Route53 record if custom domain is specified

#### Parameters:

The following parameters are required for frontend template.
- S3FrontBucket:
  - Description: Frontend bucket name
  - Type: String
- WebsiteDomainName (optional):
  - Description: Frontend domain name. If this parameter value is blank, Route53 record is not set.
  - Type: String
- SSLCertificateARN (optional):
  - Description: ACM certificate identity related to custom website domain.
  - Type: String
- HostedZoneResource (optional):
  - Description: Hosted zone id related to custom website domain.
  - Type: AWS::Route53::HostedZone::Id
- FrontendDistributionDefaultTTL:
  - Description: The default amount of time in CloudFront caches, in seconds. If not specified, default value is used.
  - Type: String
  - Default value: 86400
- FrontendDistributionMaxTTL:
  - Description: The maximum amount of time in CloudFront caches, in seconds. If not specified, default value is used.
  - Type: String
  - Default value: 31536000
- FrontendDistributionMinTTL:
  - Description: The minimun amount of time in CloudFront caches, in seconds. If not specified, default value is used.
  - Type: String
  - Default value: 0
- TagKey:
  - Description: Resources tag key name that identifies related stack. If not specified, default value is used.
  - Type: String
  - Default value: StackName
- ApplicationName:
  - Description: Resources tag value that identifies related stack. Please set your application name here.
  - Type: String

### backend template:

Backend template will create the following resources.
- VPC
- Two public subnets and two private subnets. Each subnet uses default network ACL.
- Two EC2 instances which are hosting backend application
- Custom IAM role for EC2 instance
- Security Group for EC2 instance
- RDS instance
- Security Group for RDS
- Subnet group for RDS
- ALB connected with the two EC2 instances
- Security Group for ALB
- ALB Listener
- ALB Target Group
- CloudFront Distribution
- Route53 record

#### Parameters:

The following parameters are required for backend template.
- CidrBlockVPC:
  - Description: CIDR block for VPC
  - Type: String
  - Default: 10.0.0.0/16
- CidrBlockSubnetPublic1:
  - Description: CIDR block for public subnet 1
  - Type: String
  - Default: 10.0.0.0/24
- CidrBlockSubnetPublic2:
  - Description: CIDR block for public subnet 2
  - Type: String
  - Default: 10.0.2.0/24
- CidrBlockSubnetPrivate1:
  - Description: CIDR block for private subnet 1
  - Type: String
  - Default: 10.0.1.0/24
- CidrBlockSubnetPrivate2:
  - Description: CIDR block for private subnet 2
  - Type: String
  - Default: 10.0.3.0/24
- EC2ImageId:
  - Description: The latest Amazon Linux 2 image id
  - Type: AWS::SSM::Parameter::Value<String>
  - Default: /aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2
- InstanceType:
  - Description: RESTAPI EC2 instance type
  - Type: String
  - Default: t2.micro
- KeyName:
  - Description: Name of an existing EC2 KeyPair to enable SSH access to the instance
  - Type: 'AWS::EC2::KeyPair::KeyName'
- SSHLocation:
  - Description: The IP address range that can be used to SSH to the EC2 instances
  - Type: String
  - Default: 0.0.0.0/0
- ContainerRepositoryName:
  - Description: Container Repository name used by EC2 instance to retrive container image from ECR
  - Type: String
- S3DataBucketName (optional):
  - Description: S3 Data bucket name used by backend restapi
  - Type: String
  - Default: *
- DBClass:
  - Description: Database instance class
  - Type: String
  - Default: db.t3.small
- MultiAZDatabase:
  - Description: Create a Multi-AZ RDS database instance
  - Type: String
  - Default: 'false'
- DBAllocatedStorage:
  - Description: The size of the database (Gb)
  - Type: Number
  - Default: '100'
- DBSnapshotIdentifier (optional):
  - Description: The snapshot name of DB instance. Blank value will create a new DB instance
  - Type: String
- DatabaseEngine:
  - Description: Database Engine
  - Type: String
  - Default: postgres
- DBName:
  - Description: RESTAPI database name
  - Type: String
  - Default: postgres
- DBSchema:
  - Description: The postgres database schema
  - Type: String
  - Default: app
- DBUser:
  - Description: The postgres database username
  - Type: String
  - Default: postgres
- DBPassword:
  - Description: The WordPress database admin account password
  - Type: String
  - Default: postgres
- CustomOriginHTTPPort:
  - Description: Backend CloudFront HTTP Port
  - Type: String
  - Default: '80'
- CustomOriginHTTPSPort:
  - Description: Backend CloudFront HTTPS Port
  - Type: String
  - Default: '443'
- InstanceSSHPort:
  - Description: Backend Instance Security Group SSH port
  - Type: String
  - Default: '22'
- InstanceHTTPPort:
  - Description: Backend Instance Security Group HTTP port
  - Type: String
  - Default: '80'
- LoadBalancerHTTPPort:
  - Description: Load Balancer Security Group HTTP port
  - Type: String
  - Default: '80'
- DatabasePort:
  - Description: PostgreSQL Database Port
  - Type: String
  - Default: '5432'
- RestapiDomainName (optional):
  - Description: Restapi domain name
  - Type: String
- SSLCertificateId (optional):
  - Description: ACM certificate identity
  - Type: String
- HostedZoneResource (optional):
  - Description: Hosted zone id
  - Type: String
- BackendDistributionDefaultTTL:
  - Description: The default amount of time in CloudFront caches, in seconds
  - Type: String
  - Default: 86400
- BackendDistributionMaxTTL:
  - Description: The maximum amount of time in CloudFront caches, in seconds
  - Type: String
  - Default: 31536000
- BackendDistributionMinTTL:
  - Description: The minimun amount of time in CloudFront caches, in seconds
  - Type: String
  - Default: 0
- TagKey:
  - Description: Resources tag key name that identifies related stack. If not specified, default value is used.
  - Type: String
  - Default value: StackName
- ApplicationName:
  - Description: Resources tag value that identifies related stack. Please set your application name here.
  - Type: String

## How to use these templates

### Setup environmental variables

1. #### Application name and custom website domain

    Specify your application name and custom website domain (if needed) in `setup.sh` file.
    `setup.sh` files defines several variables used to create stacks.
    - `export APP_NAME='example-app-name'`
    - `export WEBSITE_DOMAIN_NAME=''`

    If you do not specify the custom domain, leave the `WEBSITE_DOMAIN_NAME` blank.

    Leave the other variables as default value. These values will be modified in the following steps.

    Execute the following comand in terminal to set environment variables in `setup.sh`:
    ```
    source setup.sh
    ```
    If you want to specify website custom domain, you have to prepare the following dependencies. Follow the instruction below.
    - Route53 Hosted Zone
    - ACM SSL certificate

2. #### Route53 Hosted Zone (only when you specify custom domain):

    Create new Route53 Hosted Zone for your custom domain. Run the following command in your terminal.
    ```
    aws route53 create-hosted-zone --name "$WEBSITE_DOMAIN_NAME" --caller-reference `date +%Y-%m-%d_%H-%M-%S` --hosted-zone-config Comment="$APP_NAME"
    ```
    You can get Hosted Zone ID like `/hostedzone/Z0961896283IM3W80T0FD`. Copy the ID value `Z0961896283IM3W80T0FD` and set it to `HOSTEDZONE_ID` environment variable in `setup.sh` file.
    - `export HOSTEDZONE_ID='Z0961896283IM3W80T0FD'`

    If your domain is registered with a registrar other than Route 53, you must update the name servers with your registrar to make Route 53 the DNS service for the domain. Yon can see four name servers in the return value like below.
    ```
    NAMESERVERS	ns-348.awsdns-43.com
    NAMESERVERS	ns-1900.awsdns-45.co.uk
    NAMESERVERS	ns-692.awsdns-22.net
    NAMESERVERS	ns-1413.awsdns-48.org
    ```
    For more information about `aws route53 create-hosted-zone` command, see [reference document here](https://docs.aws.amazon.com/cli/latest/reference/route53/create-hosted-zone.html).

3. #### ACM SSL certificate (only when you specify custom domain):

    Before you request new certificate, you have to change aws region to `us-east-1`. Run the following command in terminal.
    ```
    export AWS_DEFAULT_REGION=us-east-1
    ```
    Then, request new certificate by the following command.
    ```
    aws acm request-certificate --domain-name "$WEBSITE_DOMAIN_NAME" --validation-method DNS --subject-alternative-names "*.$WEBSITE_DOMAIN_NAME" --tag Key=Name,Value="$APP_NAME"
    ```
    You can see the certificate arn as the return value. Set the arn value as `SSL_CERTIFICATE_ARN` environment variable in `setup.sh` file.
    - `export SSL_CERTIFICATE_ARN='arn:aws:acm:ap-northeast-1:112234567899:certificate/cfe138d8-bc19-4b2e-be68-ba9e03abef19'`

    Finally, you have to change aws region back to `ap-northeast-1`. Run the following command.
    ```
    export AWS_DEFAULT_REGION=ap-northeast-1
    ```
    For more information about `aws acm request-certificate` command, see [reference document here](https://docs.aws.amazon.com/cli/latest/reference/acm/request-certificate.html).

4. #### EC2 key pair for backend usage:

    For backend cloudformation stack, you have to prepare EC2 Keypair first. Run the following command in terminal.
    ```
    aws ec2 create-key-pair --key-name "$APP_NAME-keypair" --query 'KeyMaterial' --output text > "$APP_NAME-keypair.pem"
    ```
    Then, you have to set `EC2_KEYPAIR_NAME` environment variable in your terminal.
    ```
    export EC2_KEYPAIR_NAME="$APP_NAME-keypair"
    ```
    For more information about `aws ec2 create-key-pair` command, see [reference document here](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-key-pair.html).

5. #### ECR container repository name for backend usage:

    For RESTAPI EC2 instance deploy, specify the ECR container repository name to pull the image. Set `REPOSITORY_NAME` environment variable in `setup.sh` file.
    - `export REPOSITORY_NAME='ECR_repository_name'`

6. #### Database snapshot name to be restored (optional):

    Optinally, you can restore database instance from snapshot. To specify the database snapshot name to be restored, set `DB_SNAPSHOT_NAME` envionment variable in `setup.sh` file. If you leave this variable blank, CloudFormation will create a brand new database instance.
    - `export DB_SNAPSHOT_NAME='example_snapshot_name'`

### Deploy Web service with CloudFormation templates

#### Frontend deploy

Follow the instruction below to deploy frontend resources.

1. Create CloudFormation stack

    Execute the following command:
    ```
    aws cloudformation create-stack --stack-name "$APP_NAME-frontend" \
      --template-body file://frontend_template.yml \
      --parameters \
      ParameterKey=WebsiteDomainName,ParameterValue="$WEBSITE_DOMAIN_NAME" \
      ParameterKey=S3FrontBucket,ParameterValue="$APP_NAME-frontend" \
      ParameterKey=SSLCertificateArn,ParameterValue="$SSL_CERTIFICATE_ARN" \
      ParameterKey=HostedZoneResource,ParameterValue="$HOSTEDZONE_ID" \
      ParameterKey=ApplicationName,ParameterValue="$APP_NAME"
    ```
    For more information about `aws cloudformation create-stack` command, see [reference document here](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/create-stack.html).

2. Check the stack status

    Execute the following command to check the stack status:
    ```
    aws cloudformation describe-stacks --stack-name "$APP_NAME-frontend" --output json | jq -r '.Stacks[] | .StackStatus'
    ```
    Wait until the stack status becomes `CREATE_COMPLETE`.

3. Check the stack output

    Execute the following command to check the output and see S3 frontend bucket name:
    ```
    aws cloudformation describe-stacks --stack-name "$APP_NAME-frontend" --output json | jq -r '.Stacks[] | .Outputs[]'
    ```
    You can see the `S3FrontBucket` name as `OutputValue`:
    ```
    "OutputKey": "S3FrontBucket",
    "OutputValue": "example-application-frontend",
    "Description": "newly created frontend bucket"
    ```

    For more information about `aws cloudformation describe-stacks` command, see [reference document here](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/describe-stacks.html).

4. Upload frontend hosting data to S3 frontend bucket

    From your frontend working directory, execute the following s3 command. This command upload all files and directories in the current working directory.
    ```
    aws s3 cp . "s3://$APP_NAME-frontend/" --recursive
    ```

    For more information about `aws s3 cp` command, see [reference document here](https://docs.aws.amazon.com/cli/latest/reference/s3/cp.html).

#### Backend deploy

Follow the instruction below to deploy backend resources.

1. Create CloudFormation stack

    Execute the following command:
    ```
    aws cloudformation create-stack --stack-name "$APP_NAME-backend" \
      --template-body file://backend_template.yml \
      --parameters \
      ParameterKey=RestapiDomainName,ParameterValue="$RESTAPI_DOMAIN_NAME" \
      ParameterKey=KeyName,ParameterValue="$EC2_KEYPAIR_NAME" \
      ParameterKey=SSLCertificateArn,ParameterValue="$SSL_CERTIFICATE_ARN" \
      ParameterKey=HostedZoneResource,ParameterValue="$HOSTEDZONE_ID" \
      ParameterKey=ApplicationName,ParameterValue="$APP_NAME" \
      ParameterKey=KeyName,ParameterValue="$EC2_KEYPAIR_NAME" \
      ParameterKey=ContainerRepositoryName,ParameterValue="$REPOSITORY_NAME" \
      ParameterKey=DBSnapshotIdentifier,ParameterValue="$DB_SNAPSHOT_NAME" \
      --capabilities CAPABILITY_NAMED_IAM
    ```
    Optional parameters:
    - In case you specify backend s3 data bucket, you can add the bucket name in parameters:
        ```
        ParameterKey=S3DataBucketName,ParameterValue=s3-data-bucket-name
        ```
    - If you want to use multi-AZ database, you have to set `MultiAZDatabase` parameter `true`:
        ```
        ParameterKey=MultiAZDatabase,ParameterValue=true
        ```
    - If you want to customize database name, user name, password, and schema, you can add the following parameters:
        ```
        ParameterKey=DBName,ParameterValue=custom-database-name \
        ParameterKey=DBUser,ParameterValue=custom-user-name \
        ParameterKey=DBUser,ParameterValue=custom-password \
        ParameterKey=DBSchema,ParameterValue=custom-schema \
        ```
    - If you want to specify database storage size, you can set `DBAllocatedStorage` parameter:
        ```
        ParameterKey=DBAllocatedStorage,ParameterValue=5
        ```
    - If you want to use other database engine such as MySQL, you have to setup `DatabaseEngine` and `DatabasePort`:
        ```
        ParameterKey=DatabaseEngine,ParameterValue=MySQL \
        ParameterKey=DatabasePort,ParameterValue=3306
        ```

2. Check the stack status

    Execute the following command to check the stack status:
    ```
    aws cloudformation describe-stacks --stack-name "$APP_NAME-backend" --output json | jq -r '.Stacks[] | .StackStatus'
    ```
    Wait until the stack status becomes `CREATE_COMPLETE`.

3. Check the stack output

    Execute the following command to check the output and see EC2 istance public IPs:
    ```
    aws cloudformation describe-stacks --stack-name "$APP_NAME-backend" --output json | jq -r '.Stacks[] | .Outputs[]'
    ```
    You can see the `EC2Instance1IP` and `EC2Instance2IP` as follow:
    ```
    "OutputKey": "EC2Instance1IP",
    "OutputValue": "18.181.193.35",
    "Description": "newly created instance IP address"
    ```
    ```
    "OutputKey": "EC2Instance2IP",
    "OutputValue": "13.112.27.21",
    "Description": "newly created instance IP address"
    ```

4. Check if docker runs collectly in EC2 instances through ssh connection

    Connect to each two EC2 instances and check if docker runs collectly. First, connect to EC2 instance through ssh:
    ```
    ssh -i "$EC2_KEYPAIR_NAME.pem" ec2-user@instance-public-ip
    ```
    Then, in the EC2 instance, check if the container runs collectly:
    ```
    docker ps
    ```
