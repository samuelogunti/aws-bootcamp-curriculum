---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'IT Fundamentals: Module 04, APIs and Programming Basics'
---

# IT Fundamentals Module 04: APIs and Programming Basics

**Module 04 of 06**
Estimated lecture time: 15 to 20 minutes

<!-- Speaker notes: Welcome students to Module 04 of the IT Fundamentals primer. This module covers APIs and programming basics. The goal is recognition, not mastery. Students need to read Python code, not write it from scratch. Total lecture time is approximately 15 to 20 minutes. -->

---

## Learning Objectives

By the end of this module, you will be able to:

- Explain what an API is and how REST APIs use HTTP methods
- Summarize core programming concepts: variables, functions, loops, conditionals, and data structures

<!-- Speaker notes: Two objectives for this module, both at the Remember and Understand levels of Bloom's Taxonomy. These concepts connect directly to Lambda functions and IAM policies in the bootcamp. Approximately 1 minute on this slide. -->

---

## Prerequisites and Agenda

**Prerequisites:** Terminal skills (Module 02), HTTP context (Module 03), Python 3 installed

**Agenda:**
1. What is an API?
2. REST APIs and HTTP methods
3. JSON format and rules
4. Variables and data types
5. Functions
6. Conditionals (if/elif/else)
7. Loops
8. JSON for cloud configuration

<!-- Speaker notes: Module 02 is recommended for terminal skills and Module 03 for HTTP context. Python 3 is needed for the lab. Approximately 1 minute. -->

---

# What Is an API?

<!-- Speaker notes: Transition slide. An API (Application Programming Interface) defines how one program talks to another. In cloud computing, almost everything is accessed through APIs. -->

---

## APIs: How Programs Communicate

An API (Application Programming Interface) defines how one program talks to another. You send a request, the other program sends back a response.

- The AWS Console sends API calls to AWS services when you click buttons
- The AWS CLI sends API calls from your terminal
- Your application code sends API calls using SDKs

> In cloud computing, almost everything is accessed through APIs.

<!-- Speaker notes: When you click "Create bucket" in the S3 console, the console sends a REST API call to the S3 service on your behalf. The CLI does the same thing from the terminal. Approximately 2 minutes. -->

---

## REST APIs and HTTP Methods

REST APIs use URLs to identify resources and HTTP methods for actions:

| Method | Purpose | Example |
|--------|---------|---------|
| GET | Retrieve data | Fetch a user profile |
| POST | Create new data | Submit a registration form |
| PUT | Replace existing data | Update an entire record |
| DELETE | Remove data | Delete an account |

```bash
curl https://api.example.com/users/123
```

<!-- Speaker notes: REST stands for Representational State Transfer. Resources are identified by URLs, actions by HTTP methods, and data is exchanged in JSON. Approximately 3 minutes. -->

---

## API Response Example

```json
{
  "id": 123,
  "name": "Jane Smith",
  "email": "jane@example.com",
  "tags": ["developer", "cloud"]
}
```

- The server returns data in JSON format
- Status codes indicate success (200) or failure (403, 404, 500)
- Every AWS service has a REST API behind the scenes

> **Bootcamp connection:** Understanding APIs prepares you for building serverless APIs with Lambda and API Gateway in Module 09: Serverless (Lambda).

<!-- Speaker notes: In Module 05 (Storage: S3), students will interact with the S3 API to create buckets and upload objects. Approximately 2 minutes. -->

---

## JSON: The Language of APIs

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

**JSON rules:**
- Keys are always strings in double quotes
- Values: strings, numbers, booleans, arrays, objects, or null
- No trailing commas, no comments (unlike YAML)

<!-- Speaker notes: This is an actual IAM policy statement. Students will write these in Module 02 of the bootcamp. JSON is everywhere in AWS: IAM policies, CloudFormation templates, Lambda event payloads, API Gateway responses. Approximately 3 minutes. -->

---

## Discussion: You Call an API and Get a 403. What Happened?

You send a GET request to `https://api.example.com/admin/users` and receive a 403 Forbidden response.

**What does this tell you, and what would you check first?**

<!-- Speaker notes: Expected answer: A 403 means the server understood the request but refuses to authorize it. The client does not have permission to access that resource. Students should check: Are they authenticated? Do they have the correct role or permissions? Is there an IP restriction? This connects directly to IAM policies in Module 02 of the bootcamp. Give students 2 to 3 minutes to discuss. Approximately 5 minutes total. -->

---

# Programming Basics

<!-- Speaker notes: Transition slide. The goal is recognition, not mastery. Students need to read Python code, not write it from scratch. Lambda functions, automation scripts, and IaC templates all use these concepts. -->

