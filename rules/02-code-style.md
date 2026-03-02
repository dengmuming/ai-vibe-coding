---
description: Standard code style guidelines across the repository.
---

# General Code Style
- **Consistency**: The overriding rule is to maintain consistency with the existing codebase. If a file uses 2-space indentation, do not introduce 4-space indentation.
- **Naming Conventions**:
  - Variables and Functions: use `camelCase` (JS/TS), `snake_case` (Python/Rust/Go).
  - Classes and Types: use `PascalCase`.
  - Constants: use `UPPER_SNAKE_CASE`.
- **Clarity over Cleverness**: Write code that is easy to read and understand. Avoid overly complex one-liners if they obfuscate the logic.
- **Error Handling**: Do not swallow errors silently. Handle errors gracefully and provide meaningful error messages or logs.
- **Formatting**: Rely on automated formatters (e.g., Prettier, Black, gofmt) wherever possible.
