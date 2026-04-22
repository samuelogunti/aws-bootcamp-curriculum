# IT Fundamentals Module 06: Security Fundamentals (Module 06 of 06)

## Learning Objectives

By the end of this module, you will be able to:

- Distinguish between authentication and authorization and explain the principle of least privilege

## Prerequisites

- A computer with internet access
- A terminal application (Terminal on macOS, Git Bash on Windows, terminal on Linux)
- Module 02 recommended for terminal skills (lab uses terminal commands)

**Estimated self-study time:**

| Activity | Estimated Time |
|----------|---------------|
| Reading | 5 to 8 minutes |
| Lab | 5 to 8 minutes |
| Quiz | 3 to 5 minutes |
| Total | 13 to 21 minutes |

## Concepts

### Security Fundamentals

Security is a thread that runs through every module of the AWS Bootcamp. Understanding these basics now will make the IAM, encryption, and network security concepts much easier.

#### Authentication vs. Authorization

| Concept | Question It Answers | Example |
|---------|-------------------|---------|
| Authentication | "Who are you?" | Logging in with a username and password |
| Authorization | "What are you allowed to do?" | An IAM policy that grants read-only access to S3 |

Authentication happens first (prove your identity), then authorization determines what you can access.

#### Encryption

[Encryption](https://csrc.nist.gov/glossary/term/encryption) converts readable data (plaintext) into unreadable data (ciphertext) using a key. Only someone with the correct key can decrypt it back to plaintext.

- **Encryption at rest:** Protects data stored on disk (S3 objects, database records, EBS volumes)
- **Encryption in transit:** Protects data moving across a network (HTTPS, TLS, VPN connections)

#### The Principle of Least Privilege

Grant users and applications only the minimum permissions they need to do their job. Nothing more.

- A web server that reads from S3 should not have permission to delete S3 objects
- A developer who deploys to staging should not have permission to deploy to production
- A monitoring tool that reads CloudWatch metrics should not have permission to modify alarms

This principle is the foundation of AWS IAM, which you will learn in Module 02 of the bootcamp.

> **Bootcamp connection:** Understanding security fundamentals prepares you for Identity and Access Management (IAM) in Module 02: IAM and Security. In Module 13: Security in Depth, you will use AWS Key Management Service (KMS) for encryption and configure advanced security services.

## Instructor Notes

**Estimated lecture time:** 10 to 15 minutes

**Common student questions:**

- Q: What is the difference between encryption at rest and in transit?
  A: Encryption at rest protects data that is stored (on a hard drive, in a database, in an S3 bucket). Encryption in transit protects data while it is moving across a network (between your browser and a server, between two AWS services). Both are important, and AWS provides tools for each: KMS and S3 server-side encryption for at-rest, and TLS/HTTPS for in-transit.

- Q: Why is least privilege important?
  A: Least privilege limits the damage that can occur if credentials are compromised. If a web server only has read access to one S3 bucket, an attacker who compromises that server cannot delete data or access other resources. In AWS, you enforce least privilege through IAM policies.

**Teaching tips:**

- End with the security section to set the tone for the bootcamp: security is not an afterthought, it is built into every decision.
- Use the apartment vs house analogy for authentication and authorization: authentication is like showing your ID at the front door (proving who you are), and authorization is like having a key that only opens certain rooms (determining what you can access).

**Pause point:**

- Ask students to distinguish authentication from authorization. Expected answer: authentication verifies identity ("Who are you?"), authorization determines access ("What are you allowed to do?").

## Key Takeaways

- Security starts with two concepts: authentication (who are you) and authorization (what can you do). The principle of least privilege applies everywhere in cloud computing.

---

[Previous: Module 05, Version Control with Git](../05-version-control-git/README.md) | [Next: Module 01, Cloud Fundamentals (bootcamp)](../../../modules/01-cloud-fundamentals/README.md) | [IT Fundamentals Overview](../../README.md)
