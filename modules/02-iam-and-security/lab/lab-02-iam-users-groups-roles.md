# Lab 02: IAM Users, Groups, Policies, and Roles

## Objective

Create and configure IAM identities (users, user groups, and roles), attach managed and customer managed policies, and observe how IAM policy evaluation determines access.

## Architecture Diagram

This lab builds the following IAM identity structure within your AWS account:

```
AWS Account (us-east-1)
    |
    ├── IAM User Group: Developers
    |       ├── Attached Policy: AmazonS3ReadOnlyAccess (AWS managed)
    |       └── Member: dev-user-01
    |
    ├── IAM User: dev-user-01
    |       ├── Inherits: AmazonS3ReadOnlyAccess (from Developers group)
    |       └── Inline Deny Policy: DenyAllS3 (attached in Step 6)
    |
    ├── Customer Managed Policy: BootcampS3BucketPolicy
    |       └── Allows s3:GetObject, s3:PutObject on a specific bucket
    |
    └── IAM Role: BootcampEC2S3ReadRole
            ├── Trust Policy: EC2 service
            └── Attached Policy: AmazonS3ReadOnlyAccess (AWS managed)
```

You will create each identity, test permissions using the AWS CLI and the IAM Policy Simulator, and then demonstrate that an explicit deny always overrides an allow.

## Prerequisites

- Completed [Lab 01: AWS Account Setup and Console Tour](../../01-cloud-fundamentals/lab/lab-01-aws-account-setup.md)
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI installed and configured locally)

## Duration

60 minutes

## Instructions

### Step 1: Create an IAM User Group with a Managed Policy

