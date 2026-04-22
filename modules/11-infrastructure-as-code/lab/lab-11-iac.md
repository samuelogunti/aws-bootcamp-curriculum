# Lab 11: Infrastructure as Code with CloudFormation and SAM

## Objective

Build and deploy AWS infrastructure using CloudFormation templates and the AWS Serverless Application Model (SAM), progressing from a basic VPC stack to a parameterized, multi-resource template and a serverless application.

## Architecture Diagram

This lab builds two separate CloudFormation stacks. The first stack provisions networking and compute resources using a CloudFormation template. The second stack deploys a serverless API using SAM. The components and their relationships are as follows:

```
Stack 1: lab11-network (CloudFormation)
├── VPC (10.0.0.0/16)
│   ├── Public Subnet (10.0.1.0/24, us-east-1a)
│   │   ├── Internet Gateway (attached to VPC)
│   │   ├── Route Table (0.0.0.0/0 -> IGW)
│   │   ├── Security Group (allow SSH port 22, HTTP port 80)
│   │   └── EC2 Instance (t2.micro, Amazon Linux 2023)
│   └── Private Subnet (10.0.2.0/24, us-east-1b)
│
├── Parameters: EnvironmentName, VpcCidr
├── Outputs (exported): VPC ID, Public Subnet ID, Private Subnet ID

Stack 2: lab11-sam-api (SAM)
├── API Gateway (REST API)
│   └── GET /items -> Lambda Function (Python 3.12)
│                        └── DynamoDB Table (lab11-items)
```

You start by writing a minimal CloudFormation template for a VPC with two subnets and deploying it. You then add parameters and update the stack using a change set. Next, you add an EC2 instance and security group to the template. You add outputs that export resource IDs for cross-stack reference. You then build a serverless API using SAM with Lambda, API Gateway, and DynamoDB. You test the SAM application locally and deploy it to AWS. Finally, you delete both stacks and verify cleanup.

## Prerequisites

- Completed [Lab 03: VPC Setup](../../03-networking-basics/lab/lab-03-vpc-setup.md) (VPC, subnets, route tables, and security groups that you will now define as code)
- Completed [Lab 04: EC2 Instances](../../04-compute-ec2/lab/lab-04-ec2-instances.md) (EC2 instance launch and configuration)
- Completed [Lab 09: Building Serverless Applications](../../09-serverless-lambda/lab/lab-09-lambda.md) (Lambda functions, API Gateway, and DynamoDB)
- Completed [Module 11: Infrastructure as Code](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI installed and configured locally)
- AWS SAM CLI installed ([installation guide](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html))
- Docker installed and running (required for `sam local invoke`)

## Duration

90 minutes

## Instructions

### Step 1: Write and Deploy a Basic CloudFormation Template (Guided)

In this step, you write a [CloudFormation template](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-anatomy.html) that creates a VPC with a public subnet and a private subnet, then deploy it as a stack using the AWS CLI.

1. Open CloudShell by choosing the terminal icon in the navigation bar, or use your local terminal with the AWS CLI configured.

2. Create a working directory for your templates:

```bash
mkdir -p ~/lab11
cd ~/lab11
```

3. Create the initial CloudFormation template. This template defines a VPC, two subnets, an internet gateway, and a route table for the public subnet:

```bash
cat > vpc-template.yaml << 'EOF'
AWSTemplateFormatVersion: "2010-09-09"
Description: Lab 11 - VPC with public and private subnets

Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: lab11-vpc

  InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: lab11-igw

  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref VPC
      InternetGatewayId: !Ref InternetGateway

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: 10.0.1.0/24
      AvailabilityZone: us-east-1a
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: lab11-public-subnet

  PrivateSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: 10.0.2.0/24
      AvailabilityZone: us-east-1b
      Tags:
        - Key: Name
          Value: lab11-private-subnet

  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: lab11-public-rt

  PublicRoute:
    Type: AWS::EC2::Route
    DependsOn: AttachGateway
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  PublicSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable
EOF
```

4. Validate the template before deploying. CloudFormation checks the template syntax and reports any errors:

```bash
aws cloudformation validate-template \
  --template-body file://vpc-template.yaml
```

Expected output (abbreviated):

```json
{
    "Parameters": [],
    "Description": "Lab 11 - VPC with public and private subnets"
}
```

