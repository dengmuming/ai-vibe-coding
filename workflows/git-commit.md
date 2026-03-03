---
description: Standardized workflow for committing code updates.
---

# Git Commit Workflow

Follow these steps to ensure your code changes are staged, documented, and synchronized correctly across any development environment.

1. **Inspect Changes**
Before staging, verify your current branch and identify which files have been modified or untracked. Pay attention to the working directory.
// turbo
```
git status
```

2. **Stage Modifications**
Add all changes to the staging area.
// turbo
```
git add .
```

3. **Commit with Intent**
Record your changes with a descriptive commit message. Follow the [Conventional Commits](https://www.conventionalcommits.org/) standard to maintain a clean project history.
// turbo
```
git commit -m "type: [brief description of changes]"
```

> **Commit Type Reference:**
> - `feat:` A new feature
> - `fix:` A bug fix
> - `docs:` Documentation changes only
> - `style:` Formatting, missing semi-colons, etc.; no production code change
> - `refactor:` Refactoring production code, e.g., renaming a variable
> - `chore:` Maintenance tasks; no production code change

4. **Synchronize with Remote**
Push your local commits to the remote repository.
// turbo
```
git push
```