# Lab 13: Securing AWS Resources with KMS, Secrets Manager, CloudTrail, and GuardDuty

## Objective

Configure encryption with a customer managed KMS key, store and retrieve a database credential using AWS Secrets Manager, enable a CloudTrail trail for audit logging, enable Amazon GuardDuty for threat detection, and evaluate resource compliance using AWS Config rules.

## Architecture Diagram

This lab adds security controls to the infrastructure you built in previous modules:

```
AWS Account (us-east-1)
    |
    ├── AWS KMS
    |   └── Customer managed key: bootcamp-encryption-key
    |       └── Used by: S3 bucket encryption, Secrets Manager secret encryption
    |
    ├── AWS Secrets Manager
    |   └── Secret: bootcamp/db-password
    |       └── Encrypted with: bootcamp-encryption-key
    |
    ├── Amazon S3
    |   └── Bucket: bootcamp-secure-data-<account-id>
    |       └── Default encryption: bootcamp-encryption-key (SSE-KMS)
    |
    ├── AWS CloudTrail
    |   └── Trail: bootcamp-audit-trail
    |       └── Delivers to: S3 bucket (bootcamp-cloudtrail-logs-<account-id>)
    |       └── Logs: Management events (all Regions)
    |
    ├── Amazon GuardDuty
    |   └── Detector: enabled
    |       └── Analyzes: CloudTrail, VPC Flow Logs, DNS logs
    |
    └── AWS Config
        └── Rules: s3-bucket-server-side-encryption-enabled,
                   cloudtrail-enabled, iam-root-access-key-check
```

## Prerequisites

- Completed [Lab 01: AWS Account Setup](../../01-cloud-fundamentals/lab/lab-01-aws-account-setup.md)
- Completed [Lab 02: IAM Users, Groups, Policies, and Roles](../../02-iam-and-security/lab/lab-02-iam-users-groups-roles.md)
- Completed [Module 13: Security in Depth](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI installed and configured locally)

## Duration

75 minutes

## Instructions

### Step 1: Create a Customer Managed KMS Key

