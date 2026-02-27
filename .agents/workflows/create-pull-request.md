---
description: Steps for finalizing work and creating a Pull Request.
---

# Create Pull Request Workflow

When a feature or fix is ready to be merged, follow these steps systematically:

1. **Self Code Review:**
   - Run a quick scan of your changed files.
   - Check against `.agents/rules/02-code-style.md`.
   - Remove any debugging logs or commented-out code.
2. **Run Tests and Formatters:**
   - Execute the project's formatting tools (e.g., `npm run format`, `go fmt ./...`).
   - Run the test suite (e.g., `npm test`, `pytest`).
   - Ensure all tests pass.
3. **Commit Changes:**
   - If not already committed, stage the changes: `git add .`
   - Write a semantic commit message per `.agents/rules/03-git-conventions.md`. `git commit -m "feat/fix: semantic message here"`
4. **Push Branch:**
   - `git push -u origin <current-branch-name>`
5. **Draft the PR Description:**
   - Summarize the changes concisely.
   - Note any breaking changes or external dependencies.
   - Detail how the changes were tested.