5. Create the stack:

```bash
aws cloudformation create-stack \
  --stack-name lab11-network \
  --template-body file://vpc-template.yaml \
  --region us-east-1
```

6. Wait for the stack to finish creating. This command blocks until the stack reaches `CREATE_COMPLETE` or fails:

```bash
aws cloudformation wait stack-create-complete \
  --stack-name lab11-network \
  --region us-east-1
echo "Stack creation complete."
```

7. Verify the stack status and list the resources CloudFormation created:

```bash
aws cloudformation describe-stacks \
  --stack-name lab11-network \
  --query "Stacks[0].StackStatus" \
  --output text
```

Expected output:

```
CREATE_COMPLETE
```

8. List all resources in the stack:

```bash
aws cloudformation list-stack-resources \
  --stack-name lab11-network \
  --query "StackResourceSummaries[*].[LogicalResourceId,ResourceType,ResourceStatus]" \
  --output table
```

You should see eight resources (VPC, InternetGateway, AttachGateway, PublicSubnet, PrivateSubnet, PublicRouteTable, PublicRoute, PublicSubnetRouteTableAssociation) all with status `CREATE_COMPLETE`.

> **Tip:** Compare this experience to [Lab 03](../../03-networking-basics/lab/lab-03-vpc-setup.md) where you created each resource individually with separate CLI commands. CloudFormation created all eight resources from a single template, handled the dependency ordering automatically, and will delete them all as a unit when you delete the stack.

### Step 2: Add Parameters and Update with a Change Set (Guided)

In this step, you add [parameters](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html) to your template so you can reuse it across environments without editing the YAML each time. You then update the running stack using a [change set](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-updating-stacks-changesets.html), which lets you preview exactly what will change before committing.

1. Update the template to add parameters for the environment name and VPC CIDR block. Replace the contents of `vpc-template.yaml`:

```bash
cat > vpc-template.yaml << 'EOF'
AWSTemplateFormatVersion: "2010-09-09"
Description: Lab 11 - Parameterized VPC with public and private subnets

Parameters:
  EnvironmentName:
    Type: String
    Default: dev
    AllowedValues:
      - dev
      - staging
      - prod
    Description: Environment name used for resource tagging

  VpcCidr:
    Type: String
    Default: 10.0.0.0/16
    AllowedPattern: "^(\\d{1,3}\\.){3}\\d{1,3}/\\d{1,2}$"
    ConstraintDescription: Must be a valid CIDR block (for example, 10.0.0.0/16)
    Description: CIDR block for the VPC

Resources:
  VPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: !Ref VpcCidr
      EnableDnsSupport: true
      EnableDnsHostnames: true
      Tags:
        - Key: Name
          Value: !Sub "${EnvironmentName}-lab11-vpc"
        - Key: Environment
          Value: !Ref EnvironmentName

  InternetGateway:
    Type: AWS::EC2::InternetGateway
    Properties:
      Tags:
        - Key: Name
          Value: !Sub "${EnvironmentName}-lab11-igw"

  AttachGateway:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref VPC
      InternetGatewayId: !Ref InternetGateway

  PublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: !Select
        - 0
        - !Cidr [!Ref VpcCidr, 4, 8]
      AvailabilityZone: us-east-1a
      MapPublicIpOnLaunch: true
      Tags:
        - Key: Name
          Value: !Sub "${EnvironmentName}-lab11-public-subnet"

  PrivateSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref VPC
      CidrBlock: !Select
        - 1
        - !Cidr [!Ref VpcCidr, 4, 8]
      AvailabilityZone: us-east-1b
      Tags:
        - Key: Name
          Value: !Sub "${EnvironmentName}-lab11-private-subnet"

  PublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref VPC
      Tags:
        - Key: Name
          Value: !Sub "${EnvironmentName}-lab11-public-rt"

  PublicRoute:
    Type: AWS::EC2::Route
    DependsOn: AttachGateway
    Properties:
      RouteTableId: !Ref PublicRouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId: !Ref InternetGateway

  PublicSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref PublicSubnet
      RouteTableId: !Ref PublicRouteTable
EOF
```

2. Create a change set to preview the update. A change set shows you exactly what CloudFormation will modify before you apply the changes:

