# Module 05: Storage with Amazon Simple Storage Service (Amazon S3)

## Learning Objectives

By the end of this module, you will be able to:

- Demonstrate how Amazon S3 stores data as objects in buckets, and use keys and prefixes to organize objects
- Configure S3 bucket settings including versioning, default encryption, and Block Public Access
- Implement S3 Lifecycle policies to transition objects between storage classes and expire objects automatically
- Use S3 storage classes to optimize costs based on data access patterns
- Set up access control for S3 buckets using bucket policies, IAM policies, and Block Public Access settings
- Deploy a static website hosted on Amazon S3 with an index document and error document
- Demonstrate the differences between S3 server-side encryption options (SSE-S3, SSE-KMS, SSE-C) and configure default encryption for a bucket

## Prerequisites

- Completion of [Module 02: Identity and Access Management (IAM) and Security](../02-iam-and-security/README.md) (IAM policies, bucket policies, and the principle of least privilege for controlling access to S3 resources)
- Completion of [Module 04: Compute with Amazon EC2](../04-compute-ec2/README.md) (EC2 instances and IAM instance profiles for accessing S3 from compute resources)
- An AWS account with console access (free tier is sufficient)

## Concepts

### S3 Overview: Object Storage Fundamentals

[Amazon Simple Storage Service (Amazon S3)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html) stores data as objects in buckets, with virtually unlimited capacity and 99.999999999% (eleven nines) durability. Unlike block storage (Amazon EBS from Module 04), you do not format or mount S3 as a file system. Instead, you interact with objects through the S3 API, the AWS Management Console, or the AWS CLI.

S3 fits a wide range of use cases: backup and restore, data archiving, content distribution, data lakes for analytics, and hosting static websites. You pay only for the storage you consume, with no minimum commitment.

#### Buckets

A [bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingBucket.html) is a container for objects in Amazon S3. Every object lives inside a bucket. Bucket names are globally unique across all AWS accounts and all Regions, so once you claim a name, nobody else can use it until you delete the bucket.

Key characteristics of buckets:

- You create a bucket in a specific AWS Region. Objects stored in the bucket remain in that Region unless you explicitly replicate them to another Region.
- Each AWS account can create up to 100 buckets by default (you can request an increase).
- Bucket names must be between 3 and 63 characters, contain only lowercase letters, numbers, and hyphens, and must start with a letter or number.

```bash
aws s3 mb s3://my-example-bucket-2024 --region us-east-1
```

Expected output:

```
make_bucket: my-example-bucket-2024
```

#### Objects, Keys, and Prefixes

An object consists of the data (the file itself), metadata (information about the file such as content type and last modified date), and a key. The [key](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-keys.html) is the unique identifier for the object within a bucket. The combination of bucket name, key, and optionally a version ID uniquely identifies every object in S3.

For example, if you store a file at `s3://my-bucket/images/photo.jpg`, the bucket is `my-bucket` and the key is `images/photo.jpg`.

S3 has a flat structure with no actual directories or folders. However, you can use prefixes and delimiters to simulate a folder hierarchy. A prefix is the portion of the key before the last delimiter (typically `/`). In the key `images/photo.jpg`, the prefix is `images/`. The S3 console displays prefixes as folders for convenience, but the underlying storage is flat.

```bash
aws s3 ls s3://my-example-bucket-2024/images/
```

Expected output:

```
2024-01-15 10:30:00       2048 photo.jpg
2024-01-15 10:31:00       4096 logo.png
```

