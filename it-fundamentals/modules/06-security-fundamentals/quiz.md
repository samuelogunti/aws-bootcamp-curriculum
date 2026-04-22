# IT Fundamentals Module 06: Quiz

1. In your own words, explain the difference between authentication and authorization.

2. True or False: The principle of least privilege means granting users only the minimum permissions they need to do their job.

---

<details>
<summary>Answer Key</summary>

1. **Sample answer:** Authentication is the process of verifying a user's identity, answering the question "Who are you?" (for example, logging in with a username and password). Authorization is the process of determining what an authenticated user is allowed to do, answering the question "What are you allowed to do?" (for example, an IAM policy that grants read-only access to S3). Authentication always happens first, then authorization determines access.
   Further reading: [NIST Glossary: Authentication](https://csrc.nist.gov/glossary/term/authentication)

2. **True.**
   The principle of least privilege states that users and applications should be granted only the minimum permissions necessary to perform their tasks. For example, a web server that reads from S3 should not have permission to delete S3 objects. This limits the potential damage if credentials are compromised. In AWS, you enforce least privilege through IAM policies.
   Further reading: [What Is the Principle of Least Privilege? (Cloudflare Learning Center)](https://www.cloudflare.com/learning/access-management/principle-of-least-privilege/)

</details>
