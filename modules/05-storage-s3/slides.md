---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 05: Storage with Amazon S3'
---

# Module 05: Storage with Amazon S3

**Phase 2: Core Services**
Estimated lecture time: 90 minutes

<!-- Speaker notes: Welcome to Module 05. This module covers S3 object storage, one of the most widely used AWS services. Breakdown: 10 min S3 overview, 15 min storage classes, 10 min versioning, 10 min lifecycle policies, 15 min access control, 10 min encryption, 10 min static website hosting, 10 min replication and Q&A. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Demonstrate how S3 stores data as objects in buckets using keys and prefixes
- Configure bucket settings including versioning, encryption, and Block Public Access
- Implement Lifecycle policies to transition objects between storage classes
- Use S3 storage classes to optimize costs based on access patterns
- Set up access control using bucket policies, IAM policies, and Block Public Access
- Deploy a static website hosted on Amazon S3
- Demonstrate the differences between S3 encryption options (SSE-S3, SSE-KMS, SSE-C)

---

## Prerequisites and agenda

**Prerequisites:** Module 02 (IAM policies), Module 04 (EC2 and IAM instance profiles), AWS account

**Agenda:**
1. S3 overview: object storage fundamentals
2. Storage classes
3. Versioning
4. Lifecycle policies
5. Access control
6. Encryption
7. Static website hosting
8. Cross-Region Replication and Transfer Acceleration

---

# S3 overview

<!-- Speaker notes: This section takes approximately 10 minutes. Start by contrasting S3 (object storage) with EBS (block storage) from Module 04. -->

---

## Amazon S3 fundamentals

- Object storage with virtually unlimited capacity
- 99.999999999% (eleven nines) durability
- Pay only for what you use, no upfront cost
- Access via S3 API, console, or AWS CLI
- Strong read-after-write consistency for all operations

---

## Buckets, objects, keys, and prefixes

- **Bucket:** container for objects, globally unique name, created in a specific Region
- **Object:** the data (file) plus metadata, up to 5 TB per object
- **Key:** unique identifier within a bucket (e.g., `images/photo.jpg`)
- **Prefix:** simulates folders (e.g., `images/`), but storage is flat

```bash
aws s3 ls s3://my-bucket/images/
```

> S3 has no real directories. Prefixes and delimiters simulate a folder hierarchy in the console.

---

# Storage classes

<!-- Speaker notes: This section takes approximately 15 minutes. Use the filing system analogy: Standard is the desk drawer, Standard-IA is the filing cabinet, Glacier is the offsite warehouse. -->

---

## Storage class comparison

| Storage Class | Designed For | Retrieval Fee |
|---------------|-------------|---------------|
| S3 Standard | Frequently accessed data | None |
| S3 Intelligent-Tiering | Unknown access patterns | None |
| S3 Standard-IA | Infrequent access, fast retrieval | Per-GB fee |
| S3 One Zone-IA | Non-critical infrequent data | Per-GB fee |

---

## Glacier storage classes

| Storage Class | Designed For | Retrieval Time |
|---------------|-------------|----------------|
| Glacier Instant Retrieval | Archive, millisecond access | Milliseconds |
| Glacier Flexible Retrieval | Archive, 1-2 times/year | Minutes to hours |
| Glacier Deep Archive | Long-term archive, rarely accessed | 12 to 48 hours |

> S3 Intelligent-Tiering is a good default when you do not know the access pattern in advance.

---

## Discussion: choosing a storage strategy

You have application logs that are accessed daily for the first week, occasionally for the next 90 days, and almost never after that. Regulations require you to keep them for 7 years.

**What storage class strategy would you recommend?**

<!-- Speaker notes: Expected answer: Start in S3 Standard for the first 30 days (active access). Transition to Standard-IA after 30 days (occasional access). Transition to Glacier Flexible Retrieval after 90 days (rare access). Use a Lifecycle policy to automate these transitions. Expire after 7 years (2,555 days). This is a classic tiered storage pattern. -->

---

# Versioning

<!-- Speaker notes: This section takes approximately 10 minutes. Demonstrate by uploading a file, modifying it, uploading again, then showing both versions. -->

---

## S3 versioning

- Keeps multiple versions of an object in the same bucket
- Protects against accidental deletions and overwrites
- Deleting an object inserts a delete marker (recoverable)
- Once enabled, versioning cannot be fully disabled (only suspended)

```bash
aws s3api put-bucket-versioning \
    --bucket my-bucket \
    --versioning-configuration Status=Enabled
```

> Enable versioning on any bucket storing data you cannot afford to lose.

---

# Lifecycle policies

<!-- Speaker notes: This section takes approximately 10 minutes. Show the transition hierarchy and a JSON example. -->

---

## Lifecycle actions

- **Transition actions:** move objects to a cheaper storage class after N days
- **Expiration actions:** permanently delete objects after N days
- Objects can only transition downward through the hierarchy

```
S3 Standard
  --> Intelligent-Tiering
    --> Standard-IA / One Zone-IA
      --> Glacier Instant Retrieval
        --> Glacier Flexible Retrieval
          --> Glacier Deep Archive
```

---

## Lifecycle rule example

