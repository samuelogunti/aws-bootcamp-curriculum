# Lab 00: Security Fundamentals Hands-On Exercises

## Objective

Explore user identity, file permissions, and environment variables to understand security concepts on your local machine.

## Prerequisites

- A terminal application:
  - **macOS:** Terminal.app (pre-installed in Applications > Utilities)
  - **Windows:** Git Bash (bundled with [Git for Windows](https://git-scm.com/download/win))
  - **Linux:** Terminal (pre-installed)
- Module 02 (The Command Line) for basic terminal skills

## Duration

5 to 8 minutes

## Instructions

### Step 1: Identify Your User Identity (~2 min)

In this step you discover which user account you are logged in as. Understanding user identity is the foundation of authentication.

**Display your username:**

**macOS/Linux:**

```bash
whoami
```

Expected output (your username will differ):

```
student
```

**Display detailed user and group information:**

```bash
id
```

Expected output (your IDs and groups will differ):

```
uid=501(student) gid=20(staff) groups=20(staff),12(everyone)
```

The `uid` is your user ID, `gid` is your primary group ID, and `groups` lists all groups you belong to. On Linux servers, these IDs determine what files and resources you can access.

**Windows (Git Bash):**

```bash
whoami
```

Expected output (your machine name and username will differ):

```
desktop-abc123\student
```

On Windows, the output includes the machine name followed by a backslash and your username.

### Step 2: Examine File Permissions (~3 min)

In this step you create a file and examine its permissions. File permissions control who can read, write, and execute files.

**List files with permission details:**

```bash
ls -la
```

This shows all files in the current directory with their permissions, owner, group, size, and modification date.

**Create a file with sensitive content:**

```bash
echo "secret data" > secret.txt
```

This command produces no output.

**View the file permissions:**

```bash
ls -la secret.txt
```

Expected output (date and user will differ):

```
-rw-r--r--  1 student  staff  12 Jan  1 12:00 secret.txt
```

The permission string `-rw-r--r--` breaks down as follows:
- `-` means this is a regular file (not a directory)
- `rw-` means the owner can read and write
- `r--` means the group can read only
- `r--` means everyone else can read only

**Restrict the file to owner-only access (macOS/Linux):**

```bash
chmod 600 secret.txt
```

This command produces no output.

**Verify the updated permissions:**

```bash
ls -la secret.txt
```

Expected output:

```
-rw-------  1 student  staff  12 Jan  1 12:00 secret.txt
```

The permission string `-rw-------` means the owner can read and write, and no one else can access the file.

> **Note for Windows users:** Git Bash displays permission strings, but `chmod` has limited effect on Windows file systems. On Linux servers (which you will use in the bootcamp), file permissions are critical for security. The `chmod 600` pattern is commonly used to protect private keys and configuration files.

### Step 3: Explore Environment Variables (~2-3 min)

In this step you examine environment variables on your system. Environment variables store configuration that programs use at runtime.

**Display your home directory:**

```bash
echo $HOME
```

Expected output (your path will differ):

```
/Users/student
```

**Display the PATH variable:**

```bash
echo $PATH
```

Expected output (your paths will differ):

```
/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin
```

The PATH variable tells the system where to look for commands when you type them. Each directory is separated by a colon.

**List the first 10 environment variables:**

**macOS/Linux:**

```bash
env | head -10
```

**Windows (Git Bash):**

```bash
env | head
```

Expected output (your variables will differ):

```
HOME=/Users/student
USER=student
SHELL=/bin/zsh
PATH=/usr/local/bin:/usr/bin:/bin
LANG=en_US.UTF-8
```

Environment variables store configuration such as your username, home directory, and preferred language. In cloud computing, secrets (API keys, database passwords) should be stored in a secrets manager, not in environment variables or code. AWS Secrets Manager and AWS Systems Manager Parameter Store are designed for this purpose.

## Validation

Confirm the following before moving on:

- [ ] Running `whoami` displays your username
- [ ] Running `ls -la secret.txt` shows the file with permissions
- [ ] Running `echo $PATH` displays a list of directories

## Cleanup

Remove the file created during this lab:

```bash
rm secret.txt
```

If you created a working directory for this lab, remove it:

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

Research what the numbers in `chmod 600` mean. Hint: 6 = read (4) + write (2) for the owner, 0 = no permissions for the group, and 0 = no permissions for others. Try `chmod 644 secret.txt` and compare the permissions with `ls -la`. What changed, and who can now read the file?
