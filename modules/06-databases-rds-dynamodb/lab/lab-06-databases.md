# Lab 06: Working with Amazon RDS and DynamoDB

## Objective

Create an Amazon RDS PostgreSQL database instance and an Amazon DynamoDB table, connect to each database, insert sample data, and compare relational SQL queries with DynamoDB Query and Scan operations.

## Architecture Diagram

This lab builds two separate database environments: a managed relational database using Amazon RDS and a NoSQL table using Amazon DynamoDB.

```
Student's browser / CloudShell
    |
    v
AWS Management Console (us-east-1)
    |
    ├── Default VPC
    |       |
    |       ├── DB Subnet Group: lab06-db-subnet-group
    |       |       └── Subnets in at least 2 Availability Zones
    |       |
    |       ├── Security Group: lab06-rds-sg
    |       |       └── Inbound: TCP 5432 from CloudShell/EC2 source
    |       |
    |       └── RDS PostgreSQL Instance: lab06-postgres
    |               ├── Engine: PostgreSQL
    |               ├── Instance class: db.t3.micro (Free Tier)
    |               ├── Storage: 20 GiB gp2
    |               └── Single-AZ deployment
    |
    └── DynamoDB
            └── Table: lab06-orders
                    ├── Partition key: CustomerId (String)
                    ├── Sort key: OrderId (String)
                    ├── Billing mode: PAY_PER_REQUEST (on-demand)
                    └── Sample items (inserted via CLI)
```

You will use the default VPC for the RDS instance to keep the setup simple. For DynamoDB, no VPC configuration is needed because DynamoDB is a regional service accessed through AWS API endpoints.

## Prerequisites

- Completed [Lab 01: AWS Account Setup and Console Tour](../../01-cloud-fundamentals/lab/lab-01-aws-account-setup.md)
- Completed [Module 03: Networking Basics (VPC)](../../03-networking-basics/README.md) (understanding of VPCs, subnets, and security groups)
- Completed [Module 06: Databases with Amazon RDS and DynamoDB](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI installed and configured locally)

## Duration

75 minutes

## Instructions

### Step 1: Identify Default VPC Subnets

