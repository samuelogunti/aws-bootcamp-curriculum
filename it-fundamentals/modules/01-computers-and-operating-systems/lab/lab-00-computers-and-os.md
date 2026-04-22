# Lab 00: Exploring Your Computer and Operating System

## Objective

Identify your operating system, explore the file system hierarchy, and check system resources using command-line tools.

## Prerequisites

- A terminal application:
  - **macOS:** Terminal.app (pre-installed in Applications > Utilities)
  - **Windows:** Git Bash (bundled with [Git for Windows](https://git-scm.com/download/win))
  - **Linux:** Terminal (pre-installed)
- No prior IT Fundamentals modules required

## Duration

5 to 8 minutes

## Instructions

### Step 1: Identify Your Operating System (~2-3 min)

In this step you determine which operating system your computer is running.

**macOS/Linux:**

Open your terminal and run:

```bash
uname -a
```

Expected output (macOS example):

```
Darwin MacBook-Pro.local 23.4.0 Darwin Kernel Version 23.4.0: ... x86_64
```

Expected output (Linux example):

```
Linux hostname 5.15.0-91-generic #101-Ubuntu SMP ... x86_64 GNU/Linux
```

The output shows your OS kernel name, hostname, kernel version, and architecture.

**Windows (Git Bash):**

Open Git Bash and run:

```bash
uname -a
```

Expected output:

```
MINGW64_NT-10.0-19045 DESKTOP-ABC1234 3.4.9 ... x86_64 Msys
```

The `MINGW64` prefix indicates you are running Git Bash on Windows.

**Windows (PowerShell):**

If you prefer PowerShell, run:

```powershell
systeminfo | findstr /B /C:"OS Name"
```

Expected output:

```
OS Name:    Microsoft Windows 10 Pro
```

> **Tip:** For the rest of this lab and the bootcamp, Git Bash is recommended on Windows for command consistency with macOS and Linux.

### Step 2: Explore the File System Hierarchy (~2-3 min)

In this step you look at the top-level directories on your system and find your home directory.

**macOS/Linux:**

List the root directory contents:

```bash
ls /
```

Expected output (macOS example):

```
Applications  Library  System  Users  Volumes  bin  cores  dev  etc  home  opt  private  sbin  tmp  usr  var
```

Expected output (Linux example):

```
bin  boot  dev  etc  home  lib  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

These are the top-level directories that organize your entire system. Key directories include:
- `/home` (Linux) or `/Users` (macOS): where user home directories live
- `/etc`: system configuration files
- `/tmp`: temporary files
- `/var`: variable data such as logs

Display your home directory path:

```bash
echo $HOME
```

Expected output (your username will differ):

```
/Users/student
```

**Windows (Git Bash):**

List the C drive contents:

```bash
ls /c/
```

Expected output:

```
PerfLogs  Program Files  Program Files (x86)  Users  Windows
```

Display your home directory path:

```bash
echo $HOME
```

Expected output (your username will differ):

```
/c/Users/student
```

### Step 3: Check System Resources (~2-3 min)

In this step you check how much RAM and storage your computer has.

**macOS:**

Check RAM:

```bash
sysctl -n hw.memsize | awk '{print $1/1024/1024/1024 " GB"}'
```

Expected output (your value will differ):

```
16 GB
```

Check available storage:

```bash
df -h /
```

Expected output (values will differ):

```
Filesystem     Size   Used  Avail Capacity  Mounted on
/dev/disk1s1  466Gi  120Gi  340Gi    27%    /
```

The `Size` column shows total storage and `Avail` shows free space.

**Linux:**

Check RAM:

```bash
free -h
```

Expected output (values will differ):

```
              total        used        free      shared  buff/cache   available
Mem:           15Gi       4.2Gi       8.1Gi       256Mi       3.1Gi        10Gi
Swap:         2.0Gi          0B       2.0Gi
```

The `total` column under `Mem` shows your total RAM.

Check available storage:

```bash
df -h /
```

Expected output (values will differ):

```
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1       100G   25G   75G  25% /
```

**Windows (Git Bash):**

Check RAM:

```bash
systeminfo | findstr "Total Physical Memory"
```

Expected output (your value will differ):

```
Total Physical Memory:     16,384 MB
```

Check available storage:

```bash
df -h /c
```

Expected output (values will differ):

```
Filesystem      Size  Used Avail Use% Mounted on
C:              466G  120G  346G  26% /c
```

## Validation

Confirm the following before moving on:

- [ ] Running `uname -a` (or equivalent) displays your operating system information
- [ ] Running `ls /` (or equivalent) displays the root directory contents
- [ ] You can identify how much RAM and storage your computer has

## Cleanup

No files were created during this lab. No cleanup is needed.

## Challenge (Optional)

Find out how many CPU cores your computer has:

- **Linux:** Run `lscpu` and look for the "CPU(s)" line
- **macOS:** Run `sysctl -n hw.ncpu`
- **Windows (Command Prompt):** Run `wmic cpu get NumberOfCores`

Compare your CPU core count to the vCPU counts in AWS EC2 instance types (you will learn about these in Module 04 of the bootcamp).
