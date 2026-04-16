# Git & Branching Best Practices

## Branching Strategy
- Use a trunk-based or GitHub Flow model for most projects
- Keep `main` always deployable
- Create short-lived feature branches from `main`
- Delete branches after merging

## Branch Naming
- Use a consistent prefix convention:
  - `feature/` — new functionality
  - `fix/` — bug fixes
  - `chore/` — maintenance, refactoring, dependency updates
  - `docs/` — documentation changes
  - `hotfix/` — urgent production fixes
- Include a ticket/issue ID when applicable: `feature/PROJ-123-add-user-auth`
- Use lowercase and hyphens, no spaces

## Commit Messages
- Follow Conventional Commits format:
  ```
  type(scope): short description

  Optional longer body explaining why, not what.

  Refs: #123
  ```
- Types: `feat`, `fix`, `docs`, `style`, `refactor`, `test`, `chore`, `ci`
- Keep the subject line under 72 characters
- Use imperative mood: "add feature" not "added feature"
- Reference issue/ticket numbers

## Merging
- Prefer squash merges for feature branches to keep history clean
- Use merge commits for long-lived branches or releases
- Rebase feature branches on `main` before merging to avoid unnecessary merge commits
- Resolve conflicts locally, not in the merge tool

## Tags & Releases
- Tag releases with semantic versioning: `v1.2.3`
- Use annotated tags with release notes
- Automate changelog generation from commit messages

## Protection
- Protect `main` with required PR reviews (minimum 1 reviewer)
- Require passing CI checks before merge
- Disable force pushes to protected branches
- Use CODEOWNERS for critical paths
