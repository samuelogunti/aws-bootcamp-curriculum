# Lab 00: Command Line Navigation and File Operations

## Objective

Navigate the file system, create a workspace directory, and practice creating, copying, renaming, and deleting files.

## Prerequisites

- A terminal application:
  - **macOS:** Terminal.app (pre-installed in Applications > Utilities)
  - **Windows:** Git Bash (bundled with [Git for Windows](https://git-scm.com/download/win))
  - **Linux:** Terminal (pre-installed)
- No prior modules required

## Duration

10 to 12 minutes

## Instructions

### Step 1: Set Up Your Workspace (~5 min)

In this step you open a terminal and create a working directory for the lab.

**Open your terminal:**

- **macOS:** Open Finder, go to Applications > Utilities, and open **Terminal**. You can also press `Cmd + Space`, type `Terminal`, and press Enter.
- **Windows:** Open the Start menu, type `Git Bash`, and open it. If you do not see Git Bash, install Git from [git-scm.com](https://git-scm.com/download/win) first.
- **Linux:** Open your distribution's terminal application. On Ubuntu, press `Ctrl + Alt + T`.

Once your terminal is open, run the following commands one at a time.

**Print your current directory:**

```bash
pwd
```

Expected output (your path will differ):

```
/Users/student
```

**List the files in your current directory:**

```bash
ls
```

Expected output (your files will differ):

```
Desktop    Documents    Downloads    Music    Pictures
```

**Create a new directory for this lab:**

```bash
mkdir it-fundamentals-lab
```

This command produces no output. That is normal.

**Change into the new directory:**

```bash
cd it-fundamentals-lab
```

This command produces no output. That is normal.

**Confirm you are in the new directory:**

```bash
pwd
```

Expected output (your path will differ):

```
/Users/student/it-fundamentals-lab
```

You now have a dedicated workspace for the rest of this lab.

### Step 2: Create and Manage Files (~5-7 min)

In this step you create, view, copy, rename, and delete files using command-line commands.

**Create a file with text content:**

```bash
echo "Hello from the command line" > hello.txt
```

This command creates a file called `hello.txt` and writes the text into it. It produces no output.

**View the contents of the file:**

```bash
cat hello.txt
```

Expected output:

```
Hello from the command line
```

**Create an empty file:**

```bash
touch notes.txt
```

This command produces no output. It creates an empty file called `notes.txt`.

**List the files in the directory:**

```bash
ls
```

Expected output:

```
hello.txt    notes.txt
```

**Add text to the notes file:**

```bash
echo "This is my first note" > notes.txt
```

This command produces no output.

**View the contents of the notes file:**

```bash
cat notes.txt
```

Expected output:

```
This is my first note
```

**Copy a file:**

```bash
cp hello.txt hello-backup.txt
```

This command produces no output. It creates a copy of `hello.txt` named `hello-backup.txt`.

**List the files to confirm the copy:**

```bash
ls
```

Expected output:

```
hello-backup.txt    hello.txt    notes.txt
```

**Rename a file:**

```bash
mv notes.txt my-notes.txt
```

This command produces no output. It renames `notes.txt` to `my-notes.txt`.

**List the files to confirm the rename:**

```bash
ls
```

Expected output:

```
hello-backup.txt    hello.txt    my-notes.txt
```

**Delete a file:**

```bash
rm hello-backup.txt
```

This command produces no output. It permanently deletes `hello-backup.txt`.

> **Warning:** The `rm` command deletes files permanently. There is no recycle bin or undo. Always double-check the filename before pressing Enter.

**List the files to confirm the deletion:**

```bash
ls
```

Expected output:

```
hello.txt    my-notes.txt
```

You now know how to create, view, copy, rename, and delete files from the command line.

## Validation

Confirm the following before moving on:

- [ ] You can open a terminal and run `pwd` to see your current directory
- [ ] The `it-fundamentals-lab/` directory exists with `hello.txt` and `my-notes.txt`
- [ ] Running `cat hello.txt` displays "Hello from the command line"
- [ ] Running `cat my-notes.txt` displays "This is my first note"

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

No cloud resources were created during this lab. No further cleanup is needed.

## Challenge (Optional)

1. Create a directory called `practice/` inside a new `it-fundamentals-lab/` directory, then create three files inside it using a single `touch` command:

   ```bash
   mkdir -p it-fundamentals-lab/practice
   touch it-fundamentals-lab/practice/file1.txt it-fundamentals-lab/practice/file2.txt it-fundamentals-lab/practice/file3.txt
   ```

2. Use `ls -la` to view the files with details (permissions, size, modification date). Compare the output to a plain `ls`.

3. Use `grep` to search for a word inside a file:

   ```bash
   echo "The cloud is powerful" > it-fundamentals-lab/practice/file1.txt
   grep "cloud" it-fundamentals-lab/practice/file1.txt
   ```
