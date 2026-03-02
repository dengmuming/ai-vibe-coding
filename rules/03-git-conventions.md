---
description: Conventions for Git commits and branching.
---

# Git Conventions

## Branch Naming
- **Features**: `feat/<short-description>` or `feature/<issue-number>-<short-description>`
- **Bug Fixes**: `fix/<short-description>`
- **Documentation**: `docs/<short-description>`
- **Refactoring**: `refactor/<short-description>`

## Commit Messages
Follow the Conventional Commits specification:
```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### Types
- `feat`: A new feature
- `fix`: A bug fix
- `docs`: Documentation only changes
- `style`: Changes that do not affect the meaning of the code (white-space, formatting, missing semi-colons, etc)
- `refactor`: A code change that neither fixes a bug nor adds a feature
- `perf`: A code change that improves performance
- `test`: Adding missing tests or correcting existing tests
- `chore`: Changes to the build process or auxiliary tools and libraries such as documentation generation

### Rules
- Use the imperative, present tense: "change" not "changed" nor "changes".
- Don't capitalize the first letter.
- No dot (.) at the end.
