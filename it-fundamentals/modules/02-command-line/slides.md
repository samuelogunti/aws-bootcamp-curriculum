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

## The Command Line: Why It Matters

- Cloud servers run "headless" (no graphical interface)
- The AWS CLI manages AWS resources from the terminal
- Automation scripts and infrastructure-as-code tools use commands
- Faster than a graphical interface for many tasks

> In Module 01 of the bootcamp, you will use AWS CloudShell, a browser-based Linux terminal.

<!-- Speaker notes: Ask students to open a terminal on their own machines if they have one available. On macOS, open Terminal.app. On Windows, open Git Bash. On Linux, open the default terminal. Approximately 3 minutes. -->

---

## Navigation Commands

```bash
pwd                    # Print working directory (where am I?)
ls                     # List files in the current directory
ls -la                 # List all files with details
cd documents           # Change into the "documents" directory
cd ..                  # Go up one directory
cd ~                   # Go to your home directory
```

- Use the **up arrow** to recall previous commands
- Use **Tab** to auto-complete file and directory names

<!-- Speaker notes: Walk through each command if doing a live demo. Emphasize that cd .. moves up one level and cd ~ always takes you home. These two shortcuts are essential for navigation. Approximately 4 minutes. -->

---

## File Operation Commands

```bash
mkdir my-project       # Create a new directory
touch index.html       # Create an empty file
cp file.txt backup.txt # Copy a file
mv old.txt new.txt     # Rename (move) a file
rm file.txt            # Delete a file (no undo)
cat file.txt           # Display file contents
```

- `rm` deletes permanently; there is no recycle bin on the command line
- On Windows, use Git Bash for consistent command syntax

<!-- Speaker notes: Stress that rm has no undo. This is a common mistake for beginners. In the lab, students will practice all of these commands in a safe directory. Approximately 4 minutes. -->

---

## Discussion: What Does `rm -r` Do and Why Should You Be Careful?

The command `rm -r my-folder` deletes a directory and everything inside it.

**Why is this command potentially dangerous, and what precautions should you take before running it?**

<!-- Speaker notes: Expected answers include: rm -r recursively deletes all files and subdirectories without confirmation, there is no undo or recycle bin, a typo could delete the wrong directory, and running it with sudo or as root could damage the system. Precautions: double-check the path, use ls first to verify contents, avoid running rm -r on broad paths like / or ~. Give students 2 to 3 minutes to discuss, then summarize. Approximately 5 minutes total. -->

---

## Key Takeaways

- The command line is essential for cloud computing because most cloud servers have no graphical interface.
- Learn `cd`, `ls`, `mkdir`, `cat`, `grep`, and `rm` as your core toolkit.

<!-- Speaker notes: Reinforce that these commands will be used throughout the bootcamp. Muscle memory matters more than memorization. Approximately 1 minute. -->

---

## Lab Preview and Questions

**Lab 00: Command Line Navigation and File Operations**

What you will do:
- Navigate the file system with `pwd`, `ls`, and `cd`
- Create a workspace directory with `mkdir`
- Create, copy, rename, and delete files

**Duration:** 10 to 12 minutes
**No cloud resources created. Everything runs on your local machine.**

Questions?

<!-- Speaker notes: Walk through the lab structure briefly. Remind students that the lab is fully guided with exact commands and expected output for every step. Students using Windows should use Git Bash for command consistency. Take 2 to 3 minutes for questions before transitioning to the lab. -->
