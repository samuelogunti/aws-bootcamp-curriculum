---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'IT Fundamentals: Module 06, Security Fundamentals'
---

# IT Fundamentals Module 06: Security Fundamentals

**Module 06 of 06**
Estimated lecture time: 10 to 15 minutes

<!-- Speaker notes: Welcome students to Module 06, the final module of the IT Fundamentals primer. This module covers security fundamentals. Security is the final topic and sets the tone for the entire bootcamp: security is not an afterthought, it is built into every decision. Total lecture time is approximately 10 to 15 minutes. -->

---

## Learning Objectives

By the end of this module, you will be able to:

- Distinguish between authentication and authorization
- Explain the principle of least privilege
- Describe encryption at rest and encryption in transit

<!-- Speaker notes: Three objectives for this module, all at the Remember and Understand levels of Bloom's Taxonomy. These concepts are the foundation of AWS IAM, which students will learn in Module 02 of the bootcamp. Approximately 1 minute on this slide. -->

---

## Prerequisites and Agenda

**Prerequisites:** Terminal skills from Module 02 (lab uses terminal commands)

**Agenda:**
1. Authentication vs. authorization
2. Encryption (at rest and in transit)
3. The principle of least privilege
4. Real-world examples
5. Bootcamp connections

<!-- Speaker notes: This is the final module. After completing it, students are ready for Module 01: Cloud Fundamentals. Approximately 1 minute. -->

---

# Security Fundamentals

<!-- Speaker notes: Transition slide. Security is a thread that runs through every module of the AWS Bootcamp. Understanding these basics now makes IAM, encryption, and network security concepts much easier. -->

---

## Authentication vs. Authorization

| Concept | Question It Answers | Example |
|---------|-------------------|---------|
| Authentication | "Who are you?" | Logging in with a username and password |
| Authorization | "What are you allowed to do?" | An IAM policy granting read-only S3 access |

- **Authentication happens first:** prove your identity
- **Authorization happens second:** determine what you can access
- AWS IAM handles both for cloud resources

> **Analogy:** Authentication is showing your ID at the front door. Authorization is having a key that only opens certain rooms.

<!-- Speaker notes: This is the single most important security concept for the bootcamp. Every AWS action requires both authentication (who is making the request) and authorization (does the requester have permission). Module 02 of the bootcamp covers IAM in depth. Approximately 3 minutes. -->

---

## Encryption at Rest

Encryption converts readable data (plaintext) into unreadable data (ciphertext) using a key.

**Encryption at rest** protects data stored on disk:

| What Is Protected | AWS Service |
|-------------------|-------------|
| S3 objects | S3 server-side encryption |
| Database records | RDS encryption, DynamoDB encryption |
| EBS volumes | EBS encryption |
| Backups and snapshots | Encrypted by default when source is encrypted |

- Only someone with the correct key can decrypt the data
- AWS Key Management Service (KMS) manages encryption keys

<!-- Speaker notes: In Module 13 (Security in Depth), students will create KMS keys and configure S3 bucket encryption. Approximately 2 minutes. -->

---

## Encryption in Transit

**Encryption in transit** protects data moving across a network:

| What Is Protected | How |
|-------------------|-----|
| Web traffic | HTTPS (TLS on port 443) |
| API calls | HTTPS endpoints |
| Database connections | TLS-encrypted connections |
| VPN connections | IPSec or TLS tunnels |

- TLS (Transport Layer Security) encrypts all data between sender and receiver
- The padlock icon in your browser means TLS is active
- Always use HTTPS for production applications

<!-- Speaker notes: Students learned about HTTPS and TLS in Module 03. This slide reinforces the concept in a security context. In the bootcamp, they will configure TLS certificates on load balancers. Approximately 2 minutes. -->

---

## The Principle of Least Privilege

Grant users and applications only the minimum permissions they need. Nothing more.

| Scenario | Correct Permission | Overly Broad Permission |
|----------|-------------------|------------------------|
| Web server reads from S3 | `s3:GetObject` on one bucket | `s3:*` on all buckets |
| Developer deploys to staging | Deploy access to staging only | Admin access to all environments |
| Monitoring tool reads metrics | `cloudwatch:GetMetricData` | `cloudwatch:*` (includes delete) |

- If credentials are compromised, least privilege limits the damage
- Start with zero permissions and add only what is needed

<!-- Speaker notes: Least privilege is the foundation of IAM policy design. In Module 02 of the bootcamp, students will write IAM policies that follow this principle. The table shows concrete examples of correct vs overly broad permissions. Approximately 3 minutes. -->

---

## Quick Check: Match Security Concepts to Definitions

Match each concept to its definition:

| Concept | Definition |
|---------|------------|
| 1. Authentication | A) Grant only the minimum permissions needed |
| 2. Authorization | B) Convert data into an unreadable format |
| 3. Encryption | C) Verify a user's identity |
| 4. Least privilege | D) Determine what a user is allowed to do |

<!-- Speaker notes: Answers: 1-C, 2-D, 3-B, 4-A. Ask students to answer individually, then review as a group. This reinforces the four security concepts covered in this module. Approximately 3 minutes. -->

---

## Bootcamp Connections

> **Module 02 (IAM and Security):** Authentication, authorization, IAM users, roles, and policies
> **Module 05 (Storage: S3):** S3 bucket encryption and access policies
> **Module 13 (Security in Depth):** KMS encryption keys, WAF, Shield, GuardDuty, CloudTrail
> **Every module:** Security is built into every decision, not added as an afterthought

<!-- Speaker notes: Security is the final topic in the primer because it sets the tone for the entire bootcamp. Every module includes security considerations. Approximately 1 minute. -->

---

## Instructor Notes: Common Questions

**Q: What is the difference between encryption at rest and in transit?**
At rest protects stored data (S3, databases, EBS). In transit protects data moving across a network (HTTPS, TLS, VPN). AWS provides tools for both: KMS for at-rest, TLS/HTTPS for in-transit.

**Q: Why is least privilege important?**
It limits damage if credentials are compromised. A web server with only `s3:GetObject` on one bucket cannot delete data or access other resources if compromised.

<!-- Speaker notes: Use the apartment analogy: authentication is showing your ID at the door, authorization is having a key that only opens certain rooms. Approximately 2 minutes. -->

---

## Key Takeaways

- Authentication verifies identity ("Who are you?"); authorization determines access ("What can you do?")
- Encryption at rest protects stored data; encryption in transit protects data on the network
- The principle of least privilege: grant only the minimum permissions needed
- AWS IAM handles authentication and authorization for all cloud resources
- KMS manages encryption keys; TLS/HTTPS encrypts data in transit
- Security is built into every decision in the bootcamp, not added as an afterthought

<!-- Speaker notes: Six key takeaways covering all security concepts. This completes the IT Fundamentals primer. Approximately 1 minute. -->

---

## Lab Preview and Questions

**Lab 00: Security Fundamentals Hands-On Exercises**

What you will do:
- Identify your user identity with `whoami` and `id`
- Examine and modify file permissions with `ls -la` and `chmod`
- Explore environment variables and discuss secrets management

**Duration:** 5 to 8 minutes
**No cloud resources created. Everything runs on your local machine.**

**You are now ready for Module 01: Cloud Fundamentals.**

Questions?

<!-- Speaker notes: This is the final module of the IT Fundamentals primer. After completing the lab, students are ready to begin Module 01: Cloud Fundamentals. The lab demonstrates authentication (user identity), authorization (file permissions), and configuration management (environment variables) on their local machines. Take 2 to 3 minutes for questions. -->