Before creating the RDS instance, you need to identify the subnets in your [default VPC](https://docs.aws.amazon.com/vpc/latest/userguide/default-vpc.html). RDS requires a DB subnet group with subnets in at least two Availability Zones.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/) as `bootcamp-admin`.
2. Verify that the Region selector in the top-right corner displays **US East (N. Virginia) us-east-1**.
3. Open CloudShell by choosing the terminal icon in the navigation bar.
4. Run the following command to find your default VPC ID:

```bash
DEFAULT_VPC_ID=$(aws ec2 describe-vpcs \
    --filters "Name=isDefault,Values=true" \
    --query "Vpcs[0].VpcId" \
    --output text \
    --region us-east-1)
echo "Default VPC: $DEFAULT_VPC_ID"
```

Expected output:

```
Default VPC: vpc-0abc1234def567890
```

5. List the subnets in the default VPC:

```bash
aws ec2 describe-subnets \
    --filters "Name=vpc-id,Values=$DEFAULT_VPC_ID" \
    --query "Subnets[].{SubnetId:SubnetId,AZ:AvailabilityZone,CidrBlock:CidrBlock}" \
    --output table \
    --region us-east-1
```

Expected output (your subnet IDs and CIDR blocks will differ):

```
--------------------------------------------------------------
|                       DescribeSubnets                      |
+------------+------------------+----------------------------+
|     AZ     |    CidrBlock     |         SubnetId           |
+------------+------------------+----------------------------+
|  us-east-1a|  172.31.0.0/20   |  subnet-0aaa111bbb222ccc3 |
|  us-east-1b|  172.31.16.0/20  |  subnet-0ddd444eee555fff6 |
|  us-east-1c|  172.31.32.0/20  |  subnet-0ggg777hhh888iii9 |
+------------+------------------+----------------------------+
```

6. Store the subnet IDs for use in the next step. You need at least two subnets in different Availability Zones:

```bash
SUBNET_IDS=$(aws ec2 describe-subnets \
    --filters "Name=vpc-id,Values=$DEFAULT_VPC_ID" \
    --query "Subnets[].SubnetId" \
    --output text \
    --region us-east-1)
echo "Subnets: $SUBNET_IDS"
```

**Expected result:** You have identified your default VPC ID and at least two subnet IDs in different Availability Zones.

### Step 2: Create a DB Subnet Group

A [DB subnet group](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_VPC.WorkingWithRDSInstanceinaVPC.html) tells RDS which subnets it can use to place your database instance. The group must contain subnets in at least two Availability Zones so that RDS can support Multi-AZ deployments (even though this lab uses Single-AZ).

1. In CloudShell, create the DB subnet group using all default VPC subnets:

```bash
aws rds create-db-subnet-group \
    --db-subnet-group-name lab06-db-subnet-group \
    --db-subnet-group-description "Subnet group for Lab 06 RDS instance" \
    --subnet-ids $SUBNET_IDS \
    --region us-east-1
```

Expected output (partial):

```json
{
    "DBSubnetGroup": {
        "DBSubnetGroupName": "lab06-db-subnet-group",
        "DBSubnetGroupDescription": "Subnet group for Lab 06 RDS instance",
        "VpcId": "vpc-0abc1234def567890",
        "SubnetGroupStatus": "Complete",
        "Subnets": [
            {
                "SubnetIdentifier": "subnet-0aaa111bbb222ccc3",
                "SubnetAvailabilityZone": {
                    "Name": "us-east-1a"
                },
                "SubnetStatus": "Active"
            },
            {
                "SubnetIdentifier": "subnet-0ddd444eee555fff6",
                "SubnetAvailabilityZone": {
                    "Name": "us-east-1b"
                },
                "SubnetStatus": "Active"
            }
        ]
    }
}
```

2. Verify the subnet group was created:

```bash
aws rds describe-db-subnet-groups \
    --db-subnet-group-name lab06-db-subnet-group \
    --query "DBSubnetGroups[0].{Name:DBSubnetGroupName,Status:SubnetGroupStatus,VpcId:VpcId}" \
    --output table \
    --region us-east-1
```

Expected output:

```
-----------------------------------------------------------------
|                    DescribeDBSubnetGroups                      |
+----------------------------+----------+------------------------+
|           Name             | Status   |         VpcId          |
+----------------------------+----------+------------------------+
|  lab06-db-subnet-group     | Complete |  vpc-0abc1234def567890 |
+----------------------------+----------+------------------------+
```

**Expected result:** The DB subnet group `lab06-db-subnet-group` exists with status `Complete` and includes subnets in at least two Availability Zones.

### Step 3: Create a Security Group for the RDS Instance

A [security group](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html) acts as a virtual firewall that controls inbound and outbound traffic to your RDS instance. You will create a security group that allows PostgreSQL connections (TCP port 5432) from your CloudShell environment.

1. Create the security group in the default VPC:

```bash
RDS_SG_ID=$(aws ec2 create-security-group \
    --group-name lab06-rds-sg \
    --description "Allow PostgreSQL access for Lab 06" \
    --vpc-id $DEFAULT_VPC_ID \
    --query "GroupId" \
    --output text \
    --region us-east-1)
echo "Security Group ID: $RDS_SG_ID"
```

Expected output:

```
Security Group ID: sg-0abc123def456789a
```

2. Add an inbound rule to allow PostgreSQL traffic on port 5432. For this lab, you will allow access from the default VPC CIDR block so that CloudShell and any EC2 instances in the VPC can connect:

```bash
VPC_CIDR=$(aws ec2 describe-vpcs \
    --vpc-ids $DEFAULT_VPC_ID \
    --query "Vpcs[0].CidrBlock" \
    --output text \
    --region us-east-1)
echo "VPC CIDR: $VPC_CIDR"

aws ec2 authorize-security-group-ingress \
    --group-id $RDS_SG_ID \
    --protocol tcp \
    --port 5432 \
    --cidr $VPC_CIDR \
    --region us-east-1
```

Expected output:

```json
{
    "Return": true,
    "SecurityGroupRules": [
        {
            "SecurityGroupRuleId": "sgr-0abc123def456789b",
            "GroupId": "sg-0abc123def456789a",
            "IpProtocol": "tcp",
            "FromPort": 5432,
            "ToPort": 5432,
            "CidrIpv4": "172.31.0.0/16"
        }
    ]
}
```

3. Verify the security group rules:

```bash
aws ec2 describe-security-groups \
    --group-ids $RDS_SG_ID \
    --query "SecurityGroups[0].IpPermissions" \
    --output json \
    --region us-east-1
```

Expected output:

```json
[
    {
        "FromPort": 5432,
        "IpProtocol": "tcp",
        "IpRanges": [
            {
                "CidrIp": "172.31.0.0/16"
            }
        ],
        "ToPort": 5432
    }
]
```

**Expected result:** Security group `lab06-rds-sg` exists with an inbound rule allowing TCP port 5432 from the default VPC CIDR block.

> **Tip:** In a production environment, you would restrict the source to a specific application security group rather than the entire VPC CIDR. For this lab, using the VPC CIDR keeps the setup straightforward while still limiting access to resources within the VPC.

### Step 4: Launch an RDS PostgreSQL Instance

In this step, you create an [RDS DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.DBInstance.html) running PostgreSQL. You will use the `db.t3.micro` instance class, which is eligible for the [AWS Free Tier](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html) (750 hours per month for 12 months).

1. Create the RDS instance. Replace `YourSecurePassword123` with a strong password of your choice (at least 8 characters, including uppercase, lowercase, and numbers):

```bash
aws rds create-db-instance \
    --db-instance-identifier lab06-postgres \
    --db-instance-class db.t3.micro \
    --engine postgres \
    --master-username labadmin \
    --master-user-password YourSecurePassword123 \
    --allocated-storage 20 \
    --db-subnet-group-name lab06-db-subnet-group \
    --vpc-security-group-ids $RDS_SG_ID \
    --no-multi-az \
    --backup-retention-period 1 \
    --no-publicly-accessible \
    --storage-type gp2 \
    --region us-east-1
```

Expected output (partial):

```json
{
    "DBInstance": {
        "DBInstanceIdentifier": "lab06-postgres",
        "DBInstanceClass": "db.t3.micro",
        "Engine": "postgres",
        "DBInstanceStatus": "creating",
        "MasterUsername": "labadmin",
        "AllocatedStorage": 20,
        "MultiAZ": false,
        "StorageType": "gp2"
    }
}
```

> **Warning:** The RDS instance takes 5 to 10 minutes to become available. You can proceed to Steps 5 through 8 (DynamoDB) while waiting, then return to this step to connect.

2. Wait for the instance to become available. Run the following command periodically (every 60 seconds or so) to check the status:

```bash
aws rds describe-db-instances \
    --db-instance-identifier lab06-postgres \
    --query "DBInstances[0].DBInstanceStatus" \
    --output text \
    --region us-east-1
```

The status progresses through `creating`, `backing-up`, and finally `available`.

Alternatively, use the `wait` command to block until the instance is ready:

```bash
aws rds wait db-instance-available \
    --db-instance-identifier lab06-postgres \
    --region us-east-1
```

This command returns silently when the instance is available.

3. Once the status is `available`, retrieve the endpoint address:

```bash
RDS_ENDPOINT=$(aws rds describe-db-instances \
    --db-instance-identifier lab06-postgres \
    --query "DBInstances[0].Endpoint.Address" \
    --output text \
    --region us-east-1)
echo "RDS Endpoint: $RDS_ENDPOINT"
```

Expected output:

```
RDS Endpoint: lab06-postgres.abc123xyz789.us-east-1.rds.amazonaws.com
```

4. Store the endpoint for use in the next step:

```bash
echo "Endpoint: $RDS_ENDPOINT"
echo "Username: labadmin"
echo "Port: 5432"
```

**Expected result:** The RDS instance `lab06-postgres` is in the `available` state and you have its endpoint address.

> **Tip:** The `db.t3.micro` instance class provides 2 vCPUs and 1 GiB of memory. It uses a burstable performance model, which is suitable for development, testing, and small production workloads with variable CPU usage. For this lab, it provides more than enough capacity.


### Step 5: Connect to the RDS Instance and Create Sample Data

In this step, you connect to the PostgreSQL database from CloudShell using the `psql` command-line client, create a sample table, insert data, and run queries.

> **Tip:** If you started the DynamoDB steps (Steps 5 through 8) while waiting for RDS, return here once the RDS instance status is `available`.

1. Install the PostgreSQL client in CloudShell. CloudShell does not include `psql` by default:

```bash
sudo yum install -y postgresql15
```

Expected output (partial):

```
Installed:
  postgresql15.x86_64
Complete!
```

> **Tip:** If `postgresql15` is not available, try `postgresql` or `postgresql14` instead. The exact package name depends on the CloudShell Amazon Linux version.

2. Connect to the RDS instance using `psql`. Replace `YourSecurePassword123` with the password you set in Step 4:

```bash
psql -h $RDS_ENDPOINT -U labadmin -d postgres
```

When prompted, enter the password you specified during instance creation.

**Expected result:** You see the PostgreSQL interactive terminal prompt:

```
postgres=>
```

3. Create a sample database and switch to it:

```sql
CREATE DATABASE lab06db;
\c lab06db
```

Expected output:

```
CREATE DATABASE
You are now connected to database "lab06db" as user "labadmin".
```

4. Create a table to store product information:

```sql
CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    product_name VARCHAR(100) NOT NULL,
    category VARCHAR(50) NOT NULL,
    price NUMERIC(10, 2) NOT NULL,
    stock_quantity INTEGER DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

Expected output:

```
CREATE TABLE
```

5. Insert sample data into the table:

```sql
INSERT INTO products (product_name, category, price, stock_quantity) VALUES
    ('Wireless Mouse', 'Electronics', 29.99, 150),
    ('USB-C Hub', 'Electronics', 49.99, 75),
    ('Mechanical Keyboard', 'Electronics', 89.99, 50),
    ('Notebook 200-Pack', 'Office Supplies', 24.99, 300),
    ('Standing Desk Mat', 'Office Supplies', 39.99, 120),
    ('LED Desk Lamp', 'Lighting', 34.99, 200),
    ('Monitor Stand', 'Accessories', 59.99, 80);
```

Expected output:

```
INSERT 0 7
```

6. Query all products:

```sql
SELECT product_id, product_name, category, price FROM products ORDER BY category, price;
```

Expected output:

```
 product_id |    product_name     |    category     | price
------------+---------------------+-----------------+-------
          4 | Monitor Stand       | Accessories     | 59.99
          1 | Wireless Mouse      | Electronics     | 29.99
          2 | USB-C Hub           | Electronics     | 49.99
          3 | Mechanical Keyboard | Electronics     | 89.99
          6 | LED Desk Lamp       | Lighting        | 34.99
          5 | Notebook 200-Pack   | Office Supplies | 24.99
          7 | Standing Desk Mat   | Office Supplies | 39.99
(7 rows)
```

7. Run a filtered query to find products in the Electronics category priced under $50:

```sql
SELECT product_name, price, stock_quantity
FROM products
WHERE category = 'Electronics' AND price < 50.00
ORDER BY price;
```

Expected output:

```
  product_name  | price | stock_quantity
----------------+-------+---------------
 Wireless Mouse | 29.99 |           150
 USB-C Hub      | 49.99 |            75
(2 rows)
```

8. Run an aggregation query to see the total stock value per category:

```sql
SELECT category,
       COUNT(*) AS product_count,
       SUM(price * stock_quantity) AS total_stock_value
FROM products
GROUP BY category
ORDER BY total_stock_value DESC;
```

Expected output:

```
    category     | product_count | total_stock_value
-----------------+---------------+-------------------
 Office Supplies |             2 |          12298.80
 Electronics     |             3 |          12123.75
 Lighting        |             1 |           6998.00
 Accessories     |             1 |           4799.20
(4 rows)
```

9. Exit the `psql` session:

```sql
\q
```

**Expected result:** You created a database, a table with seven rows, and ran SELECT queries including filtering and aggregation. This demonstrates the relational query capabilities of Amazon RDS with PostgreSQL.

> **Tip:** In a production application, you would connect to RDS from an EC2 instance or Lambda function within the same VPC, not from CloudShell. CloudShell runs in an AWS-managed VPC that has connectivity to your default VPC subnets, which is why this connection works for lab purposes.

### Step 6: Create a DynamoDB Table with a Composite Key

Now you will create an [Amazon DynamoDB](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Introduction.html) table. Unlike RDS, DynamoDB does not require VPC configuration, subnet groups, or security groups. You create a table, define its key schema, and start writing data immediately.

In this step, you create an `lab06-orders` table with a composite primary key: `CustomerId` as the partition key and `OrderId` as the sort key. This design allows you to store multiple orders per customer and efficiently query all orders for a specific customer.

1. In CloudShell, create the DynamoDB table:

```bash
aws dynamodb create-table \
    --table-name lab06-orders \
    --attribute-definitions \
        AttributeName=CustomerId,AttributeType=S \
        AttributeName=OrderId,AttributeType=S \
    --key-schema \
        AttributeName=CustomerId,KeyType=HASH \
        AttributeName=OrderId,KeyType=RANGE \
    --billing-mode PAY_PER_REQUEST \
    --region us-east-1
```

Expected output (partial):

```json
{
    "TableDescription": {
        "TableName": "lab06-orders",
        "TableStatus": "CREATING",
        "KeySchema": [
            {
                "AttributeName": "CustomerId",
                "KeyType": "HASH"
            },
            {
                "AttributeName": "OrderId",
                "KeyType": "RANGE"
            }
        ],
        "BillingModeSummary": {
            "BillingMode": "PAY_PER_REQUEST"
        }
    }
}
```

2. Wait for the table to become active:

```bash
aws dynamodb wait table-exists \
    --table-name lab06-orders \
    --region us-east-1
```

This command returns silently when the table is active. DynamoDB tables typically become active within a few seconds.

3. Verify the table status:

```bash
aws dynamodb describe-table \
    --table-name lab06-orders \
    --query "Table.{Name:TableName,Status:TableStatus,KeySchema:KeySchema,BillingMode:BillingModeSummary.BillingMode}" \
    --output json \
    --region us-east-1
```

Expected output:

```json
{
    "Name": "lab06-orders",
    "Status": "ACTIVE",
    "KeySchema": [
        {
            "AttributeName": "CustomerId",
            "KeyType": "HASH"
        },
        {
            "AttributeName": "OrderId",
            "KeyType": "RANGE"
        }
    ],
    "BillingMode": "PAY_PER_REQUEST"
}
```

**Expected result:** The DynamoDB table `lab06-orders` is in the `ACTIVE` state with a composite key of `CustomerId` (partition key) and `OrderId` (sort key), using on-demand billing.

> **Tip:** On-demand mode (PAY_PER_REQUEST) is ideal for this lab because you pay only for the reads and writes you perform. There is no minimum capacity to provision and no charge when the table is idle.

### Step 7: Insert Items into the DynamoDB Table

In this step, you insert several items into the `lab06-orders` table using the AWS CLI. Each item represents a customer order. Notice that different items can have different attributes beyond the required key attributes. This is the flexible schema that DynamoDB provides.

1. Insert the first batch of items for customer `CUST-001`:

```bash
aws dynamodb put-item \
    --table-name lab06-orders \
    --item '{
        "CustomerId": {"S": "CUST-001"},
        "OrderId": {"S": "ORD-2024-001"},
        "Product": {"S": "Wireless Mouse"},
        "Quantity": {"N": "2"},
        "TotalPrice": {"N": "59.98"},
        "OrderDate": {"S": "2024-01-15"},
        "Status": {"S": "Delivered"}
    }' \
    --region us-east-1

