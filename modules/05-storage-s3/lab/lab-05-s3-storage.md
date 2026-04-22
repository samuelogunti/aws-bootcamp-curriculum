# Lab 05: Working with Amazon S3 Storage

## Objective

Create an Amazon S3 bucket, upload and manage objects using the console and CLI, configure versioning, set up a Lifecycle policy, verify default encryption and Block Public Access settings, and host a static website from the bucket.

## Architecture Diagram

This lab builds a storage environment using a single S3 bucket that you progressively configure with additional features:

```
Student's browser / AWS CLI
    |
    v
AWS Management Console (us-east-1)
    |
    └── S3 Bucket: lab05-s3-<your-initials>-<random> (us-east-1)
            |
            ├── Objects: sample-file.txt, hello.txt (uploaded via console and CLI)
            |
            ├── Versioning: Enabled
            |       └── hello.txt (version 1 and version 2)
            |
            ├── Lifecycle Rule: TransitionToIA
            |       └── Transition all objects to S3 Standard-IA after 30 days
            |
            ├── Default Encryption: SSE-S3 (AES-256, enabled by default)
            |
            ├── Block Public Access: All four settings enabled by default
            |       └── Disabled for static website hosting step
            |
            └── Static Website Hosting: Enabled
                    ├── index.html (home page)
                    ├── error.html (custom 404 page)
                    └── Bucket Policy: Public read access (s3:GetObject)
                    |
                    v
            http://<bucket>.s3-website-us-east-1.amazonaws.com
```

You will use a single S3 bucket throughout this lab. Each step adds a new configuration layer, demonstrating how S3 features work together.

## Prerequisites

- Completed [Lab 01: AWS Account Setup and Console Tour](../../01-cloud-fundamentals/lab/lab-01-aws-account-setup.md)
- Completed [Module 02: Identity and Access Management (IAM) and Security](../../02-iam-and-security/README.md) (understanding of IAM policies and bucket policies)
- Completed [Module 05: Storage with Amazon S3](../README.md) lesson content
- Signed in to the AWS Management Console as the `bootcamp-admin` IAM user
- AWS CloudShell available (or the AWS CLI installed and configured locally)

## Duration

60 minutes

## Instructions

### Step 1: Create an S3 Bucket with a Unique Name

S3 bucket names are [globally unique](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html) across all AWS accounts and all Regions. In this step, you create a bucket with a unique name that you will use for the rest of the lab.