```bash
aws cloudformation create-change-set \
  --stack-name lab11-network \
  --change-set-name add-parameters \
  --template-body file://vpc-template.yaml \
  --parameters \
    ParameterKey=EnvironmentName,ParameterValue=dev \
    ParameterKey=VpcCidr,ParameterValue=10.0.0.0/16 \
  --region us-east-1
```

3. Wait for the change set to finish computing:

```bash
aws cloudformation wait change-set-create-complete \
  --stack-name lab11-network \
  --change-set-name add-parameters \
  --region us-east-1
```

4. Review the change set to see what will change:

```bash
aws cloudformation describe-change-set \
  --stack-name lab11-network \
  --change-set-name add-parameters \
  --query "Changes[*].ResourceChange.{Action:Action,LogicalId:LogicalResourceId,ResourceType:ResourceType,Replacement:Replacement}" \
  --output table
```

You should see `Modify` actions on resources where tags changed, and the `Replacement` column should show `False` for tag-only changes. This confirms that updating tags does not replace your resources.

5. Execute the change set to apply the changes:

```bash
aws cloudformation execute-change-set \
  --stack-name lab11-network \
  --change-set-name add-parameters \
  --region us-east-1
```

6. Wait for the update to complete:

```bash
aws cloudformation wait stack-update-complete \
  --stack-name lab11-network \
  --region us-east-1
echo "Stack update complete."
```

7. Verify the stack now has parameters:

```bash
aws cloudformation describe-stacks \
  --stack-name lab11-network \
  --query "Stacks[0].Parameters" \
  --output table
```

You should see `EnvironmentName` with value `dev` and `VpcCidr` with value `10.0.0.0/16`.

> **Tip:** Always use change sets when updating stacks, even in development. This habit prevents surprises in production where an unexpected resource replacement could cause downtime. Review the [CloudFormation best practices](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/best-practices.html) for more recommendations.


### Step 3: Add an EC2 Instance with a Security Group (Semi-Guided)

**Goal:** Add an EC2 instance in the public subnet and a security group that allows inbound SSH (port 22) and HTTP (port 80) traffic. The instance should use the Amazon Linux 2023 AMI and the `t2.micro` instance type.

**Resources to add:**

| Resource Type | Logical Name | Purpose |
|---------------|-------------|---------|
| [`AWS::EC2::SecurityGroup`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-securitygroup.html) | `WebServerSecurityGroup` | Controls inbound traffic to the EC2 instance |
| [`AWS::EC2::Instance`](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-instance.html) | `WebServerInstance` | A t2.micro instance in the public subnet |

**Requirements:**

- The security group must belong to the lab11 VPC (use `!Ref VPC` for the `VpcId` property)
- Add two inbound rules using `SecurityGroupIngress`: one for SSH (port 22, TCP, `0.0.0.0/0`) and one for HTTP (port 80, TCP, `0.0.0.0/0`)
- Tag the security group with the environment name using `!Sub`
- The EC2 instance must use the AMI ID `ami-0953476d60561c955` (Amazon Linux 2023 in us-east-1)
- Place the instance in the public subnet using the `SubnetId` property with `!Ref PublicSubnet`
- Attach the security group using the `SecurityGroupIds` property (this takes a list)
- Tag the instance with the environment name

> **Hint:** The `SecurityGroupIds` property on `AWS::EC2::Instance` expects a list of security group IDs. Use YAML list syntax:
> ```yaml
> SecurityGroupIds:
>   - !GetAtt WebServerSecurityGroup.GroupId
> ```

> **Hint:** Use `!Sub "${EnvironmentName}-lab11-web-server"` for the instance Name tag to keep naming consistent with the rest of the template.

> **Hint:** Review the [AWS::EC2::SecurityGroup resource reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-securitygroup.html) for the `SecurityGroupIngress` property structure. Each rule needs `IpProtocol`, `FromPort`, `ToPort`, and `CidrIp`.

**Update the stack** after adding the resources. Use a change set to preview the additions before applying:

```bash
aws cloudformation create-change-set \
  --stack-name lab11-network \
  --change-set-name add-ec2 \
  --template-body file://vpc-template.yaml \
  --parameters \
    ParameterKey=EnvironmentName,ParameterValue=dev \
    ParameterKey=VpcCidr,ParameterValue=10.0.0.0/16 \
  --region us-east-1

aws cloudformation wait change-set-create-complete \
  --stack-name lab11-network \
  --change-set-name add-ec2 \
  --region us-east-1

aws cloudformation describe-change-set \
  --stack-name lab11-network \
  --change-set-name add-ec2 \
  --query "Changes[*].ResourceChange.{Action:Action,LogicalId:LogicalResourceId,ResourceType:ResourceType}" \
  --output table
```