aws dynamodb put-item \
    --table-name lab06-orders \
    --item '{
        "CustomerId": {"S": "CUST-001"},
        "OrderId": {"S": "ORD-2024-005"},
        "Product": {"S": "USB-C Hub"},
        "Quantity": {"N": "1"},
        "TotalPrice": {"N": "49.99"},
        "OrderDate": {"S": "2024-03-20"},
        "Status": {"S": "Shipped"}
    }' \
    --region us-east-1

aws dynamodb put-item \
    --table-name lab06-orders \
    --item '{
        "CustomerId": {"S": "CUST-001"},
        "OrderId": {"S": "ORD-2024-009"},
        "Product": {"S": "Mechanical Keyboard"},
        "Quantity": {"N": "1"},
        "TotalPrice": {"N": "89.99"},
        "OrderDate": {"S": "2024-06-10"},
        "Status": {"S": "Processing"}
    }' \
    --region us-east-1
```

Each `put-item` command returns no output on success.

2. Insert items for customer `CUST-002`:

```bash
aws dynamodb put-item \
    --table-name lab06-orders \
    --item '{
        "CustomerId": {"S": "CUST-002"},
        "OrderId": {"S": "ORD-2024-002"},
        "Product": {"S": "Standing Desk Mat"},
        "Quantity": {"N": "1"},
        "TotalPrice": {"N": "39.99"},
        "OrderDate": {"S": "2024-01-22"},
        "Status": {"S": "Delivered"}
    }' \
    --region us-east-1