In this step, you create a user group called "Developers" and attach the [AmazonS3ReadOnlyAccess](https://docs.aws.amazon.com/aws-managed-policy/latest/reference/AmazonS3ReadOnlyAccess.html) AWS managed policy to it.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/) as `bootcamp-admin`.
2. In the search bar at the top, type `IAM` and select **IAM** from the results.
3. In the left navigation pane, choose **User groups**.
4. Choose **Create group**.
5. In the **User group name** field, enter `Developers`.
6. In the **Attach permissions policies** section, type `AmazonS3ReadOnlyAccess` in the search box.
7. Select the checkbox next to **AmazonS3ReadOnlyAccess**.
8. Choose **Create user group**.

**Expected result:** The User groups page displays the `Developers` group. The **Attached policies** column shows `1`.

9. To verify using the CLI, open [CloudShell](https://docs.aws.amazon.com/cloudshell/latest/userguide/welcome.html) and run:

```bash
aws iam get-group --group-name Developers --region us-east-1
```

Expected output (the `Users` array is empty because you have not added any users yet):

```json
{
    "Users": [],
    "Group": {
        "Path": "/",
        "GroupName": "Developers",
        "GroupId": "AGPAEXAMPLE123456",
        "Arn": "arn:aws:iam::123456789012:group/Developers",
        "CreateDate": "2024-01-15T10:00:00+00:00"
    },
    "IsTruncated": false
}
```

10. Verify the attached policy:

```bash
aws iam list-attached-group-policies --group-name Developers
```

Expected output:

```json
{
    "AttachedPolicies": [
        {
            "PolicyName": "AmazonS3ReadOnlyAccess",
            "PolicyArn": "arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess"
        }
    ],
    "IsTruncated": false
}
```

### Step 2: Create an IAM User and Add to the Developers Group

In this step, you create an [IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) named `dev-user-01` and add the user to the `Developers` group.

1. In the IAM console left navigation pane, choose **Users**.
2. Choose **Create user**.
3. In the **User name** field, enter `dev-user-01`.
4. Select the checkbox **Provide user access to the AWS Management Console**.
5. Select **I want to create an IAM user**.
6. Choose **Custom password** and enter a strong password. Record this password for later use in this lab.
7. Clear the **User must create a new password at next sign-in** checkbox.
8. Choose **Next**.
9. On the **Set permissions** page, select **Add user to group**.
10. Select the checkbox next to the **Developers** group.
11. Choose **Next**.
12. Review the user details and choose **Create user**.
13. On the confirmation page, note the **Console sign-in URL**. You will use it later to sign in as `dev-user-01`.
14. Choose **Return to users list**.

**Expected result:** The Users page lists `dev-user-01`. Choosing the user name shows `Developers` under the **Groups** tab.

15. Verify using the CLI:

```bash
aws iam list-groups-for-user --user-name dev-user-01
```

Expected output:

```json
{
    "Groups": [
        {
            "Path": "/",
            "GroupName": "Developers",
            "GroupId": "AGPAEXAMPLE123456",
            "Arn": "arn:aws:iam::123456789012:group/Developers",
            "CreateDate": "2024-01-15T10:00:00+00:00"
        }
    ]
}
```

### Step 3: Test Permissions for dev-user-01

In this step, you create access keys for `dev-user-01` and verify that the user can list S3 buckets (allowed by `AmazonS3ReadOnlyAccess`) but cannot launch EC2 instances (no EC2 permissions granted).

1. In the IAM console, choose **Users**, then choose **dev-user-01**.
2. Choose the **Security credentials** tab.
3. Scroll to the **Access keys** section and choose **Create access key**.
4. Select **Command Line Interface (CLI)** as the use case.
5. Select the confirmation checkbox at the bottom and choose **Next**.
6. Choose **Create access key**.
7. Copy the **Access key ID** and **Secret access key** values. You will need both in the next step.

> **Warning:** This is the only time you can view the secret access key. If you lose it, you must create a new access key pair.

8. In CloudShell, configure a named profile for `dev-user-01`:

```bash
aws configure --profile dev-user-01
```

9. When prompted, enter the following values:
   - **AWS Access Key ID:** paste the access key ID from step 7
   - **AWS Secret Access Key:** paste the secret access key from step 7
   - **Default region name:** `us-east-1`
   - **Default output format:** `json`

10. Test S3 read access by listing buckets:

```bash
aws s3 ls --profile dev-user-01
```

**Expected result:** The command succeeds. If you have S3 buckets in the account, they are listed. If you have no buckets, the command returns an empty result with no error.

11. Test that `dev-user-01` cannot create EC2 instances:

```bash
aws ec2 run-instances \
  --image-id ami-0c02fb55956c7d316 \
  --instance-type t2.micro \
  --region us-east-1 \
  --profile dev-user-01
```

**Expected result:** The command fails with an error similar to:

```
An error occurred (UnauthorizedOperation) when calling the RunInstances operation:
You are not authorized to perform this operation.
```

This confirms that `dev-user-01` has S3 read-only access (inherited from the Developers group) but no EC2 permissions. IAM denies any action that is not explicitly allowed.

> **Tip:** The `UnauthorizedOperation` error is the expected behavior. IAM follows a default-deny model: if no policy explicitly allows an action, the action is denied.

### Step 4: Create a Customer Managed Policy

In this step, you create a [customer managed policy](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html) that allows specific S3 actions on a specific bucket. This demonstrates how to write fine-grained permissions.

1. First, create an S3 bucket to use as the policy target. In CloudShell, run:

```bash
aws s3 mb s3://bootcamp-lab02-$(aws sts get-caller-identity --query Account --output text) --region us-east-1
```

**Expected result:** Output similar to:

```
make_bucket: bootcamp-lab02-123456789012
```

> **Tip:** The command appends your AWS account ID to the bucket name to ensure uniqueness. S3 bucket names must be globally unique across all AWS accounts.

2. In the IAM console left navigation pane, choose **Policies**.
3. Choose **Create policy**.
4. Choose the **JSON** tab (or toggle to the JSON policy editor).
5. Replace the default policy content with the following JSON. Replace `ACCOUNT_ID` with your 12-digit AWS account ID:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowS3ActionsOnBootcampBucket",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:PutObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bootcamp-lab02-ACCOUNT_ID",
                "arn:aws:s3:::bootcamp-lab02-ACCOUNT_ID/*"
            ]
        }
    ]
}
```

> **Tip:** You can find your account ID by running `aws sts get-caller-identity --query Account --output text` in CloudShell.

6. Choose **Next**.
7. In the **Policy name** field, enter `BootcampS3BucketPolicy`.
8. In the **Description** field, enter `Allows GetObject, PutObject, and ListBucket on the bootcamp-lab02 bucket`.
9. Choose **Create policy**.

**Expected result:** The Policies page displays `BootcampS3BucketPolicy` in the list of customer managed policies.

10. Verify using the CLI:

```bash
aws iam list-policies --scope Local --query "Policies[?PolicyName=='BootcampS3BucketPolicy']"
```

Expected output:

```json
[
    {
        "PolicyName": "BootcampS3BucketPolicy",
        "PolicyId": "ANPAEXAMPLE123456",
        "Arn": "arn:aws:iam::123456789012:policy/BootcampS3BucketPolicy",
        "Path": "/",
        "DefaultVersionId": "v1",
        "AttachmentCount": 0,
        "IsAttachable": true,
        "CreateDate": "2024-01-15T10:30:00+00:00",
        "UpdateDate": "2024-01-15T10:30:00+00:00"
    }
]
```

### Step 5: Create an IAM Role for EC2 with S3 Read Access

In this step, you create an [IAM role](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html) that allows EC2 instances to read from S3. This role uses an [instance profile](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use_switch-role-ec2_instance-profiles.html) so that EC2 instances can assume the role and receive temporary credentials automatically.

1. In the IAM console left navigation pane, choose **Roles**.
2. Choose **Create role**.
3. Under **Trusted entity type**, select **AWS service**.
4. Under **Use case**, select **EC2**.
5. Choose **Next**.
6. In the **Permissions policies** search box, type `AmazonS3ReadOnlyAccess`.
7. Select the checkbox next to **AmazonS3ReadOnlyAccess**.
8. Choose **Next**.
9. In the **Role name** field, enter `BootcampEC2S3ReadRole`.
10. In the **Description** field, enter `Allows EC2 instances to read from S3 buckets`.
11. Choose **Create role**.

**Expected result:** The Roles page displays `BootcampEC2S3ReadRole`. The role has `AmazonS3ReadOnlyAccess` attached and a trust policy that allows the EC2 service to assume it.

12. Verify the role and its trust policy using the CLI:

```bash
aws iam get-role --role-name BootcampEC2S3ReadRole --query "Role.AssumeRolePolicyDocument"
```

Expected output:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "ec2.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

This trust policy means only the EC2 service can assume this role. When you attach this role to an EC2 instance, the instance automatically receives temporary credentials to access S3.

13. Verify the attached permissions policy:

```bash
aws iam list-attached-role-policies --role-name BootcampEC2S3ReadRole
```

Expected output:

```json
{
    "AttachedPolicies": [
        {
            "PolicyName": "AmazonS3ReadOnlyAccess",
            "PolicyArn": "arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess"
        }
    ],
    "IsTruncated": false
}
```

> **Tip:** Using IAM roles with EC2 instances eliminates the need to store access keys on the instance. The instance receives temporary credentials that rotate automatically. This is a core [IAM security best practice](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html).

### Step 6: Test the Role Using the IAM Policy Simulator

The [IAM Policy Simulator](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_testing-policies.html) lets you test the effect of policies without actually making API calls. In this step, you use it to verify that `BootcampEC2S3ReadRole` can read from S3 but cannot write.

1. Open the [IAM Policy Simulator](https://policysim.aws.amazon.com/) in a new browser tab.
2. In the left panel under **Users, Groups, and Roles**, expand **Roles**.
3. Select **BootcampEC2S3ReadRole**.
4. In the **Policy Simulator** section on the right, under **Select service**, choose **S3**.
5. Under **Select actions**, select the following actions:
   - `GetObject`
   - `ListBucket`
   - `PutObject`
   - `DeleteObject`
6. Choose **Run Simulation**.

**Expected result:**

| Action | Result |
|--------|--------|
| s3:GetObject | allowed |
| s3:ListBucket | allowed |
| s3:PutObject | denied |
| s3:DeleteObject | denied |

The `AmazonS3ReadOnlyAccess` policy allows read actions (`GetObject`, `ListBucket`) but does not allow write actions (`PutObject`, `DeleteObject`). Since IAM denies any action not explicitly allowed, the write actions are denied.

7. You can also test the simulation from the CLI:

```bash
aws iam simulate-principal-policy \
  --policy-source-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):role/BootcampEC2S3ReadRole \
  --action-names s3:GetObject s3:PutObject s3:ListBucket s3:DeleteObject
