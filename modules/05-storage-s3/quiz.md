# Module 05: Quiz

1. Which of the following statements best describes how Amazon S3 organizes data internally?

   A) S3 stores objects in a hierarchical file system with directories and subdirectories
   B) S3 uses a flat structure where each object is identified by a unique key within a bucket
   C) S3 organizes objects into tables with rows and columns, similar to a database
   D) S3 stores objects in block-level volumes that must be mounted to an instance

2. True or False: Once you enable versioning on an S3 bucket, you can return the bucket to an unversioned state by disabling versioning.

3. A company stores log files in S3 Standard. The logs are accessed frequently for the first 30 days, occasionally for the next 60 days, and almost never after 90 days. The company wants to minimize storage costs automatically. Which S3 feature should they use?

   A) S3 Intelligent-Tiering
   B) S3 Lifecycle policies with transition and expiration actions
   C) S3 Cross-Region Replication
   D) S3 Transfer Acceleration

4. Which of the following are valid S3 storage class transitions that can be configured in a Lifecycle rule? (Select TWO.)

   A) S3 Standard to S3 Standard-IA
   B) S3 Glacier Deep Archive to S3 Standard
   C) S3 Standard-IA to S3 Glacier Flexible Retrieval
   D) S3 One Zone-IA to S3 Standard
   E) S3 Glacier Instant Retrieval to S3 Intelligent-Tiering

5. In your own words, explain the difference between a bucket policy and an IAM policy when controlling access to S3 resources. When would you use each one?

6. Which S3 server-side encryption option requires you to provide the encryption key with every PUT and GET request, and does not store the key on the AWS side?

   A) SSE-S3
   B) SSE-KMS
   C) SSE-C
   D) Client-side encryption

7. True or False: An S3 static website endpoint supports HTTPS natively without any additional AWS services.

8. What is the primary prerequisite that must be configured on both the source and destination buckets before you can enable S3 Cross-Region Replication (CRR)?

   A) Default encryption with SSE-KMS
   B) S3 Versioning
   C) S3 Transfer Acceleration
   D) Block Public Access disabled

9. When you delete an object from a versioning-enabled S3 bucket without specifying a version ID, what happens?

   A) The object and all its versions are permanently deleted
   B) S3 inserts a delete marker as the current version, and previous versions remain in the bucket
   C) The most recent version is permanently deleted, and the previous version becomes current
   D) The delete request fails because you must specify a version ID

10. Which S3 encryption method provides an audit trail of key usage through AWS CloudTrail and allows you to control access to the encryption key through IAM policies?

    A) SSE-S3
    B) SSE-KMS
    C) SSE-C
    D) AES-256 client-side encryption

---

<details>
<summary>Answer Key</summary>

1. **B) S3 uses a flat structure where each object is identified by a unique key within a bucket**
   Amazon S3 is an object storage service with a flat namespace. There are no actual directories or folders. The S3 console displays prefixes (such as `images/`) as folders for convenience, but the underlying storage structure is flat. Each object is uniquely identified by its key within a bucket. Option A is incorrect because S3 does not use a hierarchical file system. Option C describes a database model, not object storage. Option D describes block storage (Amazon EBS), not object storage.
   Further reading: [Naming Amazon S3 objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-keys.html)

2. **False.**
   Once you enable versioning on an S3 bucket, you cannot return it to the unversioned state. You can only suspend versioning. When versioning is suspended, new objects are stored with a `null` version ID, but all existing versioned objects are preserved and continue to incur storage costs until you explicitly delete them.
   Further reading: [Retaining multiple versions of objects with S3 Versioning](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html)

3. **B) S3 Lifecycle policies with transition and expiration actions**
   Lifecycle policies let you define rules that automatically transition objects between storage classes after a specified number of days and expire (delete) objects when they are no longer needed. For this use case, you could transition logs from S3 Standard to S3 Standard-IA after 30 days, then to S3 Glacier Flexible Retrieval after 90 days, and expire them after a retention period. Option A (Intelligent-Tiering) automatically moves objects between tiers based on access patterns, but it does not delete objects or follow a predictable schedule. Option C (CRR) copies objects to another Region and does not reduce storage costs. Option D (Transfer Acceleration) speeds up uploads and is unrelated to storage cost optimization.
   Further reading: [Managing the lifecycle of objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html)