aws dynamodb put-item \
    --table-name lab06-orders \
    --item '{
        "CustomerId": {"S": "CUST-002"},
        "OrderId": {"S": "ORD-2024-007"},
        "Product": {"S": "LED Desk Lamp"},
        "Quantity": {"N": "3"},
        "TotalPrice": {"N": "104.97"},
        "OrderDate": {"S": "2024-04-15"},
        "Status": {"S": "Delivered"},
        "GiftWrap": {"BOOL": true}
    }' \
    --region us-east-1
```

> **Tip:** Notice that the second item for `CUST-002` includes a `GiftWrap` attribute that other items do not have. DynamoDB does not enforce a fixed schema beyond the primary key. Each item can have its own set of attributes.

3. Insert items for customer `CUST-003`:

```bash
aws dynamodb put-item \
    --table-name lab06-orders \
    --item '{
        "CustomerId": {"S": "CUST-003"},
        "OrderId": {"S": "ORD-2024-003"},
        "Product": {"S": "Notebook 200-Pack"},
        "Quantity": {"N": "5"},
        "TotalPrice": {"N": "124.95"},
        "OrderDate": {"S": "2024-02-01"},
        "Status": {"S": "Delivered"}
    }' \
    --region us-east-1

aws dynamodb put-item \
    --table-name lab06-orders \
    --item '{
        "CustomerId": {"S": "CUST-003"},
        "OrderId": {"S": "ORD-2024-010"},
        "Product": {"S": "Monitor Stand"},
        "Quantity": {"N": "2"},
        "TotalPrice": {"N": "119.98"},
        "OrderDate": {"S": "2024-07-01"},
        "Status": {"S": "Shipped"},
        "DeliveryNotes": {"S": "Leave at front desk"}
    }' \
    --region us-east-1
