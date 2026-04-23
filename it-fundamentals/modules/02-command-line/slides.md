---
marp: true
theme: default
paginate: true
size: 16:9
header: 'AWS Bootcamp'
footer: 'IT Fundamentals: Module 02, The Command Line'
---

# IT Fundamentals Module 02: The Command Line

**Module 02 of 06**
Estimated lecture time: 15 to 20 minutes

<!-- Speaker notes: Welcome students to Module 02 of the IT Fundamentals primer. This module covers the command line, which is the primary way you interact with cloud servers. Total lecture time is approximately 15 to 20 minutes. If time allows, do a live demo alongside the slides. -->

---

## Learning Objectives

By the end of this module, you will be able to:

- Use the command line to navigate the file system, create files, and run basic commands

<!-- Speaker notes: One objective for this module, at the Understand and Apply levels of Bloom's Taxonomy. The command line is a skill that improves with practice, so the lab is especially important for this module. Approximately 1 minute on this slide. -->

---

## Prerequisites and Agenda

**Prerequisites:** A terminal application (Terminal on macOS, Git Bash on Windows, terminal on Linux)

> **Tip:** On Windows, use Git Bash or WSL for command consistency with macOS and Linux.

**Agenda:**
1. Why the command line matters
2. Navigation commands
3. File operation commands
4. Searching and filtering
5. Tips and shortcuts

<!-- Speaker notes: Module 01 is recommended but not required. Emphasize that Git Bash on Windows provides the same commands as macOS and Linux. Approximately 1 minute. -->

---

# The Command Line

<!-- Speaker notes: Transition slide. The command line (also called the terminal or shell) lets you interact with the computer by typing text commands instead of clicking icons. -->

---

## Why the Command Line Matters

- Cloud servers run "headless" (no graphical interface), so you manage them through the command line
- The AWS CLI (Command Line Interface) manages AWS resources from the terminal
- Automation scripts and infrastructure-as-code tools use command-line commands
- The command line is faster than a graphical interface for many tasks

> In Module 01 of the bootcamp, you will use AWS CloudShell, a browser-based Linux terminal.

<!-- Speaker notes: Ask students to open a terminal on their own machines if they have one available. On macOS, open Terminal.app. On Windows, open Git Bash. On Linux, open the default terminal. Approximately 3 minutes. -->

---

## Navigation Commands

```bash
pwd                    # Print working directory (where am I?)
ls                     # List files in the current directory
ls -la                 # List all files including hidden ones, with details
cd documents           # Change directory to "documents"
cd ..                  # Go up one directory
cd ~                   # Go to your home directory
```

- `pwd` tells you where you are in the file system
- `ls -la` shows hidden files (starting with `.`) and file permissions
- `cd ..` moves up one level; `cd ~` always takes you home

<!-- Speaker notes: Walk through each command if doing a live demo. Emphasize that cd .. moves up one level and cd ~ always takes you home. These two shortcuts are essential for navigation. Approximately 4 minutes. -->

---

## File Operation Commands

```bash
mkdir my-project       # Create a new directory
touch index.html       # Create an empty file
cp file.txt backup.txt # Copy a file
mv old.txt new.txt     # Rename (move) a file
rm file.txt            # Delete a file (no undo)
rm -r my-folder        # Delete a directory and its contents
cat file.txt           # Display file contents
```

- `rm` deletes permanently; there is no recycle bin on the command line
- `rm -r` recursively deletes a directory and everything inside it
- On Windows, use Git Bash for consistent command syntax

<!-- Speaker notes: Stress that rm has no undo. This is a common mistake for beginners. In the lab, students will practice all of these commands in a safe directory. Approximately 4 minutes. -->

---

## Searching and Filtering

```bash
grep "error" log.txt       # Search for "error" in a file
grep -r "TODO" .           # Search recursively in all files
find . -name "*.txt"       # Find all .txt files in the directory tree
```

- `grep` searches for text patterns inside files
- `find` locates files by name, type, or other attributes
- Both commands are essential for troubleshooting in the bootcamp

<!-- Speaker notes: grep and find are used constantly when debugging cloud applications. In Module 14 (Monitoring and Observability), students will search through CloudWatch logs. Approximately 2 minutes. -->

---

## Tips and Shortcuts

