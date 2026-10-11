---
description: Identify potential vulnerabilities and enforce security best practices to prevent common attacks.
---

# Security Auditing Skill

When asked to perform a security audit on code, check for common vulnerabilities (e.g., OWASP Top 10) using these steps:

1.  **Input Validation & Sanitization**:
    - Ensure all user inputs are strictly validated against a known-good structure.
    - Protect against Cross-Site Scripting (XSS) by properly encoding outputs.
    - Guard against SQL/NoSQL Injection by enforcing parameterized queries or ORMs.
2.  **Authentication & Authorization**:
    - Verify that endpoints enforce proper access control (checking both authentication and user roles).
    - Look for insecure Direct Object References (BOLA/IDOR).
3.  **Data Protection**:
    - Ensure sensitive data (passwords, PII, API keys) is never hardcoded or logged.
    - Verify that sensitive data is appropriately hashed (e.g., with bcrypt/Argon2) or encrypted.
4.  **Configuration & Dependency Security**:
    - Spot inherently insecure function calls (e.g., `eval()`, unsafe deserialization).
    - Check for overly permissive CORS configurations or missing security headers (e.g., CSRF protection).
5.  **Actionable Remediation**: For every vulnerability identified, provide a specific explanation of the risk and a code snippet demonstrating the secure implementation.