```

4. Verify the total item count in the table:

```bash
aws dynamodb scan \
    --table-name lab06-orders \
    --select COUNT \
    --region us-east-1
```

Expected output:

```json
{
    "Count": 8,
    "ScannedCount": 8,
    "ConsumedCapacity": null
}
```

**Expected result:** The `lab06-orders` table contains 8 items across 3 customers. Items have varying attributes, demonstrating DynamoDB's flexible schema.


### Step 8: Query the DynamoDB Table by Partition Key

The [Query](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Query.html) operation retrieves items that share a specific partition key value. This is the most efficient way to read data from DynamoDB because it accesses only the items in a single partition. In this step, you query all orders for a specific customer.

1. Query all orders for customer `CUST-001`:

```bash
aws dynamodb query \
    --table-name lab06-orders \
    --key-condition-expression "CustomerId = :cid" \
    --expression-attribute-values '{":cid": {"S": "CUST-001"}}' \
    --region us-east-1
```

Expected output:

```json
{
    "Items": [
        {
            "CustomerId": {"S": "CUST-001"},
            "OrderId": {"S": "ORD-2024-001"},
            "Product": {"S": "Wireless Mouse"},
            "Quantity": {"N": "2"},
            "TotalPrice": {"N": "59.98"},
            "OrderDate": {"S": "2024-01-15"},
            "Status": {"S": "Delivered"}
        },
        {
            "CustomerId": {"S": "CUST-001"},
            "OrderId": {"S": "ORD-2024-005"},
            "Product": {"S": "USB-C Hub"},
            "Quantity": {"N": "1"},
            "TotalPrice": {"N": "49.99"},
            "OrderDate": {"S": "2024-03-20"},
            "Status": {"S": "Shipped"}
        },
        {
            "CustomerId": {"S": "CUST-001"},
            "OrderId": {"S": "ORD-2024-009"},
            "Product": {"S": "Mechanical Keyboard"},
            "Quantity": {"N": "1"},
            "TotalPrice": {"N": "89.99"},
            "OrderDate": {"S": "2024-06-10"},
            "Status": {"S": "Processing"}
        }
    ],
    "Count": 3,
    "ScannedCount": 3,
    "ConsumedCapacity": null
}
```

Notice that the results are sorted by the sort key (`OrderId`) in ascending order. DynamoDB always returns Query results sorted by the sort key.

2. Query with a sort key condition to find only recent orders for `CUST-001` (orders with an OrderId greater than `ORD-2024-004`):

```bash
aws dynamodb query \
    --table-name lab06-orders \
    --key-condition-expression "CustomerId = :cid AND OrderId > :oid" \
    --expression-attribute-values '{
        ":cid": {"S": "CUST-001"},
        ":oid": {"S": "ORD-2024-004"}
    }' \
    --region us-east-1