You should see two `Add` actions: one for `WebServerSecurityGroup` and one for `WebServerInstance`.

Execute the change set:

```bash
aws cloudformation execute-change-set \
  --stack-name lab11-network \
  --change-set-name add-ec2 \
  --region us-east-1

aws cloudformation wait stack-update-complete \
  --stack-name lab11-network \
  --region us-east-1
echo "EC2 instance added."
```

**Verify your work** by confirming the instance is running:

```bash
INSTANCE_ID=$(aws cloudformation describe-stack-resource \
  --stack-name lab11-network \
  --logical-resource-id WebServerInstance \
  --query "StackResourceDetail.PhysicalResourceId" \
  --output text)

aws ec2 describe-instances \
  --instance-ids $INSTANCE_ID \
  --query "Reservations[0].Instances[0].{State:State.Name,PublicIp:PublicIpAddress,SubnetId:SubnetId}" \
  --output table
```

You should see the instance in the `running` state with a public IP address.

**Reference links:**
- [AWS::EC2::SecurityGroup](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-securitygroup.html)
- [AWS::EC2::Instance](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-ec2-instance.html)
- [Intrinsic function reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/TemplateReference/intrinsic-function-reference.html)

### Step 4: Add Outputs for Cross-Stack Reference (Guided)

In this step, you add [outputs](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html) to your template that expose the VPC ID and subnet IDs for consumption by other stacks. This pattern, where a "network stack" exports identifiers and an "application stack" imports them via `Fn::ImportValue`, is how teams share infrastructure across independently managed stacks.

1. Add the following `Outputs` section to the bottom of your `vpc-template.yaml` file (after the `Resources` section):

```yaml
Outputs:
  VpcId:
    Description: VPC ID for cross-stack reference
    Value: !Ref VPC
    Export:
      Name: !Sub "${EnvironmentName}-lab11-vpc-id"

  PublicSubnetId:
    Description: Public subnet ID for cross-stack reference
    Value: !Ref PublicSubnet
    Export:
      Name: !Sub "${EnvironmentName}-lab11-public-subnet-id"

  PrivateSubnetId:
    Description: Private subnet ID for cross-stack reference
    Value: !Ref PrivateSubnet
    Export:
      Name: !Sub "${EnvironmentName}-lab11-private-subnet-id"

  WebServerPublicIp:
    Description: Public IP address of the web server
    Value: !GetAtt WebServerInstance.PublicIp
```

2. Update the stack with a change set:

```bash
aws cloudformation create-change-set \
  --stack-name lab11-network \
  --change-set-name add-outputs \
  --template-body file://vpc-template.yaml \
  --parameters \
    ParameterKey=EnvironmentName,ParameterValue=dev \
    ParameterKey=VpcCidr,ParameterValue=10.0.0.0/16 \
  --region us-east-1

aws cloudformation wait change-set-create-complete \
  --stack-name lab11-network \
  --change-set-name add-outputs \
  --region us-east-1
```

3. Review the change set. Adding outputs does not modify any resources, so you may see an empty changes list or only output-related changes:

```bash
aws cloudformation describe-change-set \
  --stack-name lab11-network \
  --change-set-name add-outputs \
  --query "Changes" \
  --output json
```

4. Execute the change set:

```bash
aws cloudformation execute-change-set \
  --stack-name lab11-network \
  --change-set-name add-outputs \
  --region us-east-1

aws cloudformation wait stack-update-complete \
  --stack-name lab11-network \
  --region us-east-1
echo "Outputs added."
```

5. View the stack outputs:

```bash
aws cloudformation describe-stacks \
  --stack-name lab11-network \
  --query "Stacks[0].Outputs" \
  --output table
```

You should see four outputs: `VpcId`, `PublicSubnetId`, `PrivateSubnetId`, and `WebServerPublicIp`.

6. Verify the exported values are available for cross-stack reference:

```bash
aws cloudformation list-exports \
  --query "Exports[?starts_with(Name, 'dev-lab11')]" \
  --output table
```

