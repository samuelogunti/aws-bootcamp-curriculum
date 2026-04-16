# Module 05: Resources

## Official Documentation

### S3 Overview and Core Concepts

- [What Is Amazon S3?](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Welcome.html)
- [Using Amazon S3 Buckets](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingBucket.html)
- [Naming Amazon S3 Objects (Object Keys)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-keys.html)
- [Uploading Objects Using Multipart Upload](https://docs.aws.amazon.com/AmazonS3/latest/userguide/mpuoverview.html)
- [Bucket Naming Rules](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucketnamingrules.html)

### Storage Classes

- [Understanding and Managing Amazon S3 Storage Classes](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-class-intro.html)
- [S3 Intelligent-Tiering](https://docs.aws.amazon.com/AmazonS3/latest/userguide/intelligent-tiering.html)
- [S3 Glacier Storage Classes](https://docs.aws.amazon.com/AmazonS3/latest/userguide/glacier-storage-classes.html)

### Versioning

- [Retaining Multiple Versions of Objects with S3 Versioning](https://docs.aws.amazon.com/AmazonS3/latest/userguide/Versioning.html)
- [MFA Delete](https://docs.aws.amazon.com/AmazonS3/latest/userguide/MultiFactorAuthenticationDelete.html)

### Lifecycle Policies

- [Managing the Lifecycle of Objects](https://docs.aws.amazon.com/AmazonS3/latest/userguide/object-lifecycle-mgmt.html)
- [Lifecycle Configuration Elements](https://docs.aws.amazon.com/AmazonS3/latest/userguide/intro-lifecycle-rules.html)
- [Transitioning Objects Using Amazon S3 Lifecycle](https://docs.aws.amazon.com/AmazonS3/latest/userguide/lifecycle-transition-general-considerations.html)

### Access Control

- [Examples of Amazon S3 Bucket Policies](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html)
- [Access Control Lists (ACLs) Overview](https://docs.aws.amazon.com/AmazonS3/latest/userguide/acl-overview.html)
- [Controlling Ownership of Objects and Disabling ACLs](https://docs.aws.amazon.com/AmazonS3/latest/userguide/about-object-ownership.html)
- [S3 Block Public Access](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-control-block-public-access.html)
- [Managing Access with S3 Access Points](https://docs.aws.amazon.com/AmazonS3/latest/userguide/access-points.html)
- [Setting Permissions for Website Access](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteAccessPermissionsReqd.html)

### Encryption

- [Protecting Data with Server-Side Encryption Using Amazon S3 Managed Keys (SSE-S3)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingServerSideEncryption.html)
- [Using Server-Side Encryption with AWS KMS Keys (SSE-KMS)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/UsingKMSEncryption.html)
- [Using Server-Side Encryption with Customer-Provided Keys (SSE-C)](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerSideEncryptionCustomerKeys.html)
- [Configuring Default Encryption](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-encryption.html)
- [Default Encryption FAQ](https://docs.aws.amazon.com/AmazonS3/latest/userguide/default-encryption-faq.html)
- [Reducing the Cost of SSE-KMS with Amazon S3 Bucket Keys](https://docs.aws.amazon.com/AmazonS3/latest/userguide/bucket-key.html)
- [What Is AWS Key Management Service?](https://docs.aws.amazon.com/kms/latest/developerguide/overview.html)

### Static Website Hosting

- [Hosting a Static Website Using Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/userguide/WebsiteHosting.html)
- [Configuring an Index Document](https://docs.aws.amazon.com/AmazonS3/latest/userguide/IndexDocumentSupport.html)
- [Configuring a Custom Error Document](https://docs.aws.amazon.com/AmazonS3/latest/userguide/CustomErrorDocSupport.html)

### Replication and Transfer Acceleration

- [Replicating Objects Within and Across Regions](https://docs.aws.amazon.com/AmazonS3/latest/userguide/replication.html)
- [Configuring Amazon S3 Transfer Acceleration](https://docs.aws.amazon.com/AmazonS3/latest/userguide/transfer-acceleration.html)
- [Using the Amazon S3 Transfer Acceleration Speed Comparison Tool](https://docs.aws.amazon.com/AmazonS3/latest/userguide/transfer-acceleration-speed-comparison.html)

### Server Access Logging (Lab Challenge)

- [Logging Requests with Server Access Logging](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ServerLogs.html)

### Amazon CloudFront (Referenced for HTTPS with S3 Websites)

- [What Is Amazon CloudFront?](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/Introduction.html)

## AWS Whitepapers

Module 05 focuses on Amazon S3 storage fundamentals using the Amazon S3 User Guide as the primary reference. There are no AWS whitepapers dedicated specifically to introductory S3 storage topics. For broader storage architecture guidance, students will encounter relevant whitepapers in later modules covering reliability (Module 16) and the Well-Architected Framework (Module 17).

## AWS FAQs

- [Amazon S3 FAQ](https://aws.amazon.com/s3/faqs/)

## AWS Architecture References

No specific architecture references for this module. Amazon S3 is a foundational storage service that appears in nearly every AWS reference architecture. Students will work with S3-based architecture patterns in later modules when they build serverless applications (Module 09), implement infrastructure as code (Module 11), and design multi-tier architectures (Module 18).
