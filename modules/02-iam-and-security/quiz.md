# Module 02: Quiz

1. What is the difference between authentication and authorization in AWS IAM?

   A) Authentication determines what actions you can perform; authorization verifies your identity
   B) Authentication verifies your identity; authorization determines what actions you can perform
   C) Authentication and authorization are the same process in IAM
   D) Authentication applies only to the root user; authorization applies to IAM users

2. True or False: IAM user groups can be nested, meaning one group can contain another group as a member.

3. Which of the following is a characteristic of an AWS managed policy? (Select TWO.)

   A) It is created and maintained by AWS
   B) It can only be attached to a single IAM identity
   C) It is automatically updated by AWS when new services or API actions are released
   D) It is embedded directly in a user, group, or role and deleted when the identity is deleted
   E) It supports up to 5 customer-controlled versions

4. In an IAM JSON policy document, which element specifies whether the statement allows or denies access?

   A) Action
   B) Resource
   C) Effect
   D) Condition

5. An IAM policy explicitly allows `s3:GetObject` on all S3 resources. A second IAM policy explicitly denies `s3:*` on all S3 resources. Both policies are attached to the same user. What happens when the user attempts to call `s3:GetObject`?

   A) The request is allowed because the allow policy was evaluated first
   B) The request is allowed because `s3:GetObject` is more specific than `s3:*`
   C) The request is denied because an explicit deny always overrides any allow
   D) The request is denied only if no resource-based policy allows it

6. How does an IAM role differ from an IAM user?

   A) A role can have IAM policies attached, but a user cannot
   B) A role has permanent credentials (password and access keys), while a user has temporary credentials
   C) A role provides temporary security credentials through AWS STS, while a user has permanent credentials such as a password or access keys
   D) A role can only be used by AWS services, not by human users or external accounts

7. True or False: A Service Control Policy (SCP) in AWS Organizations can grant permissions to IAM users and roles in member accounts.

8. List three IAM security best practices recommended by AWS for protecting your AWS account.

9. Which of the following best describes the purpose of a service role in IAM?

   A) A role that allows one AWS account to access resources in another AWS account
   B) A role that allows an AWS service (such as EC2 or Lambda) to perform actions on your behalf using temporary credentials
   C) A role that grants the root user additional permissions beyond the default
   D) A role that replaces IAM user groups for managing permissions across multiple users

10. A company uses AWS Organizations to manage multiple AWS accounts. The security team attaches an SCP to the Production organizational unit (OU) that denies `s3:DeleteBucket`. An IAM administrator in a member account under that OU has the `AdministratorAccess` managed policy attached. Can the administrator delete an S3 bucket in that member account?

    A) Yes, because `AdministratorAccess` grants full permissions to all AWS services
    B) Yes, because SCPs only affect non-administrator IAM users
    C) No, because the SCP sets the maximum permissions boundary, and the deny in the SCP overrides the IAM allow
    D) No, because SCPs automatically remove conflicting IAM policies from member accounts

---

<details>
<summary>Answer Key</summary>

1. **B) Authentication verifies your identity; authorization determines what actions you can perform**
   Authentication is the process of confirming who you are (for example, signing in with a username and password). Authorization is the process of determining what you are allowed to do after your identity is confirmed. IAM handles both: it authenticates credentials and then evaluates policies to authorize requests. Option A reverses the definitions. Option C is incorrect because they are distinct processes. Option D is incorrect because both authentication and authorization apply to all IAM identities, not just the root user.
   Further reading: [What is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)

2. **False.**
   IAM user groups cannot be nested. A group can contain IAM users, but it cannot contain other groups. Groups exist solely as a way to attach policies to multiple users at once. They are not identities that can be referenced in a policy's Principal element.
   Further reading: [IAM user groups](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_groups.html)

3. **A and C**
   AWS managed policies are created and maintained by AWS (A) and are automatically updated when new services or API actions are released (C). Option B describes inline policies, which have a strict one-to-one relationship with a single identity. Option D also describes inline policies, which are embedded directly in an identity and deleted with it. Option E describes customer managed policies, which support up to 5 versions that you control, not AWS managed policies.
   Further reading: [Managed policies and inline policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_managed-vs-inline.html)

4. **C) Effect**
   The Effect element specifies whether the statement results in an allow or a deny. The only valid values are `"Allow"` and `"Deny"`. Action (A) specifies which AWS service actions the statement applies to. Resource (B) specifies which AWS resources the statement applies to, using ARNs. Condition (D) is an optional element that specifies circumstances under which the statement is in effect.
   Further reading: [IAM JSON policy element reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html)

5. **C) The request is denied because an explicit deny always overrides any allow**
   IAM policy evaluation follows three rules: (1) all requests are implicitly denied by default, (2) an explicit allow overrides the implicit deny, and (3) an explicit deny always overrides any allow. Since the second policy explicitly denies `s3:*`, the deny takes precedence regardless of the allow in the first policy. The order of evaluation does not matter (A is wrong). Specificity of the action does not override an explicit deny (B is wrong). The deny applies regardless of resource-based policies in this scenario (D is wrong).
   Further reading: [Policy evaluation logic](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html)

6. **C) A role provides temporary security credentials through AWS STS, while a user has permanent credentials such as a password or access keys**
   An IAM role does not have permanent credentials. When an entity assumes a role, AWS Security Token Service (STS) issues temporary security credentials that expire after a configurable duration. An IAM user, by contrast, can have a console password and access keys that persist until rotated or deleted. Option A is wrong because both users and roles can have policies attached. Option B reverses the credential types. Option D is wrong because roles can also be assumed by human users (through federation) and by identities in other AWS accounts.
   Further reading: [IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)

7. **False.**
   SCPs do not grant permissions. They define the maximum permissions boundary for accounts in an organization or organizational unit. Even if an SCP allows an action, the action is still denied unless an IAM policy in the account also allows it. SCPs only restrict what IAM policies can grant; they cannot add permissions on their own.
   Further reading: [Service control policies (SCPs)](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html)

8. **Sample answer:** (1) Enable Multi-Factor Authentication (MFA) on the root user and all human users with console access. (2) Apply the principle of least privilege by granting only the minimum permissions required for each task. (3) Do not use the root user for daily tasks; create IAM users or use IAM Identity Center instead. Other valid answers include: rotate access keys regularly, remove unused users and credentials, use IAM roles instead of long-term access keys, and configure a strong password policy.
   Further reading: [Security best practices in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

9. **B) A role that allows an AWS service (such as EC2 or Lambda) to perform actions on your behalf using temporary credentials**
   A service role is an IAM role that an AWS service assumes to perform actions in your account. For example, you can create a role that allows an EC2 instance to read from S3 without storing access keys on the instance. Option A describes a cross-account role, not a service role. Option C is incorrect because no role grants additional permissions to the root user. Option D is incorrect because roles do not replace user groups; they serve a different purpose.
   Further reading: [IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html)

10. **C) No, because the SCP sets the maximum permissions boundary, and the deny in the SCP overrides the IAM allow**
    SCPs define the maximum permissions available to all users and roles in member accounts, including administrators and even the account's root user. If an SCP denies an action, that action is denied regardless of what IAM policies allow within the account. Option A is wrong because `AdministratorAccess` is still bounded by the SCP. Option B is wrong because SCPs affect all users and roles in member accounts, including administrators. Option D is wrong because SCPs do not modify or remove IAM policies; they act as an independent permissions boundary evaluated alongside IAM policies.
    Further reading: [Service control policies (SCPs)](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html)

</details>