You should see three exports: `dev-lab11-vpc-id`, `dev-lab11-public-subnet-id`, and `dev-lab11-private-subnet-id`. Any other stack in the same Region and account can now import these values using `Fn::ImportValue`.

> **Tip:** The `WebServerPublicIp` output does not have an `Export` block because IP addresses change when instances are stopped and started. Export only stable identifiers like VPC IDs and subnet IDs. For the cross-stack reference pattern in production, see the [CloudFormation outputs documentation](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html).

### Step 5: Deploy a Serverless API with SAM (Semi-Guided)

**Goal:** Use the [AWS Serverless Application Model (SAM)](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/what-is-sam-overview.html) to define and deploy a serverless API consisting of a Lambda function, an API Gateway endpoint, and a DynamoDB table. The API should accept GET requests at `/items` and return a list of items from the DynamoDB table.

**SAM resource types to use:**

| SAM Resource Type | Logical Name | Purpose |
|-------------------|-------------|---------|
| [`AWS::Serverless::Function`](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-function.html) | `GetItemsFunction` | Lambda function that reads from DynamoDB |
| [`AWS::Serverless::SimpleTable`](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-simpletable.html) | `ItemsTable` | DynamoDB table with a single-attribute primary key |

**Requirements:**

- Create a new directory for the SAM project: `~/lab11/sam-api/`
- Write a SAM template file at `~/lab11/sam-api/template.yaml`
- The template must include `Transform: AWS::Serverless-2016-10-31`
- Use a `Globals` section to set the function runtime to `python3.12`, timeout to `30`, and an environment variable `TABLE_NAME` that references the DynamoDB table
- The `GetItemsFunction` must have:
  - A `Handler` property pointing to your Python handler (for example, `app.handler`)
  - A `CodeUri` property pointing to a `src/` directory
  - An `Events` section with an `Api` event type for `GET /items`
  - A `Policies` section using the `DynamoDBReadPolicy` [SAM policy template](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-policy-templates.html) scoped to the items table
- The `ItemsTable` must have a primary key named `ItemId` of type `String`
- Include an `Outputs` section that exports the API endpoint URL

- Write the Lambda handler at `~/lab11/sam-api/src/app.py`:
  - Read the `TABLE_NAME` environment variable
  - Use `boto3` to scan the DynamoDB table
  - Return a JSON response with `statusCode`, `headers` (including `Content-Type: application/json`), and `body`

> **Hint:** The SAM template `Transform` declaration tells CloudFormation to process SAM-specific resource types. Your template should start with:
> ```yaml
> AWSTemplateFormatVersion: "2010-09-09"
> Transform: AWS::Serverless-2016-10-31
> Description: Lab 11 - SAM serverless API
> ```

> **Hint:** The `Events` section on a `AWS::Serverless::Function` defines what triggers the function. For an API Gateway trigger:
> ```yaml
> Events:
>   GetItems:
>     Type: Api
>     Properties:
>       Path: /items
>       Method: get
> ```

> **Hint:** SAM policy templates simplify IAM permissions. `DynamoDBReadPolicy` grants `GetItem`, `Query`, `Scan`, and `BatchGetItem` on the specified table. Reference it as:
> ```yaml
> Policies:
>   - DynamoDBReadPolicy:
>       TableName: !Ref ItemsTable
> ```

> **Hint:** For the API URL output, SAM automatically creates a `ServerlessRestApi` resource. Reference it with:
> ```yaml
> Outputs:
>   ApiUrl:
>     Description: API Gateway endpoint URL
>     Value: !Sub "https://${ServerlessRestApi}.execute-api.${AWS::Region}.amazonaws.com/Prod"
> ```

**Create the project structure:**

```bash
mkdir -p ~/lab11/sam-api/src
```

Write your `template.yaml` and `src/app.py` files, then proceed to Step 6 to build, test, and deploy.

**Reference links:**
- [SAM template anatomy](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy.html)
- [AWS::Serverless::Function](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-function.html)
- [AWS::Serverless::SimpleTable](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-resource-simpletable.html)
- [SAM policy templates](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-policy-templates.html)
- [SAM Globals section](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/sam-specification-template-anatomy-globals.html)

### Step 6: Test Locally and Deploy the SAM Application (Guided)