1. Sign in to the [AWS Management Console](https://console.aws.amazon.com/) as `bootcamp-admin`.
2. Verify that the Region selector in the top-right corner displays **US East (N. Virginia) us-east-1**.
3. In the search bar at the top, type `S3` and select **S3** from the results.
4. Choose **Create bucket**.
5. Configure the following settings:
   - **Bucket type:** General purpose
   - **Bucket name:** `lab05-s3-<your-initials>-<random>` (for example, `lab05-s3-jd-83921`). Replace `<your-initials>` with your initials and `<random>` with a random number to ensure uniqueness.
   - **AWS Region:** US East (N. Virginia) us-east-1
6. Leave all other settings at their defaults for now. You will inspect them in later steps.
7. Choose **Create bucket**.

**Expected result:** The S3 Buckets page lists your new bucket. The bucket is empty and ready for use.

> **Tip:** If you receive a "Bucket name already exists" error, choose a different random number and try again. Bucket names must be globally unique across all AWS accounts.

**CLI equivalent:**

Open CloudShell and run the following commands. Replace `lab05-s3-jd-83921` with your chosen bucket name throughout the rest of this lab.

```bash
BUCKET_NAME="lab05-s3-jd-83921"
aws s3 mb s3://$BUCKET_NAME --region us-east-1
```

Expected output:

```
make_bucket: lab05-s3-jd-83921
```

Store the bucket name in a variable for use in subsequent commands:

```bash
echo "Bucket: $BUCKET_NAME"
```

### Step 2: Upload Files Using the Console and CLI

In this step, you upload objects to your bucket using both the AWS Management Console and the AWS CLI. This demonstrates the two most common ways to interact with S3.

**Upload via the Console:**

1. In the S3 console, choose your bucket name (`lab05-s3-<your-name>`) to open it.
2. Choose **Upload**.
3. Choose **Add files**.
4. Create a simple text file on your computer named `sample-file.txt` with the content `This is a sample file for Lab 05.` and select it for upload. Alternatively, you can drag and drop the file into the upload area.
5. Choose **Upload**.
6. Wait for the upload to complete, then choose **Close**.

**Expected result:** The bucket now contains one object: `sample-file.txt`.

**Upload via the CLI:**

7. In CloudShell, create a text file and upload it to the bucket:

```bash
echo "Hello from the AWS CLI" > hello.txt
aws s3 cp hello.txt s3://$BUCKET_NAME/hello.txt
```

Expected output:

```
upload: ./hello.txt to s3://lab05-s3-jd-83921/hello.txt
```

8. List the objects in the bucket to confirm both files are present:

```bash
aws s3 ls s3://$BUCKET_NAME/
```

Expected output:

```
2024-01-15 10:30:00         34 sample-file.txt
2024-01-15 10:31:00         23 hello.txt
```

9. Download the file you uploaded via the console to verify it:

```bash
aws s3 cp s3://$BUCKET_NAME/sample-file.txt downloaded-sample.txt
cat downloaded-sample.txt
```

Expected output:

```
This is a sample file for Lab 05.
```

> **Tip:** The `aws s3 cp` command works like a standard copy command. Use `aws s3 sync` to synchronize entire directories with an S3 prefix. Use `aws s3 mv` to move objects.

### Step 3: Enable Versioning and Work with Object Versions

[S3 Versioning](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html) keeps multiple versions of an object in the same bucket. When versioning is enabled, uploading an object with the same key creates a new version instead of overwriting the original. In this step, you enable versioning, upload a modified file, and view both versions.

**Enable versioning via the console:**

1. In the S3 console, choose your bucket name to open it.
2. Choose the **Properties** tab.
3. Scroll down to the **Bucket Versioning** section.
4. Choose **Edit**.
5. Select **Enable**.
6. Choose **Save changes**.

**Expected result:** The Bucket Versioning section now displays **Enabled**.

**CLI equivalent:**

```bash
aws s3api put-bucket-versioning \
    --bucket $BUCKET_NAME \
    --versioning-configuration Status=Enabled
```

Verify the versioning status:

```bash
aws s3api get-bucket-versioning --bucket $BUCKET_NAME
```

Expected output:

```json
{
    "Status": "Enabled"
}
```

**Upload a modified version of a file:**

7. In CloudShell, create an updated version of `hello.txt` and upload it:

```bash
echo "Hello from the AWS CLI - version 2 with updates" > hello.txt
aws s3 cp hello.txt s3://$BUCKET_NAME/hello.txt
```

Expected output:

```
upload: ./hello.txt to s3://lab05-s3-jd-83921/hello.txt
```

**View both versions in the console:**

8. In the S3 console, navigate to your bucket.
9. Choose the **Objects** tab.
10. Toggle the **Show versions** switch to **On** (located above the object list).

**Expected result:** You see two entries for `hello.txt`, each with a different **Version ID** and **Last modified** timestamp. The most recent upload is the **Current version**.

**View versions via the CLI:**

```bash
aws s3api list-object-versions \
    --bucket $BUCKET_NAME \
    --prefix hello.txt \
    --query "Versions[].{Key:Key,VersionId:VersionId,IsLatest:IsLatest,LastModified:LastModified,Size:Size}" \
    --output table
```

Expected output:

```
------------------------------------------------------------------------------------
|                              ListObjectVersions                                  |
+----------+-----------+---------------------------+------+------------------------+
| IsLatest |    Key    |       LastModified        | Size |       VersionId        |
+----------+-----------+---------------------------+------+------------------------+
|  True    | hello.txt | 2024-01-15T10:35:00.000Z  |  50  | abc123def456...        |
|  False   | hello.txt | 2024-01-15T10:31:00.000Z  |  23  | xyz789ghi012...        |
+----------+-----------+---------------------------+------+------------------------+
```

11. Download the previous version to confirm it still contains the original content:

```bash
OLD_VERSION_ID=$(aws s3api list-object-versions \
    --bucket $BUCKET_NAME \
    --prefix hello.txt \
    --query "Versions[?IsLatest==\`false\`].VersionId | [0]" \
    --output text)

aws s3api get-object \
    --bucket $BUCKET_NAME \
    --key hello.txt \
    --version-id $OLD_VERSION_ID \
    old-hello.txt

cat old-hello.txt
```

Expected output:

```
Hello from the AWS CLI
```

> **Warning:** Once you enable versioning on a bucket, you cannot return it to the unversioned state. You can only suspend versioning. All existing versions remain in the bucket and continue to incur storage costs until you explicitly delete them.

### Step 4: Set Up a Lifecycle Policy

[S3 Lifecycle policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html) automate the transition of objects between storage classes and the expiration of objects. In this step, you create a Lifecycle rule that transitions all objects to [S3 Standard-IA](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) after 30 days.

**Console:**

1. In the S3 console, choose your bucket name to open it.
2. Choose the **Management** tab.
3. Under **Lifecycle rules**, choose **Create lifecycle rule**.
4. Configure the following settings:
   - **Lifecycle rule name:** `TransitionToIA`
   - **Choose a rule scope:** Select **Apply to all objects in the bucket**
   - Select the checkbox **I acknowledge that this rule will apply to all objects in the bucket.**
5. Under **Lifecycle rule actions**, select **Transition current versions of objects between storage classes**.
6. Under **Transition current versions of objects between storage classes**, configure:
   - **Storage class transitions:** Choose **Standard-IA**
   - **Days after object creation:** `30`
7. Choose **Create rule**.

**Expected result:** The Management tab shows the `TransitionToIA` rule with status **Enabled**. Objects in this bucket will automatically transition to S3 Standard-IA 30 days after creation.

**CLI equivalent:**

```bash
aws s3api put-bucket-lifecycle-configuration \
    --bucket $BUCKET_NAME \
    --lifecycle-configuration '{
        "Rules": [
            {
                "ID": "TransitionToIA",
                "Status": "Enabled",
                "Filter": {},
                "Transitions": [
                    {
                        "Days": 30,
                        "StorageClass": "STANDARD_IA"
                    }
                ]
            }
        ]
    }'
```

Verify the Lifecycle configuration:

```bash
aws s3api get-bucket-lifecycle-configuration --bucket $BUCKET_NAME
```

Expected output:

```json
{
    "Rules": [
        {
            "ID": "TransitionToIA",
            "Status": "Enabled",
            "Filter": {},
            "Transitions": [
                {
                    "Days": 30,
                    "StorageClass": "STANDARD_IA"
                }
            ]
        }
    ]
}
```

> **Tip:** Lifecycle rules do not take effect immediately. S3 processes Lifecycle rules asynchronously, typically within 24 to 48 hours. For this lab, you are verifying that the rule is configured correctly. You will not see objects transition during the lab because the 30-day threshold has not been reached.

### Step 5: Verify Default Encryption (SSE-S3)

As of January 2023, Amazon S3 [automatically encrypts](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html) all new objects using server-side encryption with Amazon S3 managed keys (SSE-S3). In this step, you verify that default encryption is active on your bucket.

**Console:**

1. In the S3 console, choose your bucket name to open it.
2. Choose the **Properties** tab.
3. Scroll down to the **Default encryption** section.

**Expected result:** The Default encryption section displays:
- **Encryption type:** Server-side encryption with Amazon S3 managed keys (SSE-S3)
- **Bucket Key:** Enabled or Disabled (either is acceptable for SSE-S3)

**CLI equivalent:**

```bash
aws s3api get-bucket-encryption --bucket $BUCKET_NAME
```

Expected output:

```json
{
    "ServerSideEncryptionConfiguration": {
        "Rules": [
            {
                "ApplyServerSideEncryptionByDefault": {
                    "SSEAlgorithm": "AES256"
                },
                "BucketKeyEnabled": true
            }
        ]
    }
}
```

4. Verify that an uploaded object is encrypted by checking its metadata:

```bash
aws s3api head-object \
    --bucket $BUCKET_NAME \
    --key hello.txt
```

Expected output (partial):

```json
{
    "ContentType": "text/plain",
    "ContentLength": 50,
    "ServerSideEncryption": "AES256",
    "VersionId": "abc123def456..."
}
```

The `ServerSideEncryption` field confirms that the object is encrypted with SSE-S3 (AES-256). This encryption is applied automatically with no additional configuration required.

> **Tip:** SSE-S3 is the default and simplest encryption option. AWS manages the encryption keys entirely. For workloads that require an audit trail of key usage or fine-grained key access control, use SSE-KMS instead. You can change the default encryption method in the bucket properties at any time.

### Step 6: Verify Block Public Access Settings

[S3 Block Public Access](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html) is a set of four settings that prevent public access to your bucket, even if a bucket policy or ACL would otherwise allow it. AWS enables all four settings by default on new buckets. In this step, you verify that Block Public Access is active.

**Console:**

1. In the S3 console, choose your bucket name to open it.
2. Choose the **Permissions** tab.
3. Scroll to the **Block public access (bucket settings)** section.

**Expected result:** All four settings are **On**:
- Block public access to buckets and objects granted through new access control lists (ACLs)
- Block public access to buckets and objects granted through any access control lists (ACLs)
- Block public access to buckets and objects granted through new public bucket or access point policies
- Block public access to buckets and objects granted through any public bucket or access point policies

**CLI equivalent:**

```bash
aws s3api get-public-access-block --bucket $BUCKET_NAME
```

Expected output:

```json
{
    "PublicAccessBlockConfiguration": {
        "BlockPublicAcls": true,
        "IgnorePublicAcls": true,
        "BlockPublicPolicy": true,
        "RestrictPublicBuckets": true
    }
}
```

All four values are `true`, confirming that the bucket is protected from accidental public exposure.

> **Warning:** You will disable Block Public Access in the next step to enable static website hosting. In production, only disable these settings on buckets that are intentionally public (such as static website buckets). Keep Block Public Access enabled on all other buckets.

### Step 7: Host a Static Website on S3

[S3 static website hosting](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html) lets you serve HTML, CSS, JavaScript, and image files directly from an S3 bucket. In this step, you create a simple website with an index page and an error page, configure the bucket for static website hosting, add a bucket policy for public read access, and test the website in your browser.

**7a. Create the website files:**

1. In CloudShell, create an `index.html` file:

```bash
cat <<'EOF' > index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Lab 05 - S3 Static Website</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 50px auto;
            padding: 20px;
            background-color: #f5f5f5;
        }
        h1 { color: #232f3e; }
        p { color: #545b64; line-height: 1.6; }
        .info-box {
            background-color: #ffffff;
            border-left: 4px solid #ff9900;
            padding: 15px;
            margin: 20px 0;
        }
    </style>
</head>
<body>
    <h1>Hello from Amazon S3</h1>
    <p>This static website is hosted entirely on Amazon S3.</p>
    <div class="info-box">
        <p><strong>Module 05:</strong> Storage with Amazon S3</p>
        <p>You configured this bucket with versioning, a Lifecycle policy,
           SSE-S3 encryption, and static website hosting.</p>
    </div>
</body>
</html>
EOF
```

2. Create an `error.html` file:

```bash
cat <<'EOF' > error.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Not Found</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 50px auto;
            padding: 20px;
            text-align: center;
            background-color: #f5f5f5;
        }
        h1 { color: #d13212; }
        p { color: #545b64; }
    </style>
</head>
<body>
    <h1>404 - Page Not Found</h1>
    <p>The page you requested does not exist in this S3 bucket.</p>
    <p><a href="/">Return to the home page</a></p>
</body>
</html>
EOF
```

3. Upload both files to the bucket:

```bash
aws s3 cp index.html s3://$BUCKET_NAME/index.html --content-type "text/html"
aws s3 cp error.html s3://$BUCKET_NAME/error.html --content-type "text/html"
```

Expected output:

```
upload: ./index.html to s3://lab05-s3-jd-83921/index.html
upload: ./error.html to s3://lab05-s3-jd-83921/error.html
```

**7b. Enable static website hosting:**

4. In the S3 console, choose your bucket name to open it.
5. Choose the **Properties** tab.
6. Scroll down to the **Static website hosting** section.
7. Choose **Edit**.
8. Configure the following settings:
   - **Static website hosting:** Select **Enable**
   - **Hosting type:** Select **Host a static website**
   - **Index document:** `index.html`
   - **Error document:** `error.html`
9. Choose **Save changes**.

**Expected result:** The Static website hosting section now displays **Enabled** and shows the bucket website endpoint URL in the format:

```
http://lab05-s3-jd-83921.s3-website-us-east-1.amazonaws.com
```

Note this URL. You will use it to test the website.

**CLI equivalent:**

```bash
aws s3 website s3://$BUCKET_NAME \
    --index-document index.html \
    --error-document error.html
```

Construct the website endpoint URL:

```bash
WEBSITE_URL="http://$BUCKET_NAME.s3-website-us-east-1.amazonaws.com"
echo "Website URL: $WEBSITE_URL"
```

**7c. Disable Block Public Access for this bucket:**

Static website hosting requires public read access. You must disable Block Public Access before adding a public bucket policy.

10. In the S3 console, choose the **Permissions** tab.
11. Under **Block public access (bucket settings)**, choose **Edit**.
12. Clear (uncheck) the **Block all public access** checkbox. This disables all four Block Public Access settings.
13. Choose **Save changes**.
14. In the confirmation dialog, type `confirm` and choose **Confirm**.

**Expected result:** The Block public access section shows all four settings as **Off**.

**CLI equivalent:**

```bash
aws s3api put-public-access-block \
    --bucket $BUCKET_NAME \
    --public-access-block-configuration \
        "BlockPublicAcls=false,IgnorePublicAcls=false,BlockPublicPolicy=false,RestrictPublicBuckets=false"
```

> **Warning:** Disabling Block Public Access makes it possible for bucket policies or ACLs to grant public access. Only do this for buckets that are intentionally public, such as static website buckets. Re-enable these settings or delete the bucket when you are finished.

**7d. Add a bucket policy for public read access:**

15. On the **Permissions** tab, scroll down to the **Bucket policy** section.
16. Choose **Edit**.
17. Paste the following [bucket policy](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteAccessPermissionsReqd.html). Replace `YOUR-BUCKET-NAME` with your actual bucket name:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::YOUR-BUCKET-NAME/*"
        }
    ]
}
```

For example, if your bucket name is `lab05-s3-jd-83921`, the Resource line should be:

```
"Resource": "arn:aws:s3:::lab05-s3-jd-83921/*"
```

18. Choose **Save changes**.

**Expected result:** The bucket policy is saved. A warning banner may appear indicating that the bucket has public access. This is expected for a static website bucket.

**CLI equivalent:**

```bash
aws s3api put-bucket-policy \
    --bucket $BUCKET_NAME \
    --policy "{
        \"Version\": \"2012-10-17\",
        \"Statement\": [
            {
                \"Sid\": \"PublicReadGetObject\",
                \"Effect\": \"Allow\",
                \"Principal\": \"*\",
                \"Action\": \"s3:GetObject\",
                \"Resource\": \"arn:aws:s3:::$BUCKET_NAME/*\"
            }
        ]
    }"
```

**7e. Test the static website:**

19. Open a new browser tab and navigate to the website endpoint URL you noted earlier:

```
http://lab05-s3-jd-83921.s3-website-us-east-1.amazonaws.com
```

**Expected result:** The browser displays your index page with the heading "Hello from Amazon S3" and the orange-bordered info box.

20. Test the error page by navigating to a URL that does not exist:

```
http://lab05-s3-jd-83921.s3-website-us-east-1.amazonaws.com/nonexistent-page
```

**Expected result:** The browser displays your custom 404 page with the heading "404 - Page Not Found".

21. Verify from the CLI:

```bash
curl $WEBSITE_URL
```

Expected output (partial):

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    ...
    <title>Lab 05 - S3 Static Website</title>
    ...
</head>
<body>
    <h1>Hello from Amazon S3</h1>
    ...
</body>
</html>
```

> **Tip:** S3 website endpoints use HTTP, not HTTPS. To serve a static website over HTTPS with a custom domain, place an [Amazon CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html) distribution in front of the S3 bucket. You will learn about CloudFront in Module 07.

## Validation

Confirm the following:

- [ ] An S3 bucket with your chosen name exists in `us-east-1`
- [ ] The bucket contains at least four objects: `sample-file.txt`, `hello.txt`, `index.html`, `error.html`
- [ ] Versioning is enabled on the bucket (`aws s3api get-bucket-versioning` returns `"Status": "Enabled"`)
- [ ] Two versions of `hello.txt` exist (visible with **Show versions** toggle or `list-object-versions`)
- [ ] A Lifecycle rule named `TransitionToIA` is configured to transition objects to Standard-IA after 30 days
- [ ] Default encryption is SSE-S3 (AES-256), confirmed by `get-bucket-encryption`
- [ ] The static website endpoint URL loads the index page in a browser
- [ ] Navigating to a nonexistent path on the website endpoint displays the custom error page

You can run the following commands to verify key configurations:

```bash
echo "--- Versioning ---"
aws s3api get-bucket-versioning --bucket $BUCKET_NAME

echo "--- Lifecycle ---"
aws s3api get-bucket-lifecycle-configuration --bucket $BUCKET_NAME

echo "--- Encryption ---"
aws s3api get-bucket-encryption --bucket $BUCKET_NAME

echo "--- Website ---"
aws s3api get-bucket-website --bucket $BUCKET_NAME

echo "--- Objects ---"
aws s3 ls s3://$BUCKET_NAME/
```

## Cleanup

Delete all resources created in this lab to avoid unexpected charges. S3 buckets must be empty before they can be deleted. Because versioning is enabled, you must delete all object versions and delete markers.

> **Warning:** Failing to clean up may result in ongoing storage charges. Versioned buckets retain all previous versions of every object until you explicitly delete them.

**1. Remove the bucket policy:**

```bash
aws s3api delete-bucket-policy --bucket $BUCKET_NAME
```

**2. Re-enable Block Public Access:**

```bash
aws s3api put-public-access-block \
    --bucket $BUCKET_NAME \
    --public-access-block-configuration \
        "BlockPublicAcls=true,IgnorePublicAcls=true,BlockPublicPolicy=true,RestrictPublicBuckets=true"
```

**3. Delete all object versions and delete markers:**

The following command deletes all object versions in the bucket. This is required because `aws s3 rm` does not remove noncurrent versions.

```bash
aws s3api list-object-versions \
    --bucket $BUCKET_NAME \
    --query "{Objects: Versions[].{Key:Key,VersionId:VersionId}}" \
    --output json | \
aws s3api delete-objects \
    --bucket $BUCKET_NAME \
    --delete file:///dev/stdin
```

If there are delete markers, remove them as well:

```bash
aws s3api list-object-versions \
    --bucket $BUCKET_NAME \
    --query "{Objects: DeleteMarkers[].{Key:Key,VersionId:VersionId}}" \
    --output json | \
aws s3api delete-objects \
    --bucket $BUCKET_NAME \
    --delete file:///dev/stdin
```

> **Tip:** If the commands above return an error about empty input, it means there are no objects or delete markers remaining. You can proceed to the next step.

**4. Delete the bucket:**

```bash
aws s3 rb s3://$BUCKET_NAME
```

Expected output:

```
remove_bucket: lab05-s3-jd-83921
```

**Console alternative:** You can also delete the bucket from the S3 console. Select the bucket, choose **Delete**, type the bucket name to confirm, and choose **Delete bucket**. The console prompts you to empty the bucket first if it is not already empty. Choose **Empty bucket**, confirm by typing `permanently delete`, then delete the bucket.

**5. Verify cleanup is complete:**

```bash
aws s3 ls s3://$BUCKET_NAME 2>&1
```

Expected output:

```
An error occurred (NoSuchBucket) when calling the ListObjectsV2 operation: The specified bucket does not exist
```

This confirms the bucket has been deleted.

## Challenge (Optional)

Using only concepts from Modules 01 through 05, complete the following:

1. Create a new S3 bucket and configure a Lifecycle rule with multiple transitions: transition objects to S3 Standard-IA after 30 days, then to S3 Glacier Flexible Retrieval after 90 days, and expire (delete) objects after 365 days. Verify the rule configuration using the CLI. This demonstrates how Lifecycle policies can chain multiple transitions to optimize long-term storage costs.

2. Enable [S3 server access logging](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerLogs.html) on your website bucket. Create a second bucket to store the access logs. Configure the website bucket to deliver access logs to the logging bucket with a prefix of `logs/`. Visit the website a few times, wait 10 to 15 minutes, and check the logging bucket for log files. This demonstrates how to audit access to your S3 resources.

3. Upload a file using SSE-KMS encryption instead of the default SSE-S3. Use the AWS-managed KMS key (`aws/s3`) for simplicity. Compare the metadata of an SSE-S3 encrypted object and an SSE-KMS encrypted object using `aws s3api head-object`. Note the differences in the `ServerSideEncryption` and `SSEKMSKeyId` fields.

> **Tip:** Remember to delete all resources created during the challenge (additional buckets, KMS-encrypted objects, logging configurations) to avoid charges.

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
