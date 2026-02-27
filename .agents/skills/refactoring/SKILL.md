---
description: Refactor code to improve readability, maintainability, or performance without changing its behavior.
---

# Refactoring Skill

When asked to refactor code, follow these principles:

1.  **Safety First**: Ensure there are tests covering the existing code if possible. If not, note the risk.
2.  **Single Responsibility**: Break down large functions, classes, or modules so that each unit does one thing well.
3.  **DRY (Don't Repeat Yourself)**: Extract duplicated logic into reusable functions or components.
4.  **Clarity**: Rename confusing variables, extract complex conditionals into well-named helper variables/functions.
5.  **Modernize**: If dealing with legacy code, apply modern language features (e.g., async/await instead of callbacks, list comprehensions instead of loops) if the environment supports it.
6.  **Review Changes**: Before presenting the final refactoring, double-check that the overall logic and return values remain identical to the original implementation.