In this step, you build the SAM application, test the Lambda function locally using `sam local invoke`, and deploy to AWS using `sam deploy --guided`. This workflow mirrors what a CI/CD pipeline does automatically (Module 12), but here you run each step manually to understand what happens at each stage.

1. Navigate to the SAM project directory:

```bash
cd ~/lab11/sam-api
```

2. Build the application. The `sam build` command installs dependencies and prepares deployment artifacts:

```bash
sam build
```

Expected output (abbreviated):

```
Building codeuri: /home/cloudshell-user/lab11/sam-api/src runtime: python3.12 ...
Build Succeeded
```

3. Create a test event file to simulate an API Gateway GET request:

```bash
mkdir -p events
cat > events/get-items.json << 'EOF'
{
  "httpMethod": "GET",
  "path": "/items",
  "pathParameters": null,
  "queryStringParameters": null,
  "headers": {
    "Content-Type": "application/json"
  },
  "body": null,
  "requestContext": {
    "httpMethod": "GET",
    "path": "/items"
  }
}
EOF
```

4. Test the function locally with `sam local invoke`. This command runs the function in a Docker container that simulates the Lambda execution environment:

```bash
sam local invoke GetItemsFunction --event events/get-items.json
```

> **Tip:** The first invocation downloads the Lambda runtime Docker image, which may take a minute. Subsequent invocations reuse the cached image and run faster. If you see a connection error for DynamoDB, that is expected because the local environment does not have access to your AWS DynamoDB table. The function will work correctly after deployment.

5. Deploy the application to AWS using the guided deployment flow:

```bash
sam deploy --guided
```

6. When prompted, enter the following configuration values:

| Prompt | Value |
|--------|-------|
| Stack Name | `lab11-sam-api` |
| AWS Region | `us-east-1` |
| Confirm changes before deploy | `y` |
| Allow SAM CLI IAM role creation | `y` |
| Disable rollback | `n` |
| GetItemsFunction may not have authorization defined, Is this okay? | `y` |
| Save arguments to configuration file | `y` |
| SAM configuration file | `samconfig.toml` |
| SAM configuration environment | `default` |

7. SAM displays a change set summary showing the resources it will create. Review the list and confirm the deployment by entering `y` when prompted.

8. Wait for the deployment to complete. SAM displays the stack outputs when finished. Look for the `ApiUrl` output value.

9. Test the deployed API:

```bash
API_URL=$(aws cloudformation describe-stacks \
  --stack-name lab11-sam-api \
  --query "Stacks[0].Outputs[?OutputKey=='ApiUrl'].OutputValue" \
  --output text)
echo "API URL: $API_URL"

curl "${API_URL}/items"
```

You should see a JSON response with an empty items list (or any items you have added to the table). The response confirms that API Gateway, Lambda, and DynamoDB are all working together.

10. Add a test item to the DynamoDB table to verify the full read path:

```bash
TABLE_NAME=$(aws cloudformation describe-stack-resource \
  --stack-name lab11-sam-api \
  --logical-resource-id ItemsTable \
  --query "StackResourceDetail.PhysicalResourceId" \
  --output text)

aws dynamodb put-item \
  --table-name $TABLE_NAME \
  --item '{"ItemId": {"S": "item-001"}, "Name": {"S": "Test Item"}, "Description": {"S": "Created in Lab 11"}}' \
  --region us-east-1
```

11. Query the API again to confirm the item appears:

```bash
curl "${API_URL}/items"
```

You should see a JSON response containing the item you just added.

> **Tip:** The `samconfig.toml` file that SAM created stores your deployment configuration. Future deployments use `sam deploy` without the `--guided` flag, and SAM reads the saved settings automatically. This is useful in CI/CD pipelines where you want repeatable, non-interactive deployments.

### Step 7: Delete Both Stacks and Verify Cleanup (Guided)

Delete all resources created during this lab to avoid unexpected charges. You must delete the SAM stack first because it is independent, then delete the network stack.

1. Delete the SAM application stack:

```bash
aws cloudformation delete-stack \
  --stack-name lab11-sam-api \
  --region us-east-1

aws cloudformation wait stack-delete-complete \
  --stack-name lab11-sam-api \
  --region us-east-1
echo "SAM stack deleted."
```

