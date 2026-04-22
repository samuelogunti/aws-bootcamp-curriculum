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
- Summarize core programming concepts: variables, functions, loops

<!-- Speaker notes: Two objectives for this module, both at the Remember and Understand levels of Bloom's Taxonomy. These concepts connect directly to Lambda functions and IAM policies in the bootcamp. Approximately 1 minute on this slide. -->

---

## APIs: REST and HTTP Methods

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

<!-- Speaker notes: Explain that the AWS Management Console, AWS CLI, and SDKs all communicate with AWS through REST APIs. When you click "Create bucket" in the S3 console, it sends a REST API call to the S3 service. Approximately 4 minutes. -->

---

## JSON: The Language of APIs

```json
{
  "id": 123,
  "name": "Jane Smith",
  "email": "jane@example.com",
  "tags": ["developer", "cloud"]
}
```

- Keys are always strings in double quotes
- Values can be strings, numbers, booleans, arrays, or objects
- No trailing commas, no comments
- AWS IAM policies, CloudFormation templates, and API responses all use JSON

<!-- Speaker notes: JSON is the most common data format students will encounter in the bootcamp. IAM policies, Lambda event payloads, and API Gateway responses are all JSON. Encourage students to get comfortable reading nested JSON structures. Approximately 4 minutes. -->

---

## Discussion: You Call an API and Get a 403. What Happened?

You send a GET request to `https://api.example.com/admin/users` and receive a 403 Forbidden response.

**What does this tell you, and what would you check first?**

<!-- Speaker notes: Expected answer: A 403 means the server understood the request but refuses to authorize it. The client does not have permission to access that resource. Students should check: Are they authenticated? Do they have the correct role or permissions? Is there an IP restriction? This connects directly to IAM policies in Module 02 of the bootcamp, where a misconfigured policy results in Access Denied (403). Give students 2 to 3 minutes to discuss. Approximately 5 minutes total. -->

---

# Programming Basics

<!-- Speaker notes: Transition slide. This section takes approximately 10 minutes across four slides. The goal is recognition, not mastery. Students need to read Python code, not write it from scratch. -->

---

## Variables and Data Types

```python
name = "Jane"              # String (text)
age = 28                   # Integer (whole number)
price = 19.99              # Float (decimal number)
is_active = True           # Boolean (true or false)
tags = ["web", "api"]      # List (ordered collection)
```

- A variable stores a value that your program can use and change
- Python does not require you to declare the type; it is inferred

<!-- Speaker notes: Use simple examples. The goal is recognition, not mastery. In Module 09, students will write Lambda functions that use variables to process event data. Approximately 3 minutes. -->

---

## Functions

```python
def calculate_cost(hours, rate_per_hour):
    total = hours * rate_per_hour
    return total

monthly_cost = calculate_cost(730, 0.0116)
print(f"Monthly cost: ${monthly_cost:.2f}")
```

- A function is a reusable block of code that performs a specific task
- Functions take inputs (parameters) and return outputs
- AWS Lambda runs your code inside a function that AWS invokes

<!-- Speaker notes: Emphasize the connection to Lambda. Every Lambda function has a handler function that receives an event and returns a response. The structure is the same: inputs, processing, output. Approximately 3 minutes. -->

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

- Keys are always strings in double quotes
- Values can be strings, numbers, booleans, arrays, or objects
- JSON is the standard data format for cloud APIs and configuration

<!-- Speaker notes: This JSON example is an IAM policy statement, which students will write in Module 02 of the bootcamp. Getting comfortable with JSON syntax now saves time later. Approximately 2 minutes. -->

---

## Key Takeaways

- APIs allow programs to communicate. AWS services are accessed through REST APIs.
- JSON is the universal data format for cloud APIs and configuration.
- Programming basics (variables, functions, loops) are the building blocks of Lambda functions.

<!-- Speaker notes: Three key takeaways for this module. Reinforce that every concept covered today connects directly to a bootcamp module. Approximately 1 minute. -->

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

<!-- Speaker notes: Walk through the lab structure briefly. Remind students that the lab is fully guided with exact commands and expected output for every step. Students need Python 3 installed for the second half of the lab. On Windows, the command is python instead of python3. Take 2 to 3 minutes for questions before transitioning to the lab. -->
