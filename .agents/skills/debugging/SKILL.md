---
description: Systematically debug an error or unexpected behavior.
---

# Debugging Skill

When faced with a bug or error trace, follow this diagnostic process:

1.  **Reproduce mentally**: Understand the exact sequence of events that led to the error. What are the inputs? What is the expected output vs actual output?
2.  **Analyze the Stack Trace**: Identify the exact file and line number where the exception occurred. Find the user-land code (not library code) that triggered the issue.
3.  **Formulate a Hypothesis**: What could cause this error at this location? (e.g., variable is null, network request failed, index out of bounds).
4.  **Gather Information**: If you need more information, insert logging statements or use diagnostic tools (read terminal output, check specific variables). If you are uncertain about the state, do not guess.
5.  **Propose a Fix**: Once the root cause is identified, write the minimal code change required to fix the bug without introducing side effects.
6.  **Verify**: Explain how the user can verify that the bug is fixed (e.g., adding a test case, running a specific command).