---

## Variables and Data Types

```python
name = "Jane"              # String (text)
age = 28                   # Integer (whole number)
price = 19.99              # Float (decimal number)
is_active = True           # Boolean (true or false)
tags = ["web", "api"]      # List (ordered collection)
config = {"region": "us-east-1", "env": "prod"}  # Dictionary
```

- A variable stores a value that your program can use and change
- Python infers the type automatically (no type declarations needed)
- Dictionaries (key-value pairs) map directly to JSON objects

<!-- Speaker notes: Use simple examples. In Module 09, students will write Lambda functions that use variables to process event data. The dictionary type maps directly to JSON, which is why Python is popular for AWS work. Approximately 3 minutes. -->

---

## Functions

```python
def calculate_cost(hours, rate_per_hour):
    total = hours * rate_per_hour
    return total

monthly_cost = calculate_cost(730, 0.0116)  # 730 hours * $0.0116/hr
print(f"Monthly cost: ${monthly_cost:.2f}")  # Monthly cost: $8.47
```

- A function is a reusable block of code with inputs and outputs
- AWS Lambda runs your code inside a function that AWS invokes
- Every Lambda function has a handler that receives an event and returns a response

<!-- Speaker notes: Emphasize the connection to Lambda. The structure is the same: inputs (event), processing, output (response). Approximately 3 minutes. -->

---

## Conditionals

```python
instance_type = "t3.micro"
if instance_type.startswith("t3"):
    print("Burstable instance")
elif instance_type.startswith("m6"):
    print("General purpose instance")
else:
    print("Other instance type")
```

- `if` checks a condition; `elif` checks additional conditions; `else` is the fallback
- Conditionals let your code make decisions based on data
- Lambda functions use conditionals to handle different event types

<!-- Speaker notes: Conditionals are used in Lambda functions to route different event types (S3 events vs API Gateway events vs scheduled events). Approximately 2 minutes. -->

---

## Loops

```python
regions = ["us-east-1", "eu-west-1", "ap-southeast-1"]
for region in regions:
    print(f"Deploying to {region}")
```

- Loops repeat an action for each item in a collection
- The `for` loop is the most common loop in Python
- In the bootcamp, you will loop over AWS resources, regions, and configuration items

<!-- Speaker notes: Show how loops iterate over lists. Keep it simple. The lab includes a loop in the Python script exercise. Approximately 2 minutes. -->

---

## JSON for Cloud Configuration

```json
{
  "Effect": "Allow",
  "Action": "s3:GetObject",
  "Resource": "arn:aws:s3:::my-bucket/*"
}
```

- IAM policies, CloudFormation templates, and API responses all use JSON
- You will read and write JSON constantly in the bootcamp
- Get comfortable with nested objects and arrays now

> **Bootcamp connection:** Understanding programming basics prepares you for Lambda functions in Module 09 and infrastructure-as-code with CDK in Module 11.

<!-- Speaker notes: This JSON example is an IAM policy statement. Getting comfortable with JSON syntax now saves time later. Approximately 2 minutes. -->

---

## Instructor Notes: Common Questions

**Q: Which programming language should I learn?**
Python is the most common for AWS Lambda and automation. The bootcamp uses Python. If you know JavaScript, the concepts transfer directly.

**Q: Why does JSON not allow trailing commas or comments?**
JSON is a strict data interchange format (RFC 8259). YAML, used in SAM templates, does allow comments.

<!-- Speaker notes: Reassure students that the goal is recognition, not mastery. They need to read Python code, not write it from scratch at this stage. Approximately 2 minutes. -->

---

## Key Takeaways

- APIs allow programs to communicate. AWS services are accessed through REST APIs.
- REST uses HTTP methods: GET (read), POST (create), PUT (update), DELETE (remove)
- JSON is the universal data format for cloud APIs and configuration
- Variables store values; functions are reusable code blocks; loops repeat actions
- Conditionals (if/elif/else) let code make decisions
- Python dictionaries map directly to JSON objects
- Lambda functions use all of these concepts

<!-- Speaker notes: Seven key takeaways covering APIs, JSON, and all programming concepts. Approximately 1 minute. -->

---

## Lab Preview and Questions

**Lab 00: APIs, JSON, and Python Scripting**

What you will do:
- Create and read JSON files
- Call a public API with `curl`
- Write and run a Python script with variables, a function, and a loop

**Duration:** 15 to 18 minutes
**No cloud resources created. Everything runs on your local machine.**

Questions?

<!-- Speaker notes: Students need Python 3 installed for the second half of the lab. On Windows, the command is python instead of python3. Take 2 to 3 minutes for questions before transitioning to the lab. -->
