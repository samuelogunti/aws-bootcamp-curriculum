# IT Fundamentals Module 04: APIs and Programming Basics (Module 04 of 06)

## Learning Objectives

By the end of this module, you will be able to:

- Explain what an API is and how REST APIs use HTTP methods
- Summarize core programming concepts including variables, functions, loops, and data structures

## Prerequisites

- A computer with internet access
- A terminal application (Terminal on macOS, Git Bash on Windows, terminal on Linux)
- Python 3 installed ([python.org](https://www.python.org/downloads/))
- Module 02 recommended for terminal skills
- Module 03 helpful for HTTP context

**Estimated self-study time:**

| Activity | Estimated Time |
|----------|---------------|
| Reading | 12 to 18 minutes |
| Lab | 15 to 18 minutes |
| Quiz | 3 to 5 minutes |
| Total | 30 to 41 minutes |

## Concepts

### What Is an API?

An [API (Application Programming Interface)](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Introduction) defines how one program talks to another. You send a request, and the other program sends back a response. In cloud computing, almost everything is accessed through APIs.

#### REST APIs

Most web APIs follow the REST (Representational State Transfer) pattern:

- Resources are identified by URLs: `https://api.example.com/users/123`
- Actions are expressed through HTTP methods: GET to read, POST to create, PUT to update, DELETE to remove
- Data is exchanged in JSON format

Example: retrieving a user from an API:

```bash
curl https://api.example.com/users/123
```

Response:
```json
{
  "id": 123,
  "name": "Jane Smith",
  "email": "jane@example.com"
}
```

The AWS CLI and AWS Management Console both communicate with AWS through REST APIs. When you click "Create bucket" in the S3 console, the console sends a REST API call to the S3 service on your behalf.

> **Bootcamp connection:** Understanding APIs prepares you for building serverless APIs with Lambda and API Gateway in Module 09: Serverless (Lambda). In Module 05: Storage (S3), you will interact with the S3 API to create buckets, upload objects, and manage storage.

### Programming Basics

You do not need to be an expert programmer for the AWS Bootcamp, but you need to understand core concepts because Lambda functions, automation scripts, and infrastructure-as-code templates all use them.

#### Variables and Data Types

A variable stores a value that your program can use and change.

```python
# Python examples
name = "Jane"              # String (text)
age = 28                   # Integer (whole number)
price = 19.99              # Float (decimal number)
is_active = True           # Boolean (true or false)
tags = ["web", "api"]      # List (ordered collection)
config = {"region": "us-east-1", "env": "prod"}  # Dictionary (key-value pairs)
```

#### Functions

A function is a reusable block of code that performs a specific task.

```python
def calculate_cost(hours, rate_per_hour):
    total = hours * rate_per_hour
    return total

monthly_cost = calculate_cost(730, 0.0116)  # 730 hours * $0.0116/hr
print(f"Monthly cost: ${monthly_cost:.2f}")  # Monthly cost: $8.47
```

In AWS Lambda, your code runs inside a function that AWS invokes when an event occurs.

#### Loops and Conditionals

```python
# Conditional: make a decision
instance_type = "t3.micro"
if instance_type.startswith("t3"):
    print("Burstable instance")
elif instance_type.startswith("m6"):
    print("General purpose instance")
else:
    print("Other instance type")

# Loop: repeat an action
regions = ["us-east-1", "eu-west-1", "ap-southeast-1"]
for region in regions:
    print(f"Deploying to {region}")
```

#### JSON

JSON (JavaScript Object Notation) is the standard data format for APIs and configuration files in cloud computing. AWS IAM policies, CloudFormation templates, and API responses all use JSON.

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

JSON rules:
- Keys are always strings in double quotes
- Values can be strings, numbers, booleans, arrays, objects, or null
- No trailing commas
- No comments (unlike YAML)

> **Tip:** You will read and write JSON constantly in the AWS Bootcamp. Get comfortable with the syntax now, especially nested objects and arrays.

> **Bootcamp connection:** Understanding programming basics prepares you for writing Lambda functions in Module 09: Serverless (Lambda). In Module 11: Infrastructure as Code, you will use programming languages (Python, TypeScript) with the AWS Cloud Development Kit (CDK) to define cloud infrastructure.

## Instructor Notes

**Estimated lecture time:** 15 to 20 minutes

**Common student questions:**

- Q: Which programming language should I learn?
  A: Python is the most common language for AWS Lambda functions and automation scripts. The bootcamp uses Python for code examples. If you already know JavaScript or another language, the concepts transfer directly.

- Q: Why does JSON not allow trailing commas or comments?
  A: JSON is a strict data interchange format designed for machine parsing. Trailing commas and comments are not part of the JSON specification (RFC 8259). YAML, which is used in some AWS configurations (like SAM templates), does allow comments.

**Teaching tips:**

- Use simple examples for programming concepts; the goal is recognition, not mastery. Students need to read Python code, not write it from scratch at this stage.
- Show how JSON appears in IAM policies to connect the programming concepts to real AWS usage.

**Pause point:**

- After the APIs section, ask students what a 403 status code means (connects to Module 03).

## Key Takeaways

- APIs allow programs to communicate with each other. AWS services are accessed through REST APIs, whether you use the console, the CLI, or code.
- JSON is the universal data format for cloud APIs and configuration. Get comfortable reading and writing it.

---

[Previous: Module 03, Networking and the Internet](../03-networking-and-internet/README.md) | [Next: Module 05, Version Control with Git](../05-version-control-git/README.md) | [IT Fundamentals Overview](../../README.md)
