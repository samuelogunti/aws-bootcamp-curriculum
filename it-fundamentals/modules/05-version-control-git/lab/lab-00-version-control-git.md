# Lab 00: Version Control with Git

## Objective

Configure Git, initialize a repository, stage and commit files, and view commit history.

## Prerequisites

- A terminal application:
  - **macOS:** Terminal.app (pre-installed in Applications > Utilities)
  - **Windows:** Git Bash (bundled with [Git for Windows](https://git-scm.com/download/win))
  - **Linux:** Terminal (pre-installed)
- Module 02 (The Command Line) for basic terminal skills
- Git installed:
  - **macOS:** `xcode-select --install` or download from [git-scm.com](https://git-scm.com/download/mac)
  - **Windows:** Download from [git-scm.com](https://git-scm.com/download/win)
  - **Linux:** `sudo apt install git` or `sudo dnf install git`

## Duration

10 to 12 minutes

## Instructions

### Step 1: Configure Git and Initialize a Repository (~5 min)

In this step you set up Git and create a new repository.

**Create a working directory:**

```bash
mkdir -p it-fundamentals-lab
cd it-fundamentals-lab
```

**Configure Git (one-time setup):**

If you have never used Git on this computer, run these two commands to set your name and email. Git attaches this information to every commit you make.

```bash
git config --global user.name "Your Name"
```

```bash
git config --global user.email "you@example.com"
```

Replace `Your Name` and `you@example.com` with your actual name and email address. These commands produce no output.

**Initialize a Git repository:**

```bash
git init
```

Expected output:

```
Initialized empty Git repository in /Users/student/it-fundamentals-lab/.git/
```

This creates a hidden `.git` folder that tracks all changes in the directory.

**Create two files to work with:**

```bash
echo "Hello from the command line" > hello.txt
echo "This is my first note" > my-notes.txt
```

These commands produce no output.

**Check the status of your repository:**

```bash
git status
```

Expected output:

```
On branch main
No commits yet
Untracked files:
  (use "git add <file>..." to include in what will be committed)
	hello.txt
	my-notes.txt

nothing added to commit but untracked files present (use "git add" to track)
```

Git sees the two files but is not tracking them yet.

> **Tip:** If your output shows `On branch master` instead of `On branch main`, that is fine. Older versions of Git use `master` as the default branch name.

### Step 2: Stage, Commit, and View History (~5-7 min)

In this step you stage files, create commits, and view the commit history.

**Stage all files for commit:**

```bash
git add .
```

This command produces no output. The `.` means "add everything in the current directory."

**Check the status again:**

```bash
git status
```

Expected output:

```
On branch main
No commits yet
Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   hello.txt
	new file:   my-notes.txt
```

Both files are now staged and ready to be committed.

**Create your first commit:**

```bash
git commit -m "Add initial files"
```

Expected output:

```
[main (root-commit) a1b2c3d] Add initial files
 2 files changed, 2 insertions(+)
 create mode 100644 hello.txt
 create mode 100644 my-notes.txt
```

Your commit hash (the `a1b2c3d` part) will be different. That is normal.

**Edit a file and commit the change:**

Append a new line to `hello.txt`:

```bash
echo "Updated content" >> hello.txt
```

This command produces no output. The `>>` operator appends text to the file instead of overwriting it.

**Stage and commit the change:**

```bash
git add hello.txt
```

```bash
git commit -m "Update hello.txt"
```

Expected output:

```
[main b2c3d4e] Update hello.txt
 1 file changed, 1 insertion(+)
```

**View the commit history:**

```bash
git log --oneline
```

Expected output:

```
b2c3d4e Update hello.txt
a1b2c3d Add initial files
```

Your commit hashes will be different, but you should see two commits listed with the most recent one on top.

## Validation

Confirm the following before moving on:

- [ ] Running `git log --oneline` inside the directory shows at least two commits
- [ ] Running `git status` shows a clean working tree (no uncommitted changes)
- [ ] The `hello.txt` file contains two lines of text

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

1. Create a new Git branch called `feature/add-readme`, add a README.md file, commit it, and switch back to `main`:

   ```bash
   git checkout -b feature/add-readme
   echo "# My Lab Project" > README.md
   git add README.md
   git commit -m "Add README"
   git checkout main
   ```

2. Run `git log --all --oneline --graph` to see a visual representation of your branches and commits.

3. Try `git diff` after editing a file but before staging it to see what changed.

---

*AWS Bootcamp: From Novice to Architect*
*Author: Samuel Ogunti*
*License: [CC BY-NC 4.0](https://creativecommons.org/licenses/by-nc/4.0/)*