```json
{
  "Rules": [{
    "ID": "ArchiveAndExpireLogs",
    "Filter": { "Prefix": "logs/" },
    "Status": "Enabled",
    "Transitions": [
      { "Days": 30, "StorageClass": "STANDARD_IA" },
      { "Days": 90, "StorageClass": "GLACIER" }
    ],
    "Expiration": { "Days": 365 }
  }]
}
```

> For versioned buckets, add NoncurrentVersionExpiration to control storage costs for old versions.

---

# Access control

<!-- Speaker notes: This section takes approximately 15 minutes. Connect to IAM policies from Module 02. Show a bucket policy and IAM policy side by side. -->

---

## Bucket policies vs. IAM policies

| Feature | IAM Policy | Bucket Policy |
|---------|-----------|---------------|
| Attached to | IAM user, group, or role | S3 bucket |
| Controls access for | A specific identity | Any principal accessing a bucket |
| Cross-account | Requires role assumption | Can grant access directly |
| Use case | What a user/role can do | Who can access a bucket |

> Both the IAM policy and bucket policy must allow the action for the request to succeed.

---

## Block Public Access

- Four settings that override any policy granting public access
- Acts as a safety net against accidental public exposure
- Enabled by default on new buckets and at the account level
- Disable only on specific buckets that require public access

> Enable Block Public Access at the account level as a guardrail.

---

## Quick check: access control

A developer attaches a bucket policy granting `s3:GetObject` to `"Principal": "*"` on a bucket. Block Public Access is enabled at the account level.

**Can anonymous users read objects from this bucket?**

<!-- Speaker notes: Answer: No. Block Public Access overrides the bucket policy. Even though the policy grants public read, the account-level Block Public Access setting prevents it. The developer must explicitly disable Block Public Access on that specific bucket (and have account-level permission to do so) before the public policy takes effect. -->

---

# Encryption

<!-- Speaker notes: This section takes approximately 10 minutes. Emphasize that SSE-S3 is the default and sufficient for most use cases. -->

---

## Server-side encryption options

| Method | Key Management | Cost | Use Case |
|--------|---------------|------|----------|
| SSE-S3 | AWS manages entirely | No extra cost | Default, no overhead |
| SSE-KMS | AWS KMS | KMS key fees | Audit trail, key access control |
| SSE-C | Customer provides keys | No S3 cost | Full customer key control |

- SSE-S3 is enabled by default on all buckets (since January 2023)
- SSE-KMS adds audit trail via CloudTrail and fine-grained key policies
- SSE-C requires you to provide the key with every request

> Use SSE-KMS when compliance requires customer-managed keys or audit logging.

---

# Static website hosting

<!-- Speaker notes: This section takes approximately 10 minutes. Cover the configuration steps and the HTTPS limitation. -->

---

## Hosting a static website on S3

1. Create a bucket and enable static website hosting
2. Specify an index document (`index.html`) and error document
3. Disable Block Public Access on the bucket
4. Add a bucket policy granting public `s3:GetObject`
5. Upload your website files

Endpoint format:
```
http://<bucket>.s3-website-<region>.amazonaws.com
```

> S3 website endpoints use HTTP only. Place CloudFront in front for HTTPS, caching, and custom domains.

---

# Cross-Region Replication

<!-- Speaker notes: This section takes approximately 5 minutes. Cover CRR and Transfer Acceleration briefly. -->

---

## Replication and Transfer Acceleration

- **Cross-Region Replication (CRR):** automatic async copy to another Region
- Requires versioning on both source and destination buckets
- Use cases: compliance, latency reduction, disaster recovery

- **Transfer Acceleration:** speeds uploads via CloudFront edge locations
- Most effective for long-distance uploads
- Uses accelerate endpoint: `<bucket>.s3-accelerate.amazonaws.com`

---

## Key takeaways

- Amazon S3 is object storage with virtually unlimited capacity and eleven-nines durability. Objects are stored in buckets and identified by keys.
- S3 storage classes optimize costs by matching tier to access patterns. Use Lifecycle policies to automate transitions and expire unneeded objects.
- Enable versioning on buckets storing important data. Combine with Lifecycle rules to manage costs for old versions.
- Control access using bucket policies and IAM policies together. Keep Block Public Access enabled at the account level as a safety net.
- S3 encrypts all new objects by default with SSE-S3. Use SSE-KMS when you need audit logging or customer-managed keys.

---

## Lab preview: S3 storage operations

**Objective:** Create an S3 bucket, configure versioning, set up Lifecycle policies, configure encryption, and host a static website

**Key services:** Amazon S3, IAM, AWS KMS

**Duration:** 45 minutes

<!-- Speaker notes: Students will create a bucket in us-east-1, upload objects, enable versioning, create a Lifecycle rule, configure SSE-KMS encryption, and set up static website hosting with a public bucket policy. Remind students to clean up the bucket and objects after the lab to avoid storage charges. -->

---

# Questions?

Review `modules/05-storage-s3/resources.md` for further reading.

<!-- Speaker notes: Take 5-10 minutes for questions. Common questions involve the difference between S3 and EBS, and when to use SSE-KMS vs. SSE-S3. Transition to the lab when ready. -->
