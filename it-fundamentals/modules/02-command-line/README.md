# IT Fundamentals Module 02: The Command Line (Module 02 of 06)

## Learning Objectives

By the end of this module, you will be able to:

- Use the command line to navigate the file system, create files, and run basic commands

## Prerequisites

- A computer with internet access
- A terminal application (Terminal on macOS, Git Bash on Windows, terminal on Linux)
- Module 01 recommended but not required

> **Tip:** On Windows, use Git Bash or Windows Subsystem for Linux (WSL) for command consistency with macOS and Linux. The commands in this module and the bootcamp labs assume a Unix-style terminal.

**Estimated self-study time:**

| Activity | Estimated Time |
|----------|---------------|
| Reading | 8 to 12 minutes |
| Lab | 10 to 12 minutes |
| Quiz | 3 to 5 minutes |
| Total | 21 to 29 minutes |

## Concepts

### The Command Line

The command line (also called the terminal or shell) lets you interact with the computer by typing text commands instead of clicking icons. Cloud computing relies heavily on the command line because most cloud servers do not have a graphical interface.

#### Why the Command Line Matters

- Cloud servers typically run "headless" (no graphical interface), so you manage them through the command line
- The AWS CLI (Command Line Interface) is a command-line tool for managing AWS resources
- Automation scripts and infrastructure-as-code tools use command-line commands
- The command line is faster than a graphical interface for many tasks once you learn the basics

#### Essential Commands

These commands work on Linux, macOS, and Windows (using Git Bash or WSL).

> **Tip:** On Windows, use Git Bash or Windows Subsystem for Linux (WSL) so that the commands below work the same way as on macOS and Linux. PowerShell and Command Prompt use different syntax for many operations.

**Navigation:**

```bash
pwd                    # Print working directory (where am I?)
ls                     # List files in the current directory
ls -la                 # List all files including hidden ones, with details
cd documents           # Change directory to "documents"
cd ..                  # Go up one directory
cd ~                   # Go to your home directory
```

**File operations:**

```bash
mkdir my-project       # Create a new directory
touch index.html       # Create an empty file
cp file.txt backup.txt # Copy a file
mv old.txt new.txt     # Rename (move) a file
rm file.txt            # Delete a file (no undo)
rm -r my-folder        # Delete a directory and its contents
cat file.txt           # Display file contents
```

**Searching and filtering:**

```bash
grep "error" log.txt   # Search for "error" in a file
find . -name "*.txt"   # Find all .txt files in the current directory tree
```

> **Tip:** Use the up arrow key to recall previous commands. Use Tab to auto-complete file and directory names. These two shortcuts will save you significant typing.

> **Bootcamp connection:** Understanding the command line prepares you for using AWS CloudShell in Module 01: Cloud Fundamentals and connecting to EC2 instances via EC2 Instance Connect in Module 04: Compute (EC2).

## Instructor Notes

**Estimated lecture time:** 15 to 20 minutes

**Common student questions:**

- Q: Do I need to memorize all the commands?
  A: No. Focus on the most common ones: `pwd`, `ls`, `cd`, `mkdir`, `cat`, and `rm`. You will use them so often that they become second nature. Keep a reference sheet handy for less common commands.

- Q: Should I use Git Bash or PowerShell on Windows?
  A: Git Bash is recommended for this module and the bootcamp. It provides a Unix-style terminal that matches the commands used on macOS and Linux. PowerShell uses different syntax for many operations, which can cause confusion when following along with examples.

**Teaching tips:**

- The command-line section works best as a live demo. Open a terminal, navigate around, create files, and let students follow along. Muscle memory matters more than memorization here.
- Emphasize that `rm` has no undo. This is a common mistake for beginners. Encourage students to double-check filenames before pressing Enter.

**Pause point:**

- Ask students to open a terminal and run `pwd` and `ls`.

## Key Takeaways

- The command line is essential for cloud computing because most cloud servers have no graphical interface. Learn `cd`, `ls`, `mkdir`, `cat`, `grep`, and `rm`.

---

[Previous: Module 01, Computers and Operating Systems](../01-computers-and-operating-systems/README.md) | [Next: Module 03, Networking and the Internet](../03-networking-and-internet/README.md) | [IT Fundamentals Overview](../../README.md)