```

Expected output:

```json
{
    "Items": [
        {
            "CustomerId": {"S": "CUST-001"},
            "OrderId": {"S": "ORD-2024-005"},
            "Product": {"S": "USB-C Hub"},
            "Quantity": {"N": "1"},
            "TotalPrice": {"N": "49.99"},
            "OrderDate": {"S": "2024-03-20"},
            "Status": {"S": "Shipped"}
        },
        {
            "CustomerId": {"S": "CUST-001"},
            "OrderId": {"S": "ORD-2024-009"},
            "Product": {"S": "Mechanical Keyboard"},
            "Quantity": {"N": "1"},
            "TotalPrice": {"N": "89.99"},
            "OrderDate": {"S": "2024-06-10"},
            "Status": {"S": "Processing"}
        }
    ],
    "Count": 2,
    "ScannedCount": 2,
    "ConsumedCapacity": null
}
```

3. Query with a projection expression to return only specific attributes:

```bash
aws dynamodb query \
    --table-name lab06-orders \
    --key-condition-expression "CustomerId = :cid" \
    --projection-expression "OrderId, Product, TotalPrice, #s" \
    --expression-attribute-names '{"#s": "Status"}' \
    --expression-attribute-values '{":cid": {"S": "CUST-002"}}' \
    --region us-east-1
```

Expected output:

```json
{
    "Items": [
        {
            "OrderId": {"S": "ORD-2024-002"},
            "Product": {"S": "Standing Desk Mat"},
            "TotalPrice": {"N": "39.99"},
            "Status": {"S": "Delivered"}
        },
        {
            "OrderId": {"S": "ORD-2024-007"},
            "Product": {"S": "LED Desk Lamp"},
            "TotalPrice": {"N": "104.97"},
            "Status": {"S": "Delivered"}
        }
    ],
    "Count": 2,
    "ScannedCount": 2,
    "ConsumedCapacity": null
}
```

> **Tip:** The `#s` in `--expression-attribute-names` is required because `Status` is a [reserved word](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/ReservedWords.html) in DynamoDB. You must use an expression attribute name placeholder for reserved words in projection and filter expressions.

**Expected result:** You queried the DynamoDB table by partition key, applied sort key conditions, and used projection expressions to limit returned attributes. Each query accessed only the items in a single partition, making it efficient regardless of table size.

### Step 9: Scan the DynamoDB Table and Compare with Query