4. **A and C**
   S3 Lifecycle rules can only transition objects in a downward direction through the storage class hierarchy: S3 Standard to S3 Standard-IA (A) and S3 Standard-IA to S3 Glacier Flexible Retrieval (C) are both valid transitions. Option B is incorrect because you cannot transition from Glacier Deep Archive back to S3 Standard using a Lifecycle rule. Option D is incorrect because you cannot transition from S3 One Zone-IA back to S3 Standard. Option E is incorrect because you cannot transition from a lower-cost class back to a higher-cost class. To move objects back up the hierarchy, you must copy them manually.
   Further reading: [Transitioning objects using Amazon S3 Lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html)

5. **Sample answer:** A bucket policy is a resource-based policy attached directly to an S3 bucket. It controls who (which principals) can access that specific bucket and its objects. A bucket policy can grant access to IAM users, roles, other AWS accounts, or even anonymous users. An IAM policy is an identity-based policy attached to an IAM user, group, or role. It controls what actions that identity can perform across all AWS services, including S3. Use a bucket policy when you need to control access to a specific bucket, especially for cross-account access or public access scenarios. Use an IAM policy when you want to define what a specific user or role is allowed to do across multiple services and resources. In practice, both policies must allow the action for the request to succeed.
   Further reading: [Examples of Amazon S3 bucket policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html)

6. **C) SSE-C**
   With server-side encryption with customer-provided keys (SSE-C), you provide the encryption key with every PUT and GET request. S3 uses your key to encrypt or decrypt the object but does not store the key. You are responsible for managing, rotating, and tracking which key was used for each object. SSE-C also requires HTTPS for all requests. Option A (SSE-S3) uses keys managed entirely by AWS. Option B (SSE-KMS) uses keys managed by AWS Key Management Service. Option D (client-side encryption) encrypts data before sending it to S3, which is different from server-side encryption.
   Further reading: [Using server-side encryption with customer-provided keys (SSE-C)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerSideEncryptionCustomerKeys.html)

7. **False.**
   S3 static website endpoints use HTTP only. They do not support HTTPS natively. To serve a static website over HTTPS, you must place an Amazon CloudFront distribution in front of the S3 bucket. CloudFront provides HTTPS support, caching at edge locations, and the ability to use a custom domain name with an SSL/TLS certificate.
   Further reading: [Hosting a static website using Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html)

8. **B) S3 Versioning**
   Cross-Region Replication (CRR) requires versioning to be enabled on both the source and destination buckets. Without versioning, S3 cannot track object versions for replication. Option A (SSE-KMS) is not a prerequisite for CRR, though encrypted objects can be replicated with additional configuration. Option C (Transfer Acceleration) speeds up uploads and is unrelated to replication. Option D (Block Public Access) controls public access and is not a prerequisite for replication.
   Further reading: [Replicating objects within and across Regions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication.html)

9. **B) S3 inserts a delete marker as the current version, and previous versions remain in the bucket**
   In a versioning-enabled bucket, deleting an object without specifying a version ID does not permanently remove the object. Instead, S3 inserts a delete marker, which becomes the current version. All previous versions remain in the bucket and can be recovered by deleting the delete marker or by retrieving a specific version by its version ID. Option A is incorrect because a simple delete does not remove all versions. Option C is incorrect because no version is permanently deleted. Option D is incorrect because the delete request succeeds by inserting a delete marker.
   Further reading: [Retaining multiple versions of objects with S3 Versioning](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html)

10. **B) SSE-KMS**
    Server-side encryption with AWS KMS keys (SSE-KMS) integrates with AWS CloudTrail to log every use of the encryption key, providing a full audit trail. You can also control access to the KMS key through IAM policies, giving you fine-grained control over who can encrypt and decrypt objects. Option A (SSE-S3) uses keys managed entirely by AWS with no customer visibility into key usage. Option C (SSE-C) uses customer-provided keys, but since AWS does not store the key, there is no AWS-side audit trail of key usage. Option D (client-side encryption) is managed entirely by the customer and does not involve AWS key management services.
    Further reading: [Using server-side encryption with AWS KMS keys (SSE-KMS)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingKMSEncryption.html)

</details>

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
