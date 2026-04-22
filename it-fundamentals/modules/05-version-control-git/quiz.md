# IT Fundamentals Module 05: Quiz

1. What does the `git commit` command do?

   A) Uploads your code to a remote repository
   B) Saves a snapshot of your staged changes with a descriptive message
   C) Downloads the latest changes from a remote repository
   D) Creates a new branch for development

2. True or False: Git tracks every change made to files in a repository, allowing you to view the full history and revert to previous versions.

---

<details>
<summary>Answer Key</summary>

1. **B) Saves a snapshot of your staged changes with a descriptive message**
   The `git commit` command records a snapshot of all staged changes (files added with `git add`) along with a message describing what changed. It does not upload code to a remote repository (that is `git push`), download changes (that is `git pull`), or create a branch (that is `git checkout -b`).
   Further reading: [git-commit (git-scm.com)](https://git-scm.com/docs/git-commit)

2. **True.**
   Git is a version control system that records every change made to tracked files. Each commit is a snapshot of the project at a point in time. You can view the full history with `git log` and revert to any previous commit if needed.
   Further reading: [Getting Started - About Version Control (git-scm.com)](https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control)

</details>
