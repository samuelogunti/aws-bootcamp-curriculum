# Lab 00: APIs, JSON, and Python Scripting

## Objective

Create and read JSON files, call a public API that returns JSON, and write and run a Python script that uses variables, a function, and a loop.

## Prerequisites

- A terminal application:
  - **macOS:** Terminal.app (pre-installed in Applications > Utilities)
  - **Windows:** Git Bash (bundled with [Git for Windows](https://git-scm.com/download/win))
  - **Linux:** Terminal (pre-installed)
- Module 02 (The Command Line) for basic terminal skills
- Python 3 installed:
  - **macOS:** `brew install python3` or download from [python.org](https://www.python.org/downloads/)
  - **Windows:** Download from [python.org](https://www.python.org/downloads/)
  - **Linux:** `sudo apt install python3` or `sudo dnf install python3`

## Duration

15 to 18 minutes

## Instructions

### Step 1: Work with JSON (~7-8 min)

In this step you create a JSON file, display its contents, and call a public API that returns JSON.

**Create a working directory:**

```bash
mkdir -p it-fundamentals-lab
cd it-fundamentals-lab
```

**Create a JSON file:**

```bash
cat > data.json << 'EOF'
{
  "name": "IT Fundamentals Lab",
  "version": 1,
  "topics": ["command line", "git", "networking", "json", "python"],
  "completed": false
}
EOF
```

This command creates a file called `data.json` and writes the JSON content into it. It produces no output.

> **Tip:** The `<< 'EOF'` syntax is called a "here document." It lets you write multiple lines of text into a file. Everything between `<< 'EOF'` and the closing `EOF` is written to the file.

**Display the JSON file:**

```bash
cat data.json
```

Expected output:

```json
{
  "name": "IT Fundamentals Lab",
  "version": 1,
  "topics": ["command line", "git", "networking", "json", "python"],
  "completed": false
}
```

This is valid JSON. Notice the structure: keys are strings in double quotes, values can be strings, numbers, arrays (lists in square brackets), or booleans (`true`/`false`).

**Call a public API that returns JSON:**

```bash
curl -s https://httpbin.org/json
```

Expected output:

```json
{
  "slideshow": {
    "author": "Yours Truly",
    "date": "date of publication",
    "slides": [
      {
        "title": "Wake up to WonderWidgets!",
        "type": "all"
      },
      {
        "items": [
          "Why <em>WonderWidgets</em> are great",
          "Who <em>buys</em> WonderWidgets"
        ],
        "title": "Overview",
        "type": "all"
      }
    ],
    "title": "Sample Slide Show"
  }
}
```

This API returns a sample JSON response with nested objects and arrays. In the AWS Bootcamp, you will work with JSON constantly: IAM policies, CloudFormation templates, and API responses all use this format.

### Step 2: Write and Run a Python Script (~8-10 min)

In this step you create a Python script that uses variables, a function, and a loop, then run it from the command line.

**Create the Python script:**

```bash
cat > hello.py << 'EOF'
# IT Fundamentals Lab - Python Script

# Variables
student_name = "Bootcamp Student"
topics = ["Cloud", "IAM", "VPC", "EC2", "S3"]

# Function
def greet(name):
    return f"Welcome to the AWS Bootcamp, {name}"

# Print greeting
message = greet(student_name)
print(message)

# Loop
print("\nBootcamp topics you will learn:")
for i, topic in enumerate(topics, 1):
    print(f"  {i}. {topic}")

print(f"\nTotal topics: {len(topics)}")
EOF
```

This command creates a file called `hello.py` with a complete Python script. It produces no output.

**View the script to confirm it was created:**

```bash
cat hello.py
```

Expected output:

```python
# IT Fundamentals Lab - Python Script

# Variables
student_name = "Bootcamp Student"
topics = ["Cloud", "IAM", "VPC", "EC2", "S3"]

# Function
def greet(name):
    return f"Welcome to the AWS Bootcamp, {name}"

# Print greeting
message = greet(student_name)
print(message)

# Loop
print("\nBootcamp topics you will learn:")
for i, topic in enumerate(topics, 1):
    print(f"  {i}. {topic}")

print(f"\nTotal topics: {len(topics)}")
```

**Run the script:**

**macOS/Linux:**

```bash
python3 hello.py
```

**Windows (Git Bash or PowerShell):**

```bash
python hello.py
```

> **Tip:** On Windows, the Python installer registers the command as `python` (not `python3`). If `python` does not work, try `python3`. If neither works, verify that Python is installed by running `python --version` or `python3 --version`.

Expected output:

```
Welcome to the AWS Bootcamp, Bootcamp Student

Bootcamp topics you will learn:
  1. Cloud
  2. IAM
  3. VPC
  4. EC2
  5. S3

Total topics: 5
```

This script demonstrates three core programming concepts:

- **Variables:** `student_name` stores a string, `topics` stores a list.
- **Function:** `greet()` takes a name as input and returns a greeting string.
- **Loop:** The `for` loop iterates over the `topics` list and prints each item with a number.

These are the same concepts you will use when writing AWS Lambda functions in Module 09: Serverless (Lambda).

## Validation

Confirm the following before moving on:

- [ ] The `data.json` file contains valid JSON with 5 topics
- [ ] Running `curl -s https://httpbin.org/json` returns a JSON response with nested objects
- [ ] Running `python3 hello.py` (or `python hello.py` on Windows) prints the greeting and 5 topics

## Cleanup

Remove the lab directory when you are finished:

**macOS/Linux:**

```bash
cd ~
rm -r it-fundamentals-lab
```

**Windows (Git Bash):**

```bash
cd ~
rm -r it-fundamentals-lab
```

**Windows (PowerShell):**

```powershell
cd $HOME
Remove-Item -Recurse -Force it-fundamentals-lab
```

No cloud resources were created during this lab. No further cleanup is needed.

## Challenge (Optional)

1. Write a Python script that reads `data.json` and prints each topic. Hint: use `import json`, then `json.load()` to read the file, and a `for` loop to print each item in the `topics` list.

2. Use `curl` to call `https://httpbin.org/headers` and save the response to a file called `headers.json`:

   ```bash
   curl -s https://httpbin.org/headers > headers.json
   cat headers.json
   ```

3. Modify `hello.py` to add a dictionary variable and print its key-value pairs using a loop.

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