The [Scan](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/Scan.html) operation reads every item in the table and then applies any filter expressions. Unlike Query, which targets a specific partition, Scan examines the entire table. This makes Scan less efficient and more expensive for large tables. In this step, you run a Scan and compare its behavior with the Query operations from Step 8.

1. Scan the entire table to retrieve all items:

```bash
aws dynamodb scan \
    --table-name lab06-orders \
    --region us-east-1
```

Expected output (partial, showing the Count and ScannedCount):

```json
{
    "Items": [
        ...
    ],
    "Count": 8,
    "ScannedCount": 8,
    "ConsumedCapacity": null
}
```

The `Count` and `ScannedCount` are both 8, meaning DynamoDB read all 8 items in the table.

2. Scan with a filter expression to find all orders with status `Delivered`:

```bash
aws dynamodb scan \
    --table-name lab06-orders \
    --filter-expression "#s = :status" \
    --expression-attribute-names '{"#s": "Status"}' \
    --expression-attribute-values '{":status": {"S": "Delivered"}}' \
    --region us-east-1
```

Expected output (partial):

```json
{
    "Items": [
        {
            "CustomerId": {"S": "CUST-001"},
            "OrderId": {"S": "ORD-2024-001"},
            "Product": {"S": "Wireless Mouse"},
            "Status": {"S": "Delivered"}
        },
        {
            "CustomerId": {"S": "CUST-002"},
            "OrderId": {"S": "ORD-2024-002"},
            "Product": {"S": "Standing Desk Mat"},
            "Status": {"S": "Delivered"}
        },
        {
            "CustomerId": {"S": "CUST-002"},
            "OrderId": {"S": "ORD-2024-007"},
            "Product": {"S": "LED Desk Lamp"},
            "Status": {"S": "Delivered"}
        },
        {
            "CustomerId": {"S": "CUST-003"},
            "OrderId": {"S": "ORD-2024-003"},
            "Product": {"S": "Notebook 200-Pack"},
            "Status": {"S": "Delivered"}
        }
    ],
    "Count": 4,
    "ScannedCount": 8,
    "ConsumedCapacity": null
}
```

Notice the difference between `Count` (4) and `ScannedCount` (8). DynamoDB scanned all 8 items in the table but only returned the 4 items that matched the filter. You are charged for all 8 items read, not just the 4 returned.

3. Compare Query and Scan performance by examining the `ScannedCount` values:

| Operation | What It Does | ScannedCount | Count | Efficiency |
|-----------|-------------|--------------|-------|------------|
| Query (Step 8, sub-step 1) | Reads items for `CUST-001` only | 3 | 3 | High: reads only the target partition |
| Scan (Step 9, sub-step 1) | Reads every item in the table | 8 | 8 | Low: reads the entire table |
| Scan with filter (Step 9, sub-step 2) | Reads every item, filters after reading | 8 | 4 | Low: reads 8 items to return 4 |

> **Warning:** Scan operations read the entire table regardless of filter expressions. For tables with millions of items, a Scan can be slow and expensive. Always prefer Query when you know the partition key. Use Scan only for infrequent operations such as data exports or one-time analysis. If you frequently need to filter by a non-key attribute (such as `Status`), consider creating a [Global Secondary Index (GSI)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html) on that attribute.

**Expected result:** You observed that Scan reads the entire table while Query reads only a single partition. The `ScannedCount` field reveals how many items DynamoDB actually read, which directly affects cost and performance.

## Validation

Confirm the following:

- [ ] A DB subnet group named `lab06-db-subnet-group` exists with subnets in at least 2 Availability Zones
- [ ] A security group named `lab06-rds-sg` exists with an inbound rule for TCP port 5432
- [ ] An RDS instance named `lab06-postgres` is in the `available` state with engine `postgres` and instance class `db.t3.micro`
- [ ] You connected to the RDS instance with `psql` and created the `lab06db` database with a `products` table containing 7 rows
- [ ] A DynamoDB table named `lab06-orders` is in the `ACTIVE` state with partition key `CustomerId` and sort key `OrderId`
- [ ] The DynamoDB table contains 8 items across 3 customers
- [ ] A Query on `CustomerId = CUST-001` returns 3 items with `ScannedCount` of 3
- [ ] A Scan with a filter on `Status = Delivered` returns `Count` of 4 but `ScannedCount` of 8

You can run the following commands to verify key configurations:

```bash
echo "--- RDS Instance Status ---"
aws rds describe-db-instances \
    --db-instance-identifier lab06-postgres \
    --query "DBInstances[0].{Status:DBInstanceStatus,Engine:Engine,Class:DBInstanceClass}" \
    --output table \
    --region us-east-1

echo "--- DB Subnet Group ---"
aws rds describe-db-subnet-groups \
    --db-subnet-group-name lab06-db-subnet-group \
    --query "DBSubnetGroups[0].{Name:DBSubnetGroupName,Status:SubnetGroupStatus}" \
    --output table \
    --region us-east-1

echo "--- DynamoDB Table ---"
aws dynamodb describe-table \
    --table-name lab06-orders \
    --query "Table.{Name:TableName,Status:TableStatus,ItemCount:ItemCount}" \
    --output table \
    --region us-east-1

echo "--- DynamoDB Item Count (via Scan) ---"
aws dynamodb scan \
    --table-name lab06-orders \
    --select COUNT \
    --region us-east-1
```

## Cleanup

Delete all resources created in this lab to avoid unexpected charges. Follow these steps in order.

> **Warning:** The RDS instance incurs charges while it is running. Delete it promptly after completing the lab. If you skip cleanup, the `db.t3.micro` instance consumes Free Tier hours.

**1. Delete the RDS instance:**

Delete the RDS instance and skip the final snapshot (since this is a lab environment):

```bash
aws rds delete-db-instance \
    --db-instance-identifier lab06-postgres \
    --skip-final-snapshot \
    --region us-east-1
```

Expected output (partial):

```json
{
    "DBInstance": {
        "DBInstanceIdentifier": "lab06-postgres",
        "DBInstanceStatus": "deleting"
    }
}
```

Wait for the instance to be fully deleted (this takes several minutes):

```bash
aws rds wait db-instance-deleted \
    --db-instance-identifier lab06-postgres \
    --region us-east-1
```

**2. Delete the DB subnet group:**

You cannot delete the subnet group until the RDS instance is fully deleted.

```bash
aws rds delete-db-subnet-group \
    --db-subnet-group-name lab06-db-subnet-group \
    --region us-east-1
```

This command returns no output on success.

**3. Delete the security group:**

```bash
aws ec2 delete-security-group \
    --group-id $RDS_SG_ID \
    --region us-east-1
```

This command returns no output on success.

If you no longer have the `$RDS_SG_ID` variable, find it first:

```bash
RDS_SG_ID=$(aws ec2 describe-security-groups \
    --filters "Name=group-name,Values=lab06-rds-sg" \
    --query "SecurityGroups[0].GroupId" \
    --output text \
    --region us-east-1)
aws ec2 delete-security-group \
    --group-id $RDS_SG_ID \
    --region us-east-1
```

**4. Delete the DynamoDB table:**

```bash
aws dynamodb delete-table \
    --table-name lab06-orders \
    --region us-east-1
```

Expected output (partial):

```json
{
    "TableDescription": {
        "TableName": "lab06-orders",
        "TableStatus": "DELETING"
    }
}
```

**5. Verify cleanup is complete:**

```bash
echo "--- RDS Instance (should return error) ---"
aws rds describe-db-instances \
    --db-instance-identifier lab06-postgres \
    --region us-east-1 2>&1

echo "--- DynamoDB Table (should return error) ---"
aws dynamodb describe-table \
    --table-name lab06-orders \
    --region us-east-1 2>&1

echo "--- Security Group (should return error) ---"
aws ec2 describe-security-groups \
    --filters "Name=group-name,Values=lab06-rds-sg" \
    --query "SecurityGroups" \
    --region us-east-1
```

Expected results:
- The RDS command returns: `An error occurred (DBInstanceNotFound)`
- The DynamoDB command returns: `An error occurred (ResourceNotFoundException)`
- The security group command returns an empty list: `[]`

## Challenge (Optional)

Using only concepts from Modules 01 through 06, complete the following:

1. Create a [Global Secondary Index (GSI)](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/GSI.html) on the `lab06-orders` table (recreate the table first if you already deleted it). Use `Status` as the GSI partition key and `OrderDate` as the GSI sort key. Name the index `StatusDateIndex`. After the GSI is active, run a Query against the index to find all orders with status `Delivered`, sorted by date. Compare the `ScannedCount` of this GSI Query with the Scan-with-filter approach from Step 9. The GSI Query should read only the matching items, not the entire table.

2. Enable [encryption at rest](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Overview.Encryption.html) on a new RDS instance by specifying `--storage-encrypted` during creation. After the instance is available, verify the encryption status using `describe-db-instances` and check the `StorageEncrypted` and `KmsKeyId` fields. Compare this with the default (unencrypted) instance you created in Step 4.

3. Create a second DynamoDB table with a simple primary key (partition key only, no sort key). Insert several items and attempt to use the Query operation. Observe how Query behaves differently with a simple key versus a composite key. With a simple key, each Query returns at most one item (since the partition key must be unique). This demonstrates why composite keys are valuable for one-to-many relationships.

> **Tip:** Remember to delete all resources created during the challenge (GSIs, additional RDS instances, additional DynamoDB tables) to avoid charges.
