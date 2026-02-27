---
description: Generate unit, integration, or end-to-end tests for a given piece of code.
---

# Test Generation Skill

When generating tests, aim for robust and meaningful coverage:

1.  **Identify the Framework**: Determine which testing framework is used in the repository (e.g., Jest, PyTest, Go test).
2.  **Understand the Target**: Analyze the function/class to be tested. Identify its inputs, expected outputs, side effects, and dependencies.
3.  **Cover the Happy Path**: Always write a test for the most common, expected use case.
4.  **Cover Edge Cases**: Write tests for null inputs, empty lists, negative numbers, boundary conditions, and typical user errors.
5.  **Mocking/Stubbing**: If the code interacts with a database, filesystem, or external API, set up appropriate mocks or stubs to isolate the unit test. Do not make real network requests in unit tests.
6.  **Assertions**: Make precise assertions. Don't just assert that an output array has length 3; assert the specific contents of the array if possible.
7.  **Clean Up**: Ensure tests clean up after themselves (e.g., deleting temporary files, rolling back mock databases).
