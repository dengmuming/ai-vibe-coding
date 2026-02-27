---
description: Perform a comprehensive code review on a pull request or code segment.
---

# Code Review Skill

When asked to review code, follow these steps systematically:

1.  **Understand the Goal**: Read the PR description or issue to understand *what* the code is supposed to do.
2.  **Structural Review**:
    -   Does the logic make sense?
    -   Are there any obvious bugs or logic errors?
    -   Are edge cases handled (nulls, empty arrays, extreme values)?
3.  **Security Review**:
    -   Are there vulnerability risks (e.g., SQL injection, XSS)?
    -   Is sensitive data handled properly?
4.  **Performance Review**:
    -   Are there obvious bottlenecks (e.g., N+1 queries, unnecessary loops)?
    -   Can space/time complexity be improved?
5.  **Style & Best Practices**:
    -   Check if the code follows `.agents/rules/02-code-style.md`.
    -   Are variable names descriptive?
    -   Are functions too long? Do they adhere to the Single Responsibility Principle?
6.  **Actionable Feedback**:
    -   Provide specific, polite, and actionable feedback. Provide code suggestions when possible.
    -   Distinguish between "blocking" issues (bugs) and "nitpicks" (style preferences).