| Shortcut | What It Does |
|----------|-------------|
| Up arrow | Recall previous commands |
| Tab | Auto-complete file and directory names |
| Ctrl+C | Cancel a running command |
| Ctrl+L | Clear the terminal screen |
| `history` | Show command history |

- Tab completion saves significant typing and prevents typos
- Ctrl+C is your escape hatch when a command hangs

<!-- Speaker notes: Demonstrate Tab completion live if possible. Type the first few letters of a filename and press Tab. This is one of the most useful shortcuts for beginners. Approximately 2 minutes. -->

---

## Windows vs. macOS/Linux Commands

| Task | macOS/Linux | Windows PowerShell |
|------|------------|-------------------|
| Print directory | `pwd` | `Get-Location` |
| List files | `ls` | `Get-ChildItem` |
| Create file | `touch file.txt` | `New-Item file.txt` |
| Delete file | `rm file.txt` | `Remove-Item file.txt` |
| Display file | `cat file.txt` | `Get-Content file.txt` |

> **Recommendation:** Use Git Bash on Windows so all commands match the bootcamp examples.

<!-- Speaker notes: This table shows why Git Bash is recommended. PowerShell commands are completely different. Git Bash provides the same Unix-style commands on Windows. Approximately 2 minutes. -->

---

## Bootcamp Connection

> **Bootcamp connection:** Understanding the command line prepares you for using AWS CloudShell in Module 01: Cloud Fundamentals and connecting to EC2 instances via EC2 Instance Connect in Module 04: Compute (EC2).

- AWS CloudShell is a browser-based Linux terminal
- EC2 Instance Connect gives you a terminal on a cloud server
- The AWS CLI uses the same terminal you are learning now

<!-- Speaker notes: Every module in the bootcamp uses the command line in some form. CloudShell, EC2 Instance Connect, and the AWS CLI all require terminal skills. Approximately 1 minute. -->

---

## Discussion: What Does `rm -r` Do and Why Should You Be Careful?

The command `rm -r my-folder` deletes a directory and everything inside it.

**Why is this command potentially dangerous, and what precautions should you take before running it?**

<!-- Speaker notes: Expected answers include: rm -r recursively deletes all files and subdirectories without confirmation, there is no undo or recycle bin, a typo could delete the wrong directory, and running it with sudo or as root could damage the system. Precautions: double-check the path, use ls first to verify contents, avoid running rm -r on broad paths like / or ~. Give students 2 to 3 minutes to discuss, then summarize. Approximately 5 minutes total. -->

---

## Instructor Notes: Common Questions

**Q: Do I need to memorize all the commands?**
No. Focus on `pwd`, `ls`, `cd`, `mkdir`, `cat`, and `rm`. You will use them so often they become second nature. Keep a reference sheet handy.

**Q: Should I use Git Bash or PowerShell on Windows?**
Git Bash is recommended. It provides a Unix-style terminal that matches the commands used on macOS, Linux, and in the bootcamp labs.

<!-- Speaker notes: These are the two most common questions. Reassure students that muscle memory matters more than memorization. Approximately 2 minutes. -->

---

## Key Takeaways

- The command line is essential for cloud computing because most cloud servers have no graphical interface
- Core navigation: `pwd`, `ls`, `cd`, `cd ..`, `cd ~`
- Core file operations: `mkdir`, `touch`, `cp`, `mv`, `rm`, `cat`
- Searching: `grep` for text patterns, `find` for files
- Use Git Bash on Windows for consistent command syntax
- Tab completion and up arrow are your best friends

<!-- Speaker notes: Reinforce that these commands will be used throughout the bootcamp. Muscle memory matters more than memorization. Approximately 1 minute. -->

---

## Lab Preview and Questions

**Lab 00: Command Line Navigation and File Operations**

What you will do:
- Navigate the file system with `pwd`, `ls`, and `cd`
- Create a workspace directory with `mkdir`
- Create, copy, rename, and delete files
- Display file contents with `cat`

**Duration:** 10 to 12 minutes
**No cloud resources created. Everything runs on your local machine.**

Questions?

<!-- Speaker notes: Walk through the lab structure briefly. Remind students that the lab is fully guided with exact commands and expected output for every step. Students using Windows should use Git Bash for command consistency. Take 2 to 3 minutes for questions before transitioning to the lab. -->