In this step, you create a [customer managed KMS key](https://docs.aws.amazon.com/kms/latest/developerguide/concepts.html) that you will use to encrypt an S3 bucket and a Secrets Manager secret.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/) as `bootcamp-admin`.
2. Verify that the Region selector displays **US East (N. Virginia) us-east-1**.
3. In the search bar, type `KMS` and select **Key Management Service**.
4. In the left navigation pane, choose **Customer managed keys**.
5. Choose **Create key**.
6. Configure the key:
   - **Key type:** Symmetric
   - **Key usage:** Encrypt and decrypt
7. Choose **Next**.
8. Set the alias to `bootcamp-encryption-key` and add a description: `Customer managed key for bootcamp security lab`.
9. Choose **Next**.
10. Under **Key administrators**, select your `bootcamp-admin` user.
11. Choose **Next**.
12. Under **Key users**, select your `bootcamp-admin` user.
13. Choose **Next**, review the key policy, and choose **Finish**.

**Expected result:** The Customer managed keys page lists `bootcamp-encryption-key` with status `Enabled`.

**CLI equivalent:**

```bash
KEY_ID=$(aws kms create-key \
  --description "Customer managed key for bootcamp security lab" \
  --query "KeyMetadata.KeyId" \
  --output text \
  --region us-east-1)
echo "Key ID: $KEY_ID"
```

```bash
aws kms create-alias \
  --alias-name alias/bootcamp-encryption-key \
  --target-key-id $KEY_ID \
  --region us-east-1
```

14. Verify the key:

```bash
aws kms describe-key \
  --key-id alias/bootcamp-encryption-key \
  --query "KeyMetadata.{KeyId:KeyId,KeyState:KeyState,Description:Description}" \
  --region us-east-1
```

Expected output:

```json
{
    "KeyId": "abcd1234-5678-90ab-cdef-example11111",
    "KeyState": "Enabled",
    "Description": "Customer managed key for bootcamp security lab"
}
```

### Step 2: Encrypt an S3 Bucket with the Customer Managed Key

In this step, you create an S3 bucket with default encryption using your customer managed KMS key.

1. In the console search bar, type `S3` and select **S3**.
2. Choose **Create bucket**.
3. Configure the bucket:
   - **Bucket name:** `bootcamp-secure-data-<your-account-id>` (replace `<your-account-id>` with your 12-digit AWS account ID)
   - **Region:** US East (N. Virginia) us-east-1
4. Under **Default encryption**, select **Server-side encryption with AWS Key Management Service keys (SSE-KMS)**.
5. Under **AWS KMS key**, select **Choose from your AWS KMS keys** and select `bootcamp-encryption-key`.
6. Leave all other settings as defaults and choose **Create bucket**.

**Expected result:** The bucket is created with SSE-KMS default encryption using your customer managed key.

7. Upload a test file to verify encryption. Choose the bucket name, choose **Upload**, add any small text file, and choose **Upload**.
8. After the upload completes, choose the file name and check the **Server-side encryption settings** section. It should show `AWS KMS (SSE-KMS)` with your key alias.

**CLI equivalent:**

```bash
ACCOUNT_ID=$(aws sts get-caller-identity --query "Account" --output text)
BUCKET_NAME="bootcamp-secure-data-${ACCOUNT_ID}"

aws s3api create-bucket \
  --bucket $BUCKET_NAME \
  --region us-east-1
```

```bash
aws s3api put-bucket-encryption \
  --bucket $BUCKET_NAME \
  --server-side-encryption-configuration '{
    "Rules": [{
      "ApplyServerSideEncryptionByDefault": {
        "SSEAlgorithm": "aws:kms",
        "KMSMasterKeyID": "alias/bootcamp-encryption-key"
      },
      "BucketKeyEnabled": true
    }]
  }' \
  --region us-east-1
```

> **Tip:** Enabling the S3 Bucket Key (`BucketKeyEnabled: true`) reduces the number of KMS API calls, which lowers KMS costs for S3 encryption. The bucket key is a short-lived key derived from your KMS key that S3 uses for a limited time before requesting a new one.

### Step 3: Store and Retrieve a Secret in AWS Secrets Manager

In this step, you store a database credential in [AWS Secrets Manager](https://docs.aws.amazon.com/secretsmanager/latest/userguide/intro.html) and retrieve it programmatically.

1. In the console search bar, type `Secrets Manager` and select **Secrets Manager**.
2. Choose **Store a new secret**.
3. Configure the secret:
   - **Secret type:** Other type of secret
   - **Key/value pairs:** Add two key-value pairs:
     - Key: `username`, Value: `bootcamp_db_admin`
     - Key: `password`, Value: `S3cur3P@ssw0rd2026`
   - **Encryption key:** Select `bootcamp-encryption-key`
4. Choose **Next**.
5. Set the secret name to `bootcamp/db-password` and add a description: `Database credentials for bootcamp application`.
6. Choose **Next**.
7. On the rotation configuration page, leave rotation disabled for now (you will explore rotation concepts in the README).
8. Choose **Next**, review, and choose **Store**.

**Expected result:** The Secrets Manager console lists `bootcamp/db-password` with status `Active`.

9. Retrieve the secret using the CLI:

```bash
aws secretsmanager get-secret-value \
  --secret-id bootcamp/db-password \
  --query "SecretString" \
  --output text \
  --region us-east-1
```

Expected output:

```json
{"username":"bootcamp_db_admin","password":"S3cur3P@ssw0rd2026"}
```

10. Parse the password value using `jq` (or Python):

```bash
aws secretsmanager get-secret-value \
  --secret-id bootcamp/db-password \
  --query "SecretString" \
  --output text \
  --region us-east-1 | jq -r '.password'
```

Expected output:

```
S3cur3P@ssw0rd2026
```

> **Tip:** In a real application, you would retrieve the secret at runtime using the AWS SDK, not the CLI. The SDK caches the secret value locally and refreshes it periodically, so your application always has the current credential without restarting.

### Step 4: Enable a CloudTrail Trail

In this step, you create a [CloudTrail trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-trails.html) that logs management events across all Regions and delivers them to an S3 bucket.

Create the trail and its S3 bucket. CloudTrail can create the bucket for you, or you can create it manually.

> **Hint:** You need to create an S3 bucket for CloudTrail logs, configure a bucket policy that allows CloudTrail to write to it, and then create the trail pointing to that bucket. The console handles the bucket policy automatically. If using the CLI, you must create the bucket and attach the policy before creating the trail.

Use the console or CLI to:

1. Create an S3 bucket named `bootcamp-cloudtrail-logs-<your-account-id>`.
2. Create a trail named `bootcamp-audit-trail` that logs management events for all Regions.
3. Configure the trail to deliver logs to the S3 bucket you created.
4. Verify the trail is logging by checking the trail status.

After creating the trail, perform a test action (for example, create and delete a test S3 bucket) and then check the CloudTrail Event History to confirm the action was recorded.

> **Tip:** CloudTrail events typically appear in the Event History within 5 to 15 minutes of the API call. If you do not see your test event immediately, wait a few minutes and refresh.

### Step 5: Enable Amazon GuardDuty

In this step, you enable [Amazon GuardDuty](https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html) to begin monitoring your account for threats.

Enable GuardDuty in the `us-east-1` Region. After enabling it, explore the GuardDuty console to understand the findings interface.

> **Hint:** Enabling GuardDuty is a single action in the console or a single CLI command. GuardDuty begins analyzing CloudTrail management events, VPC Flow Logs, and DNS logs immediately. You do not need to configure any data sources manually.

After enabling GuardDuty:

1. Navigate to the **Findings** page. If your account is new, there may be no findings yet.
2. Choose **Settings** and review the data sources that GuardDuty analyzes.
3. Generate sample findings to explore the findings interface: choose **Settings**, scroll to **Sample findings**, and choose **Generate sample findings**.
4. Return to the **Findings** page and examine several sample findings. For each finding, note the severity, affected resource, and recommended action.

> **Tip:** Sample findings are labeled with `[SAMPLE]` in the title. They do not represent real threats in your account. Use them to familiarize yourself with the finding format and severity levels.

### Step 6: Configure AWS Config Rules

In this step, you enable [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.html) and create rules to evaluate the compliance of your resources.

Enable AWS Config recording for your account and add the following managed rules:

- `s3-bucket-server-side-encryption-enabled`: checks that all S3 buckets have default encryption enabled
- `cloudtrail-enabled`: checks that CloudTrail is enabled in the account
- `iam-root-access-key-check`: checks that the root account does not have active access keys

> **Hint:** When enabling Config for the first time, you need to set up a configuration recorder and a delivery channel (an S3 bucket where Config stores configuration snapshots). The console setup wizard handles this automatically. If using the CLI, you must create the recorder, delivery channel, and start recording separately.

After enabling Config and adding the rules:

1. Wait 2 to 5 minutes for Config to evaluate your resources.
2. Navigate to the **Rules** page and check the compliance status of each rule.
3. Identify any non-compliant resources and determine what change would bring them into compliance.

> **Tip:** If the `s3-bucket-server-side-encryption-enabled` rule shows non-compliant resources, check whether the CloudTrail log bucket you created in Step 4 has default encryption enabled. If not, enable SSE-S3 or SSE-KMS encryption on that bucket to bring it into compliance.

## Validation

Confirm the following:

- [ ] A customer managed KMS key named `bootcamp-encryption-key` exists and is enabled
- [ ] The S3 bucket `bootcamp-secure-data-<account-id>` uses SSE-KMS encryption with your customer managed key
- [ ] An uploaded object in the encrypted bucket shows `AWS KMS (SSE-KMS)` in its encryption settings
- [ ] The secret `bootcamp/db-password` exists in Secrets Manager and returns the correct username and password when retrieved via CLI
- [ ] A CloudTrail trail named `bootcamp-audit-trail` is logging and delivering events to an S3 bucket
- [ ] A recent API action (such as creating a test bucket) appears in the CloudTrail Event History
- [ ] Amazon GuardDuty is enabled and sample findings are visible in the Findings page
- [ ] AWS Config is recording and at least three managed rules are evaluating resource compliance

## Cleanup

Delete all resources created in this lab to avoid charges:

1. **Delete the AWS Config rules and recorder:**
   - Navigate to the [AWS Config console](https://console.aws.amazon.com/config/).
   - Choose **Rules**, select each rule, and choose **Delete**.
   - Choose **Settings**, then choose **Edit** and stop the configuration recorder.

2. **Disable Amazon GuardDuty:**
   - Navigate to the [GuardDuty console](https://console.aws.amazon.com/guardduty/).
   - Choose **Settings**, scroll to the bottom, and choose **Suspend GuardDuty** or **Disable GuardDuty**.

3. **Delete the CloudTrail trail:**
   - Navigate to the [CloudTrail console](https://console.aws.amazon.com/cloudtrail/).
   - Choose **Trails**, select `bootcamp-audit-trail`, and choose **Delete**.
   - Confirm the deletion.

4. **Delete the CloudTrail log bucket:**
   - Navigate to the [S3 console](https://console.aws.amazon.com/s3/).
   - Select `bootcamp-cloudtrail-logs-<account-id>`.
   - Empty the bucket first (select all objects and choose **Delete**), then delete the bucket.

5. **Delete the Secrets Manager secret:**
   - Navigate to the [Secrets Manager console](https://console.aws.amazon.com/secretsmanager/).
   - Select `bootcamp/db-password` and choose **Actions**, then **Delete secret**.
   - Set the recovery window to 7 days (minimum) and confirm.

6. **Delete the encrypted S3 bucket:**
   - Navigate to the S3 console.
   - Select `bootcamp-secure-data-<account-id>`.
   - Empty the bucket, then delete it.

7. **Schedule KMS key deletion:**
   - Navigate to the [KMS console](https://console.aws.amazon.com/kms/).
   - Select `bootcamp-encryption-key` and choose **Key actions**, then **Schedule key deletion**.
   - Set the waiting period to 7 days (minimum) and confirm.

> **Warning:** KMS key deletion is irreversible after the waiting period. Ensure you have deleted all data encrypted with this key before scheduling deletion. During the waiting period, you can cancel the deletion if needed.

## Challenge (Optional)

Extend this lab by implementing the following:

1. Create a Secrets Manager secret that stores RDS database credentials and configure automatic rotation using the built-in rotation function for RDS. You will need an RDS instance (from [Module 06](../../06-databases-rds-dynamodb/README.md)) and a Lambda rotation function that Secrets Manager creates automatically.

2. Create a custom AWS Config rule using a Lambda function that checks whether all S3 buckets have versioning enabled (not just encryption). Evaluate the rule and identify any non-compliant buckets.

3. Create an EventBridge rule that triggers an SNS notification whenever GuardDuty generates a finding with High severity. Test it by generating sample findings and verifying that the email notification arrives.

These challenges combine security services with Lambda, EventBridge, and SNS concepts from previous modules.