2. Delete the network stack. CloudFormation will terminate the EC2 instance, delete the security group, subnets, route tables, internet gateway, and VPC in the correct order:

```bash
aws cloudformation delete-stack \
  --stack-name lab11-network \
  --region us-east-1

aws cloudformation wait stack-delete-complete \
  --stack-name lab11-network \
  --region us-east-1
echo "Network stack deleted."
```

3. Verify that both stacks are deleted:

```bash
aws cloudformation list-stacks \
  --stack-status-filter DELETE_COMPLETE \
  --query "StackSummaries[?contains(StackName, 'lab11')].[StackName,StackStatus,DeletionTime]" \
  --output table
```

You should see both `lab11-network` and `lab11-sam-api` with status `DELETE_COMPLETE`.

4. Confirm that no lab11 resources remain active:

```bash
aws ec2 describe-vpcs \
  --filters "Name=tag:Name,Values=*lab11*" \
  --query "Vpcs[*].[VpcId,Tags[?Key=='Name'].Value|[0]]" \
  --output table
```

This command should return an empty table, confirming the VPC and all associated resources are gone.

5. Delete the CloudWatch Log groups created by the SAM Lambda function:

```bash
aws logs describe-log-groups \
  --log-group-name-prefix "/aws/lambda/lab11-sam-api" \
  --query "logGroups[*].logGroupName" \
  --output text | tr '\t' '\n' | while read LOG_GROUP; do
  aws logs delete-log-group --log-group-name "$LOG_GROUP"
  echo "Deleted log group: $LOG_GROUP"
done
```

6. Clean up the local working directory:

```bash
rm -rf ~/lab11
```

> **Warning:** If you skip the cleanup steps, the EC2 instance in the network stack will incur hourly charges, and the DynamoDB table in the SAM stack may incur charges for stored data. Always delete lab stacks when you are finished.

## Validation

Confirm that you completed the lab successfully by verifying each of the following:

- [ ] The `lab11-network` CloudFormation stack was created with a VPC, public subnet, private subnet, internet gateway, and route table
- [ ] The template uses `Parameters` for `EnvironmentName` and `VpcCidr` with validation constraints
- [ ] A change set was created and reviewed before each stack update
- [ ] The template includes an `AWS::EC2::SecurityGroup` with SSH and HTTP inbound rules
- [ ] The template includes an `AWS::EC2::Instance` in the public subnet with the security group attached
- [ ] The stack `Outputs` section exports the VPC ID, public subnet ID, and private subnet ID
- [ ] The exported values appear in `aws cloudformation list-exports`
- [ ] A SAM template defines a `AWS::Serverless::Function`, `AWS::Serverless::SimpleTable`, and API event source
- [ ] The SAM template uses a `Globals` section for shared function configuration
- [ ] `sam local invoke` executed the function in a local Docker container
- [ ] `sam deploy --guided` created the `lab11-sam-api` stack with API Gateway, Lambda, and DynamoDB
- [ ] `curl` to the API Gateway endpoint returned a JSON response with items from DynamoDB
- [ ] Both stacks were deleted and `aws cloudformation list-stacks` confirms `DELETE_COMPLETE` status

## Cleanup

See Step 7 above for the complete cleanup procedure. If you skipped Step 7, return to it now and execute all cleanup commands to avoid unexpected charges.

## Challenge

Extend your infrastructure as code skills with the following enhancements:

1. **Add a NAT Gateway to the network stack.** Modify `vpc-template.yaml` to add an Elastic IP, a NAT Gateway in the public subnet, and a private route table with a default route through the NAT Gateway. Associate the private subnet with the new route table. Use a change set to preview and apply the update. Verify that the private subnet route table has a route to the NAT Gateway by running `aws ec2 describe-route-tables`.

2. **Add a POST endpoint to the SAM API.** Create a second Lambda function in the SAM template that accepts POST requests at `/items` and writes a new item to the DynamoDB table. Use the `DynamoDBCrudPolicy` SAM policy template instead of `DynamoDBReadPolicy`. Redeploy with `sam deploy` and test with `curl -X POST`.

3. **Use cross-stack references.** Create a third CloudFormation template that imports the VPC ID and public subnet ID exported by the network stack using `Fn::ImportValue`. Use the imported values to launch a second EC2 instance in the same VPC. This demonstrates how teams can share infrastructure across independently managed stacks.