Individual objects can be up to 5 terabytes (TB) in size. For objects larger than 100 megabytes (MB), AWS recommends using [multipart upload](https://docs.aws.amazon.com/AmazonS3/latest/userguide/mpuoverview.html) to improve upload performance and reliability.

> **Tip:** S3 provides strong read-after-write consistency for all operations. After a successful PUT or DELETE, any subsequent read request immediately returns the latest version of the object.


### Storage Classes

Amazon S3 offers multiple [storage classes](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) optimized for different access patterns and cost requirements. All classes deliver the same eleven-nines durability, but they differ in availability, retrieval speed, and price. Picking the right class for each dataset is one of the simplest ways to cut your AWS bill.

#### Storage Class Comparison

| Storage Class | Designed For | Availability | Min Storage Duration | Retrieval Fee | Use Cases |
|---------------|-------------|-------------|---------------------|---------------|-----------|
| [S3 Standard](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) | Frequently accessed data | 99.99% | None | None | Active application data, content distribution, data analytics |
| [S3 Intelligent-Tiering](https://docs.aws.amazon.com/AmazonS3/latest/userguide/intelligent-tiering.html) | Data with changing or unknown access patterns | 99.9% | None | None | Unpredictable workloads, data lakes with mixed access patterns |
| [S3 Standard-IA](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) | Infrequently accessed data | 99.9% | 30 days | Per-GB retrieval fee | Backups, disaster recovery copies, older data accessed occasionally |
| [S3 One Zone-IA](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html) | Infrequently accessed, non-critical data | 99.5% | 30 days | Per-GB retrieval fee | Secondary backups, easily reproducible data |
| [S3 Glacier Instant Retrieval](https://docs.aws.amazon.com/AmazonS3/latest/userguide/glacier-storage-classes.html) | Archive data needing millisecond access | 99.9% | 90 days | Per-GB retrieval fee | Medical images, news media archives, user-generated content archives |
| [S3 Glacier Flexible Retrieval](https://docs.aws.amazon.com/AmazonS3/latest/userguide/glacier-storage-classes.html) | Archive data accessed 1-2 times per year | 99.99% | 90 days | Per-GB retrieval fee | Long-term backups, compliance archives (retrieval in minutes to hours) |
| [S3 Glacier Deep Archive](https://docs.aws.amazon.com/AmazonS3/latest/userguide/glacier-storage-classes.html) | Long-term archive, rarely accessed | 99.99% | 180 days | Per-GB retrieval fee | Regulatory archives, digital preservation (retrieval in 12-48 hours) |

#### S3 Intelligent-Tiering

[S3 Intelligent-Tiering](https://docs.aws.amazon.com/AmazonS3/latest/userguide/intelligent-tiering.html) is unique because it monitors access patterns and moves objects between tiers automatically. Objects untouched for 30 days shift to an infrequent access tier; after 90 days, they move to an archive instant access tier. When accessed again, they return to the frequent access tier. There are no retrieval fees, just a small monthly monitoring charge per object.

Intelligent-Tiering works well as a default when you cannot predict how often data will be accessed.

#### Choosing a Storage Class

Start with these guidelines:

- **Active, frequently accessed data:** Use S3 Standard.
- **Unknown or changing access patterns:** Use S3 Intelligent-Tiering.
- **Infrequent access (but needs fast retrieval):** Use S3 Standard-IA or S3 One Zone-IA.
- **Archive data (accessed rarely):** Use S3 Glacier Instant Retrieval, Glacier Flexible Retrieval, or Glacier Deep Archive depending on how quickly you need to retrieve the data.

> **Tip:** S3 One Zone-IA stores data in a single Availability Zone, which makes it less expensive than Standard-IA. However, if that AZ is destroyed, the data is lost. Use it only for data you can recreate or for secondary copies of data that exists elsewhere.


### Versioning

[S3 Versioning](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html) keeps multiple copies of an object in the same bucket. When versioning is enabled, uploading an object with an existing key creates a new version rather than overwriting the original. This protects you from accidental deletions and overwrites, which is why you should enable it on any bucket storing data you cannot afford to lose.

#### How Versioning Works

A bucket can be in one of three states:

1. **Unversioned (default).** Versioning has never been enabled. Objects are stored without version IDs.
2. **Versioning-enabled.** Every object receives a unique version ID. Uploading an object with an existing key creates a new version. Deleting an object inserts a delete marker instead of permanently removing the object.
3. **Versioning-suspended.** New objects are stored with a `null` version ID, but existing versioned objects are preserved. Suspending versioning does not delete previous versions.

> **Warning:** Once you enable versioning on a bucket, you cannot return it to the unversioned state. You can only suspend versioning. All existing versions remain in the bucket and continue to incur storage costs until you explicitly delete them.

#### Enabling Versioning

You can enable versioning through the console or the AWS CLI:

```bash
aws s3api put-bucket-versioning \
    --bucket my-example-bucket-2024 \
    --versioning-configuration Status=Enabled
```

To verify the versioning status:

```bash
aws s3api get-bucket-versioning --bucket my-example-bucket-2024
```

Expected output:

```json
{
    "Status": "Enabled"
}
```

#### Delete Markers and Recovering Objects

When you delete an object in a versioning-enabled bucket, S3 does not permanently remove the object. Instead, it inserts a delete marker, which becomes the current version of the object. The previous versions remain in the bucket. To recover a deleted object, you delete the delete marker or retrieve a specific previous version by its version ID.

#### MFA Delete

[MFA delete](https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiFactorAuthenticationDelete.html) adds an extra layer of protection to versioned buckets. When MFA delete is enabled, two actions require authentication with a Multi-Factor Authentication (MFA) device:

- Permanently deleting an object version
- Changing the versioning state of the bucket (enabling or suspending)

Only the bucket owner (the root account) can enable MFA delete. This prevents even administrators with full S3 permissions from accidentally or maliciously deleting object versions without physical MFA device access.

> **Tip:** Enable versioning on any bucket that stores data you cannot afford to lose. Combine versioning with Lifecycle policies (covered in the next section) to automatically expire old versions and control storage costs.


### Lifecycle Policies

[S3 Lifecycle policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html) let you define rules that automatically move objects between storage classes or delete them after a specified period. They are your primary tool for automating storage cost optimization without manual intervention.

#### Lifecycle Actions

A Lifecycle rule can perform two types of actions:

- **Transition actions.** Move objects to a different storage class after a specified number of days. For example, transition objects from S3 Standard to S3 Standard-IA after 30 days, then to S3 Glacier Flexible Retrieval after 90 days.
- **Expiration actions.** Permanently delete objects after a specified number of days. For versioning-enabled buckets, you can also expire noncurrent versions or remove expired delete markers.

#### Supported Transitions

Not all storage class transitions are supported. Objects can only move in a [downward direction](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html) through the storage class hierarchy:

```
S3 Standard
    --> S3 Intelligent-Tiering
    --> S3 Standard-IA / S3 One Zone-IA
        --> S3 Glacier Instant Retrieval
            --> S3 Glacier Flexible Retrieval
                --> S3 Glacier Deep Archive
```

You cannot use a Lifecycle rule to transition objects from a lower-cost class back to a higher-cost class (for example, from Glacier back to Standard). To move an object back, you must copy it manually.

#### Lifecycle Rule Configuration

Each [Lifecycle rule](https://docs.aws.amazon.com/AmazonS3/latest/userguide/intro-lifecycle-rules.html) includes:

- **A filter.** Determines which objects the rule applies to. You can filter by prefix (for example, `logs/`), object tag, object size, or apply the rule to all objects in the bucket.
- **One or more actions.** Transition actions, expiration actions, or both.
- **A status.** Enabled or Disabled. You can disable a rule temporarily without deleting it.

Here is an example Lifecycle configuration in JSON that transitions objects with the prefix `logs/` to Standard-IA after 30 days and expires them after 365 days:

```json
{
    "Rules": [
        {
            "ID": "ArchiveAndExpireLogs",
            "Filter": {
                "Prefix": "logs/"
            },
            "Status": "Enabled",
            "Transitions": [
                {
                    "Days": 30,
                    "StorageClass": "STANDARD_IA"
                },
                {
                    "Days": 90,
                    "StorageClass": "GLACIER"
                }
            ],
            "Expiration": {
                "Days": 365
            }
        }
    ]
}
```

> **Tip:** For versioning-enabled buckets, add a `NoncurrentVersionExpiration` action to automatically delete old versions after a set number of days. This prevents storage costs from growing indefinitely as new versions accumulate.


### Access Control

Controlling who can access your S3 buckets and objects is critical for security. S3 provides several mechanisms for managing access, and understanding when to use each one is essential. In Module 02, you learned about [IAM policies](../02-iam-and-security/README.md) and the principle of least privilege. Those same concepts apply directly to S3 access control.

#### Bucket Policies

A [bucket policy](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html) is a resource-based JSON policy that you attach directly to an S3 bucket. Bucket policies can grant or deny access to the bucket and its objects for specific AWS accounts, IAM users, IAM roles, or even anonymous (public) users.

Bucket policies use the same JSON structure you learned in Module 02 (Effect, Action, Resource, Condition), with the addition of a Principal element that specifies who the policy applies to.

Here is an example bucket policy that grants read-only access to all objects in a bucket for any user (public read):

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::my-website-bucket/*"
        }
    ]
}
```

> **Warning:** Granting `"Principal": "*"` makes the bucket publicly accessible. Only use this for intentionally public content such as static website assets. For all other buckets, restrict access to specific IAM principals.

#### IAM Policies vs. Bucket Policies

Both IAM policies and bucket policies control access to S3, but they work from different perspectives:

| Feature | IAM Policy | Bucket Policy |
|---------|-----------|---------------|
| Attached to | IAM user, group, or role | S3 bucket |
| Controls access for | A specific identity across all AWS services | Any principal accessing a specific bucket |
| Cross-account access | Requires role assumption | Can grant access directly to external accounts |
| Use case | Controlling what a user or role can do | Controlling who can access a bucket |

In practice, you often use both together. An IAM policy might grant a role permission to access S3, while a bucket policy restricts which buckets that role can access. Remember from Module 02 that both the IAM policy and the bucket policy must allow the action for the request to succeed (unless one explicitly denies it).

#### Access Control Lists (ACLs)

[Access Control Lists (ACLs)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/acl-overview.html) are a legacy access control mechanism in S3. ACLs predate IAM and bucket policies. AWS recommends disabling ACLs for most use cases and using bucket policies and IAM policies instead.

As of April 2023, new S3 buckets have the [Bucket owner enforced](https://docs.aws.amazon.com/AmazonS3/latest/userguide/about-object-ownership.html) setting enabled by default, which disables ACLs. All access control is then managed through policies.

#### Block Public Access

[S3 Block Public Access](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html) is a set of four settings that override any bucket policy or ACL that would otherwise grant public access. Think of it as a safety net: even if someone accidentally attaches a permissive policy, Block Public Access prevents exposure.

The four settings are:

1. **BlockPublicAcls.** Rejects any PUT request that includes a public ACL.
2. **IgnorePublicAcls.** Ignores all public ACLs on the bucket and its objects.
3. **BlockPublicPolicy.** Rejects any bucket policy that grants public access.
4. **RestrictPublicBuckets.** Restricts access to buckets with public policies to only AWS service principals and authorized users.

AWS enables all four settings by default on new buckets and at the account level. You must explicitly disable them if you need public access (for example, for a static website).

> **Tip:** Enable Block Public Access at the account level as a guardrail. This prevents any bucket in the account from being made public, even if someone attaches a permissive bucket policy. Disable it only on specific buckets that require public access.

#### S3 Access Points

[S3 Access Points](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-points.html) simplify managing access to shared datasets. An access point is a named network endpoint attached to a bucket, with its own access point policy and network controls. Instead of writing a single complex bucket policy with dozens of conditions, you create separate access points for different applications or teams, each with its own permissions.

For example, you might create one access point for a data analytics team with read-only access to the `analytics/` prefix, and another access point for an application with read-write access to the `app-data/` prefix. Each access point has its own Amazon Resource Name (ARN) and can be restricted to a specific Virtual Private Cloud (VPC) for network isolation.

In Module 04, you learned about [IAM roles for EC2 instances](../04-compute-ec2/README.md). When an EC2 instance needs to access S3, you attach an IAM role with an instance profile. The role's IAM policy grants the instance permission to perform S3 operations, and the bucket policy (or access point policy) controls which buckets and prefixes the instance can access. This combination of IAM roles and S3 policies follows the principle of least privilege you learned in Module 02.


### Encryption

Amazon S3 provides several options for encrypting data at rest. As of January 2023, S3 automatically encrypts all new objects using server-side encryption with Amazon S3 managed keys (SSE-S3) by default. You can choose a different encryption method based on your key management requirements.

#### Server-Side Encryption Options

S3 supports three server-side encryption methods. With server-side encryption, S3 encrypts the object after receiving it and decrypts it when you retrieve it. The encryption and decryption are transparent to the client.

| Encryption Method | Key Management | Key Storage | Cost | Use Case |
|-------------------|---------------|-------------|------|----------|
| [SSE-S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingServerSideEncryption.html) | AWS manages keys entirely | S3 internal | No additional cost | Default encryption; no key management overhead |
| [SSE-KMS](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingKMSEncryption.html) | AWS Key Management Service (KMS) | AWS KMS | KMS key usage fees | Audit trail for key usage; fine-grained key access control; regulatory compliance |
| [SSE-C](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerSideEncryptionCustomerKeys.html) | Customer provides keys with each request | Customer managed | No S3 encryption cost | Full customer control over encryption keys |

**SSE-S3** uses AES-256 encryption. AWS handles key generation, management, and rotation. This is the simplest option and is enabled by default on all buckets.

**SSE-KMS** uses [AWS Key Management Service (AWS KMS)](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html) keys. SSE-KMS provides additional benefits over SSE-S3: you can create and manage your own KMS keys, control who can use the key through IAM policies, and audit key usage through AWS CloudTrail. SSE-KMS also supports [S3 Bucket Keys](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-key.html), which reduce the cost of SSE-KMS by decreasing the number of requests to AWS KMS.

**SSE-C** requires you to provide the encryption key with every PUT and GET request. S3 uses your key to encrypt or decrypt the object but does not store the key. You are responsible for managing, rotating, and tracking which key was used for each object. SSE-C requires HTTPS for all requests.

> **Warning:** With SSE-C, if you lose the encryption key, you permanently lose access to the encrypted object. S3 does not store a copy of your key.

#### Client-Side Encryption

With client-side encryption, you encrypt the data before sending it to S3 and decrypt it after downloading. S3 stores the encrypted object without any knowledge of the encryption key or algorithm. Client-side encryption gives you complete control over the encryption process but requires you to manage the encryption logic in your application.

#### Configuring Default Encryption

You can set the [default encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-encryption.html) for a bucket so that all new objects are automatically encrypted with your chosen method:

```bash
aws s3api put-bucket-encryption \
    --bucket my-example-bucket-2024 \
    --server-side-encryption-configuration '{
        "Rules": [
            {
                "ApplyServerSideEncryptionByDefault": {
                    "SSEAlgorithm": "aws:kms",
                    "KMSMasterKeyID": "alias/my-s3-key"
                },
                "BucketKeyEnabled": true
            }
        ]
    }'
```

> **Tip:** For most workloads, SSE-S3 (the default) provides sufficient encryption. Use SSE-KMS when you need an audit trail of key usage, fine-grained access control over the encryption key, or when compliance requirements mandate customer-managed keys.


### S3 Static Website Hosting

Amazon S3 can host [static websites](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html) directly from a bucket. A static website serves fixed content (HTML, CSS, JavaScript, images) without any server-side processing. This is a simple, low-cost option for landing pages, documentation sites, and single-page applications that do not need a backend server.

#### Configuration

To host a static website on S3, you need to:

1. **Create a bucket** with a name that matches your intended domain (optional but recommended if using a custom domain).
2. **Enable static website hosting** on the bucket and specify an index document (the default page, typically `index.html`) and an optional error document (displayed for 404 errors, typically `error.html`).
3. **Disable Block Public Access** settings on the bucket (since website visitors need public read access).
4. **Add a bucket policy** that grants `s3:GetObject` permission to all users (`"Principal": "*"`).
5. **Upload your website files** to the bucket.

After enabling static website hosting, S3 provides a website endpoint URL in the format:

```
http://<bucket-name>.s3-website-<region>.amazonaws.com
```

For example: `http://my-website-bucket.s3-website-us-east-1.amazonaws.com`

#### Index and Error Documents

The [index document](https://docs.aws.amazon.com/AmazonS3/latest/userguide/IndexDocumentSupport.html) is the page S3 returns when a user navigates to the root URL or a directory path. If a user requests `http://my-site.s3-website-us-east-1.amazonaws.com/`, S3 returns the `index.html` object.

The [error document](https://docs.aws.amazon.com/AmazonS3/latest/userguide/CustomErrorDocSupport.html) is the page S3 returns when a requested object does not exist (HTTP 404). Without an error document, S3 returns a default XML error response.

```bash
aws s3 website s3://my-website-bucket \
    --index-document index.html \
    --error-document error.html
```

> **Warning:** S3 website endpoints use HTTP, not HTTPS. To serve your static website over HTTPS, place an [Amazon CloudFront](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html) distribution in front of the S3 bucket. CloudFront provides HTTPS support, caching at edge locations, and a custom domain name with an SSL/TLS certificate.

> **Tip:** S3 static website hosting is covered by the AWS Free Tier for the first 12 months (within the S3 free tier limits of 5 GB storage, 20,000 GET requests, and 2,000 PUT requests per month).


### Cross-Region Replication and Transfer Acceleration

Amazon S3 provides features for managing data across geographic locations and optimizing transfer speeds. These features are important for global applications, disaster recovery, and compliance requirements.

#### Cross-Region Replication (CRR)

[S3 Replication](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication.html) automatically copies objects from one bucket to another, either across Regions (Cross-Region Replication, CRR) or within the same Region (Same-Region Replication, SRR). This runs asynchronously in the background.

Common use cases for CRR include:

- **Compliance.** Regulations may require you to store copies of data in a geographically distant location.
- **Latency reduction.** Replicate data closer to users in different Regions for faster access.
- **Disaster recovery.** Maintain a copy of critical data in a separate Region in case of a regional outage.

CRR requires versioning to be enabled on both the source and destination buckets. You configure replication rules that specify which objects to replicate (by prefix or tag filter) and the destination bucket.

#### Transfer Acceleration

[S3 Transfer Acceleration](https://docs.aws.amazon.com/AmazonS3/latest/userguide/transfer-acceleration.html) speeds up uploads by routing data through the nearest CloudFront edge location, then moving it to the S3 bucket over the optimized AWS backbone network. This is most useful when uploading over long geographic distances (for example, from Asia to a bucket in `us-east-1`). It does not affect downloads.

To use Transfer Acceleration, you enable it on the bucket and then use the accelerate endpoint for uploads:

```
https://<bucket-name>.s3-accelerate.amazonaws.com
```

> **Tip:** Use the [S3 Transfer Acceleration Speed Comparison tool](https://docs.aws.amazon.com/AmazonS3/latest/userguide/transfer-acceleration-speed-comparison.html) to test whether Transfer Acceleration improves upload speeds from your location before enabling it.

## Instructor Notes

**Estimated lecture time:** 90 minutes

**Common student questions:**

- Q: What is the difference between S3 and EBS?
  A: Amazon S3 is object storage accessed through an API. You store and retrieve entire objects (files) by key. Amazon EBS is block storage that you attach to a single EC2 instance and use as a file system. S3 is ideal for storing files, backups, and static content. EBS is ideal for operating system volumes and databases that need low-latency, block-level access. You learned about EBS in [Module 04](../04-compute-ec2/README.md). See the [S3 overview](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html) for details.

- Q: If S3 has eleven-nines durability, do I still need backups?
  A: S3 durability protects against hardware failures within AWS. It does not protect against accidental deletion, application bugs that overwrite data, or malicious actions. Enable versioning to protect against accidental overwrites and deletions. For critical data, consider Cross-Region Replication for geographic redundancy. See the [S3 Versioning documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html) for details.

- Q: When should I use a bucket policy versus an IAM policy for S3 access?
  A: Use IAM policies when you want to control what a specific user or role can do across multiple AWS services. Use bucket policies when you want to control who can access a specific bucket, especially for cross-account access or public access. In many cases, you use both together. The request must be allowed by both the IAM policy and the bucket policy. See the [bucket policy examples](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html) for common patterns.

- Q: Why are ACLs deprecated if they still exist?
  A: ACLs were the original access control mechanism in S3, created before IAM existed. They are limited in functionality compared to bucket policies and IAM policies (for example, ACLs cannot use conditions). AWS now recommends the Bucket owner enforced setting, which disables ACLs entirely. New buckets have ACLs disabled by default. See the [Object Ownership documentation](https://docs.aws.amazon.com/AmazonS3/latest/userguide/about-object-ownership.html) for details.

**Teaching tips:**

- Start by connecting S3 to the IAM concepts from Module 02. Remind students that bucket policies use the same JSON structure (Effect, Action, Resource, Condition) they learned for IAM policies, with the addition of a Principal element. Display a bucket policy and an IAM policy side by side to highlight the similarities.
- When explaining storage classes, use the analogy of a filing system: S3 Standard is the desk drawer (instant access, most expensive per unit), Standard-IA is the filing cabinet across the room (slightly slower, cheaper), and Glacier Deep Archive is the offsite warehouse (cheapest, but retrieval takes hours). Walk through the comparison table and ask students to classify sample workloads.
- For versioning, demonstrate the concept by uploading a file, modifying it, uploading again, then showing both versions in the console. Delete the object and show the delete marker. Then recover the object by deleting the delete marker. This hands-on demonstration makes the concept concrete.
- When covering encryption, emphasize that SSE-S3 is the default and sufficient for most use cases. Students often overcomplicate encryption choices. Focus on when SSE-KMS adds value (audit trail, key access control) rather than memorizing all options.

**Pause points:**

- After S3 overview: ask students to explain the difference between a key and a prefix, and why S3 does not have real folders.
- After storage classes: present a scenario (for example, "You have application logs that are accessed daily for the first week, then rarely after that") and ask students to recommend a storage class strategy using Lifecycle policies.
- After access control: display a bucket policy and ask students to identify who has access, what actions are allowed, and what would happen if Block Public Access were enabled.
- After encryption: ask students when they would choose SSE-KMS over SSE-S3 (answer: when they need an audit trail of key usage or fine-grained control over who can use the encryption key).

## Key Takeaways

- Amazon S3 is object storage with virtually unlimited capacity and eleven-nines durability. Objects are stored in buckets and identified by keys, with prefixes simulating a folder structure.
- S3 storage classes let you optimize costs by matching storage tier to access patterns. Use Lifecycle policies to automate transitions between classes and expire objects you no longer need.
- Enable versioning on buckets that store important data to protect against accidental deletion and overwrites. Combine versioning with Lifecycle rules to manage storage costs for old versions.
- Control access to S3 using bucket policies and IAM policies together, and keep Block Public Access enabled at the account level as a safety net. ACLs are a legacy mechanism and should be disabled on new buckets.
- S3 encrypts all new objects by default with SSE-S3. Use SSE-KMS when you need audit logging, key access control, or compliance with regulations that require customer-managed keys.
---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
