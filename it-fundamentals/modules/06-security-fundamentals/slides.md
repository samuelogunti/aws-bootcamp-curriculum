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

<!-- Speaker notes: Welcome students to Module 06, the final module of the IT Fundamentals primer. This module covers security fundamentals. Security is the final topic and sets the tone for the entire bootcamp. Total lecture time is approximately 10 to 15 minutes. -->

---

## Learning Objectives

By the end of this module, you will be able to:

- Distinguish between authentication and authorization
- Explain the principle of least privilege

<!-- Speaker notes: Two objectives for this module, both at the Remember and Understand levels of Bloom's Taxonomy. These concepts are the foundation of AWS IAM, which students will learn in Module 02 of the bootcamp. Approximately 1 minute on this slide. -->

---

# Security Fundamentals

<!-- Speaker notes: Transition slide. This section takes approximately 10 minutes across two slides plus the quick check. Security is the final topic and sets the tone for the entire bootcamp. -->

---

## Authentication vs. Authorization

| Concept | Question | Example |
|---------|----------|---------|
| Authentication | "Who are you?" | Logging in with a username and password |
| Authorization | "What can you do?" | An IAM policy granting read-only S3 access |

- Authentication happens first (prove your identity)
- Authorization determines what you can access
- AWS IAM handles both for cloud resources

<!-- Speaker notes: This is the single most important security concept for the bootcamp. Every AWS action requires both authentication (who is making the request) and authorization (does the requester have permission). Module 02 of the bootcamp covers IAM in depth. Approximately 3 minutes. -->

---

## Encryption and Least Privilege

**Encryption** converts readable data into unreadable data using a key:
- At rest: protects stored data (S3 objects, database records)
- In transit: protects data on the network (HTTPS, TLS)

**Principle of least privilege:**
- Grant only the minimum permissions needed
- A web server reading from S3 should not have delete permissions
- A developer in staging should not have production access

<!-- Speaker notes: Least privilege is the foundation of IAM policy design. In Module 02 of the bootcamp, students will write IAM policies that follow this principle. In Module 13 (Security in Depth), they will use KMS for encryption. Approximately 4 minutes. -->

---

## Quick Check: Match Security Concepts to Definitions

Match each concept to its definition:

| Concept | Definition |
|---------|------------|
| 1. Authentication | A) Grant only the minimum permissions needed |
| 2. Authorization | B) Convert data into an unreadable format |
| 3. Encryption | C) Verify a user's identity |
| 4. Least privilege | D) Determine what a user is allowed to do |

<!-- Speaker notes: Answers: 1-C, 2-D, 3-B, 4-A. Ask students to answer individually, then review as a group. This reinforces the four security concepts covered in this section. Approximately 3 minutes. -->

---

## Key Takeaways

- Security starts with authentication (who are you) and authorization (what can you do).
- Encryption protects data at rest and in transit.
- The principle of least privilege applies everywhere in cloud computing.

<!-- Speaker notes: Three key takeaways for this module. Reinforce that security is not an afterthought; it is built into every decision in the bootcamp. This completes the IT Fundamentals primer. Approximately 1 minute. -->

---

## Lab Preview and Questions

**Lab 00: Security Fundamentals Hands-On Exercises**

What you will do:
- Identify your user identity with `whoami` and `id`
- Examine and modify file permissions with `ls -la` and `chmod`
- Explore environment variables and discuss secrets management

**Duration:** 5 to 8 minutes
**No cloud resources created. Everything runs on your local machine.**

Questions?

<!-- Speaker notes: Walk through the lab structure briefly. Remind students that the lab is fully guided with exact commands and expected output for every step. The lab demonstrates authentication (user identity), authorization (file permissions), and configuration management (environment variables) on their local machines. This is the final module of the IT Fundamentals primer. After completing the lab, students are ready to begin Module 01: Cloud Fundamentals. Take 2 to 3 minutes for questions. -->
