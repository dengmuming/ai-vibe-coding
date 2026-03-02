---
description: Standardized workflow for committing code updates.
---

# Git Commit Workflow

Follow these steps to ensure your code changes are staged, documented, and synchronized correctly across any development environment.

1. **Inspect Changes**
Before staging, verify your current branch and identify which files have been modified or untracked.

```Bash
git status
```

2. **Stage Modifications**
Add your changes to the index. You can add specific files or include all changes in the current directory.

```Bash
git add .
```

3. **Commit with Intent**
Record your changes with a descriptive message. To maintain a clean project history, we recommend following the Conventional Commits standard:

```Bash
git commit -m "type: [brief description of changes]"
Common Commit Types:

feat: A new feature for the user.

fix: A bug fix.

docs: Changes to documentation only.

style: Formatting, missing semi-colons, etc.; no production code change.

refactor: Refactoring production code, e.g., renaming a variable.

chore: Updating grunt tasks etc.; no production code change.
```

4. **Synchronize with Remote**
Upload your local commits to the tracking branch on the remote repository.

```Bash
git push 或者 git push -u origin <current-branch-name>
```