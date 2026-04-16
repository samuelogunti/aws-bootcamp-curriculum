---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'Module 02: IAM and Security'
---

# Module 02: IAM and Security

**Phase 1: Cloud Foundations**
Estimated lecture time: 75 minutes

<!-- Speaker notes: This module builds directly on Module 01's Shared Responsibility Model. Remind students that IAM is how they fulfill the customer side of that model. Timing: 15 min IAM overview, 15 min users/groups/policies, 10 min roles, 10 min policy JSON, 10 min Organizations/SCPs, 10 min best practices, 5 min Q&A. -->

---

## Learning objectives

By the end of this module, you will be able to:

- Define authentication and authorization in the context of AWS IAM
- Describe IAM users, user groups, roles, and policies
- Explain how IAM policy documents are structured in JSON
- Identify when to use IAM roles instead of IAM users
- List IAM security best practices
- Summarize how AWS Organizations and SCPs provide multi-account governance
- Distinguish between identity-based and resource-based policies

---

## Prerequisites and agenda

**Prerequisites:** Completion of Module 01 (AWS account, console navigation, Shared Responsibility Model)

**Agenda:**
1. IAM overview: authentication vs. authorization
2. IAM users, user groups, and policies
3. IAM roles: when and why
4. IAM policy structure (JSON)
5. AWS Organizations and SCPs
6. IAM security best practices

---

# Authentication vs. authorization

<!-- Speaker notes: 15 minutes for this section. Start by asking students: "What is the difference between proving who you are and proving what you are allowed to do?" -->

---

## Two fundamental questions

| Question | IAM Concept | Mechanism |
|----------|------------|-----------|
| **Who are you?** | Authentication | Username + password, access keys, MFA |
| **What can you do?** | Authorization | IAM policies (JSON documents) |

> Think of authentication as showing your ID at the door, and authorization as the list of rooms you are allowed to enter.

---

## Discussion: authentication vs. authorization

You sign in to the AWS Console with your username and password (authentication). You then try to create an S3 bucket, but receive an "Access Denied" error.

**Which step failed: authentication or authorization?**

<!-- Speaker notes: Answer: Authorization failed. Authentication succeeded (you signed in), but the IAM policy attached to your user does not grant s3:CreateBucket permission. This is the most common confusion for beginners. -->

---

# IAM users, user groups, and policies

<!-- Speaker notes: 15 minutes. Emphasize that groups simplify permission management: assign policies to groups, then add users to groups. -->

---

## IAM identity types

| Identity | What It Is | Best For |
|----------|-----------|----------|
| **IAM user** | Person or application with permanent credentials | Individual human access |
| **User group** | Collection of users sharing the same policies | Team-based permissions |
| **IAM role** | Identity with temporary credentials (no password) | Services, cross-account access |

---

## Policy types

| Type | Attached To | Managed By | Use Case |
|------|-----------|------------|----------|
| AWS managed | Users, groups, roles | AWS | Common permissions (ReadOnlyAccess) |
| Customer managed | Users, groups, roles | You | Custom permissions for your org |
| Inline | Single user, group, or role | You | Strict 1:1 policy-to-identity binding |

---

# IAM roles

<!-- Speaker notes: 10 minutes. Key point: roles provide temporary credentials via STS. Never embed long-lived access keys in code. -->

---

## Why roles instead of users?

- Roles provide **temporary credentials** that expire automatically
- No passwords or access keys to manage or rotate
- AWS services (EC2, Lambda, ECS) assume roles to access other services
- Cross-account access without sharing credentials

**Common role use cases:**
- EC2 instance accessing S3
- Lambda function writing to DynamoDB
- Cross-account access for a CI/CD pipeline

---

# IAM policy structure

<!-- Speaker notes: 10 minutes. Walk through the JSON structure on the next slide. Emphasize the Effect/Action/Resource pattern. -->

---

## Anatomy of a policy document

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::my-bucket/*"
    }
  ]
}
```

- **Effect:** Allow or Deny
- **Action:** Which API calls (e.g., `s3:GetObject`)
- **Resource:** Which AWS resources (ARN)

---

## Policy evaluation logic

1. All requests start as **implicitly denied**
2. An **Allow** in a policy grants access
3. An **explicit Deny** always overrides any Allow
4. If no policy allows the action, the request is denied

> An explicit Deny always wins. One Deny overrides any number of Allows.

---

## Quick check: policy evaluation

A user has two policies attached:
- Policy A: `Allow s3:*` on all resources
- Policy B: `Deny s3:DeleteBucket` on all resources

**Can the user delete an S3 bucket?**

<!-- Speaker notes: Answer: No. The explicit Deny in Policy B overrides the Allow in Policy A. This is the most important rule in IAM policy evaluation. -->

---

# AWS Organizations and SCPs

<!-- Speaker notes: 10 minutes. Explain that Organizations is for multi-account governance. SCPs set the maximum permissions boundary for an entire account. -->

---

## AWS Organizations

- Centrally manage multiple AWS accounts
- Group accounts into Organizational Units (OUs)
- Consolidated billing across all accounts
- Apply Service Control Policies (SCPs) for guardrails

---

## SCPs vs. IAM policies

| Feature | SCP | IAM Policy |
|---------|-----|------------|
| Scope | Entire account or OU | Individual user, group, or role |
| Grants permissions? | No (restricts only) | Yes |
| Overrides IAM Allow? | Yes (sets maximum boundary) | No |

> SCPs are the fence around the property. IAM policies are the keys to rooms inside.

---

# IAM security best practices

<!-- Speaker notes: 10 minutes. These are the practices students should follow for every lab and project in the bootcamp. -->

---

## Top security practices

1. **Lock away the root user:** Enable MFA, do not use for daily tasks
2. **Use IAM roles, not access keys:** Temporary credentials are safer
3. **Apply least privilege:** Start with zero permissions, add only what is needed
4. **Use groups for permissions:** Assign policies to groups, add users to groups
5. **Enable MFA:** Require MFA for all human users

---

## More security practices

- Enable AWS CloudTrail for audit logging
- Use IAM Access Analyzer to find overly permissive policies
- Rotate credentials regularly
- Use policy conditions (IP restrictions, MFA required)
- Review permissions periodically and remove unused access

---

## Key takeaways

- IAM controls authentication (who you are) and authorization (what you can do) for your AWS account
- Use IAM roles with temporary credentials instead of long-lived access keys
- IAM policies are JSON documents with Effect, Action, and Resource; explicit Deny always wins
- Apply the principle of least privilege: grant only the permissions needed for the task
- AWS Organizations and SCPs provide guardrails across multiple accounts

---

## Lab preview: IAM users, groups, policies, and roles

**What you will do:**
- Create IAM user groups with different permission levels
- Attach managed and custom policies
- Create an IAM role for EC2 to access S3
- Test explicit deny behavior with the IAM Policy Simulator

**Duration:** 60 minutes

---

# Questions?

Review `modules/02-iam-and-security/resources.md` for further reading.

<!-- Speaker notes: Take 5 minutes for questions. Common question: "Do I need to memorize the JSON policy syntax?" Answer: No, but you need to understand the Effect/Action/Resource structure and how evaluation works. -->