```

Expected output (abbreviated):

```json
{
    "EvaluationResults": [
        {
            "EvalActionName": "s3:GetObject",
            "EvalDecision": "allowed"
        },
        {
            "EvalActionName": "s3:PutObject",
            "EvalDecision": "implicitDeny"
        },
        {
            "EvalActionName": "s3:ListBucket",
            "EvalDecision": "allowed"
        },
        {
            "EvalActionName": "s3:DeleteObject",
            "EvalDecision": "implicitDeny"
        }
    ]
}
```

> **Tip:** Notice the difference between `implicitDeny` and an explicit deny. An implicit deny means no policy allows the action. An explicit deny means a policy statement with `"Effect": "Deny"` actively blocks the action. You will see an explicit deny in the next step.

### Step 7: Demonstrate Explicit Deny Overrides Allow

In this step, you attach an inline deny policy to `dev-user-01` that denies all S3 actions. Even though the `Developers` group grants `AmazonS3ReadOnlyAccess`, the explicit deny overrides the allow. This demonstrates the [IAM policy evaluation logic](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html).

1. In CloudShell, create an inline policy that denies all S3 actions for `dev-user-01`:

```bash
aws iam put-user-policy \
  --user-name dev-user-01 \
  --policy-name DenyAllS3 \
  --policy-document '{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DenyAllS3Actions",
            "Effect": "Deny",
            "Action": "s3:*",
            "Resource": "*"
        }
    ]
}'
```

**Expected result:** The command completes with no output (exit code 0).

2. Verify the inline policy was attached:

```bash
aws iam list-user-policies --user-name dev-user-01
```

Expected output:

```json
{
    "PolicyNames": [
        "DenyAllS3"
    ],
    "IsTruncated": false
}
```

3. Now test S3 access as `dev-user-01`:

```bash
aws s3 ls --profile dev-user-01
```

**Expected result:** The command fails with an error similar to:

```
An error occurred (AccessDenied) when calling the ListBuckets operation: Access Denied
```

4. Compare this to the result in Step 3, where the same command succeeded. The `Developers` group still has `AmazonS3ReadOnlyAccess` attached, but the explicit deny in the `DenyAllS3` inline policy overrides it.

This is the key principle of [IAM policy evaluation](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html):

1. By default, all requests are denied (implicit deny).
2. An explicit allow in a policy overrides the implicit deny.
3. An explicit deny in any policy always overrides any allow.

> **Warning:** Explicit deny policies are powerful. A single deny statement anywhere in the policy chain blocks access, regardless of how many other policies allow the action. Use deny policies carefully and document them clearly.

5. Use the IAM Policy Simulator to visualize this. Open the [IAM Policy Simulator](https://policysim.aws.amazon.com/).
6. In the left panel, expand **Users** and select **dev-user-01**.
7. Under **Select service**, choose **S3**.
8. Under **Select actions**, select `ListAllMyBuckets`.
9. Choose **Run Simulation**.

**Expected result:** The result shows `denied` with the `DenyAllS3` policy listed as the source of the denial. This confirms that the explicit deny overrides the allow from `AmazonS3ReadOnlyAccess`.

## Validation

Confirm the following:

- [ ] The `Developers` user group exists with the `AmazonS3ReadOnlyAccess` policy attached
- [ ] The `dev-user-01` IAM user exists and is a member of the `Developers` group
- [ ] Running `aws s3 ls --profile dev-user-01` returns `AccessDenied` (because the `DenyAllS3` inline policy is active)
- [ ] The `BootcampS3BucketPolicy` customer managed policy exists and targets the `bootcamp-lab02-*` bucket
- [ ] The `BootcampEC2S3ReadRole` IAM role exists with a trust policy allowing `ec2.amazonaws.com` and `AmazonS3ReadOnlyAccess` attached
- [ ] The IAM Policy Simulator shows `s3:GetObject` as `allowed` and `s3:PutObject` as `denied` for `BootcampEC2S3ReadRole`
- [ ] The IAM Policy Simulator shows `s3:ListAllMyBuckets` as `denied` for `dev-user-01` (explicit deny from `DenyAllS3`)

## Cleanup

Delete all resources created in this lab to avoid unintended configuration in your account.

1. **Remove the inline deny policy from dev-user-01:**

```bash
aws iam delete-user-policy --user-name dev-user-01 --policy-name DenyAllS3
```

2. **Delete the access keys for dev-user-01:**

```bash
aws iam list-access-keys --user-name dev-user-01 --query "AccessKeyMetadata[].AccessKeyId" --output text
```

Use the access key ID from the output in the following command:

```bash
aws iam delete-access-key --user-name dev-user-01 --access-key-id ACCESS_KEY_ID
```

3. **Remove dev-user-01 from the Developers group:**

```bash
aws iam remove-user-from-group --user-name dev-user-01 --group-name Developers
```

4. **Delete the dev-user-01 user:**

First, delete the login profile (console password):

```bash
aws iam delete-login-profile --user-name dev-user-01
```

Then delete the user:

```bash
aws iam delete-user --user-name dev-user-01
```

5. **Detach the policy from the Developers group and delete the group:**

```bash
aws iam detach-group-policy --group-name Developers --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
```

```bash
aws iam delete-group --group-name Developers
```

6. **Delete the customer managed policy:**

```bash
aws iam delete-policy --policy-arn arn:aws:iam::$(aws sts get-caller-identity --query Account --output text):policy/BootcampS3BucketPolicy
```

7. **Delete the IAM role:**

```bash
aws iam detach-role-policy --role-name BootcampEC2S3ReadRole --policy-arn arn:aws:iam::aws:policy/AmazonS3ReadOnlyAccess
```

```bash
aws iam delete-role --role-name BootcampEC2S3ReadRole
```

8. **Delete the S3 bucket:**

```bash
aws s3 rb s3://bootcamp-lab02-$(aws sts get-caller-identity --query Account --output text) --force
```

> **Warning:** The `--force` flag deletes all objects in the bucket before removing the bucket. Verify you are deleting the correct bucket.

9. **Remove the dev-user-01 CLI profile:**

```bash
aws configure --profile dev-user-01 set aws_access_key_id ""
aws configure --profile dev-user-01 set aws_secret_access_key ""
```

## Challenge (Optional)

Using only concepts from Modules 01 and 02, complete the following:

1. Create a second user group called `Auditors` with the `SecurityAudit` AWS managed policy attached.
2. Create an IAM user called `audit-user-01` and add the user to the `Auditors` group.
3. Sign in as `audit-user-01` (or use a CLI profile) and verify that the user can view IAM policies and CloudTrail logs but cannot create or modify any resources.
4. Use the IAM Policy Simulator to test which actions `audit-user-01` is allowed to perform on the IAM and CloudTrail services.

This exercise reinforces the concept of [least privilege](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html): auditors need read access to security configurations but should never be able to modify them.

> **Tip:** Remember to delete the `Auditors` group and `audit-user-01` user after completing the challenge.
