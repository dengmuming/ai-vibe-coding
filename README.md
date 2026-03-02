# Vibe-Coding: Skills, Workflows & Rules

[English](README.md) | [中文](README_zh.md)

Vibe-coding is an intuition-driven, flow-oriented coding method that combines creativity with structure, focusing on smooth development rhythm and code quality. Its core is balancing intuitive coding with systematic execution for agile and creative programming.

Core skills: intuitive problem-solving, rapid prototyping, adaptive logic, clean maintainable code, real-time debugging, and context-aware implementation. Workflows: enter focus, sketch core logic, code incrementally with testing, adjust by feedback, refine, and integrate smoothly.

Key rules: prioritize rhythm without sacrificing quality, keep structure clear, avoid over-engineering, ensure code reviewability/extensibility, maintain style consistency, and balance creativity with stability.

---

## 🤖 AI Agent Collaboration Framework

This repository includes a comprehensive "AI Vibe Coding" framework located in the `rules`, `skills`, and `workflows` directories. It provides AI agents with standard operating procedures (Rules), specific capabilities (Skills), and step-by-step processes (Workflows) to collaborate effectively and autonomously with software engineers.

### 📜 Rules (`rules/`)
Rules define the core guidelines and constraints the AI should always follow when operating in this repository.

*   **[01-general-behavior.md](rules/01-general-behavior.md)**: General behavioral principles including safety first, conciseness, proactivity, and step-by-step problem solving.
*   **[02-code-style.md](rules/02-code-style.md)**: Standard code style guidelines focusing on consistency, naming conventions, and utilizing automated formatters.
*   **[03-git-conventions.md](rules/03-git-conventions.md)**: Conventions for Git branching patterns and semantic commit messages.
*   **[04-documentation.md](rules/04-documentation.md)**: Directions for writing precise code comments, API docstrings, and architectural descriptions.

### 🛠️ Skills (`skills/`)
Skills provide specialized methodologies the AI can read to perform complex tasks systematically.

*   **[Code Review (`code_review/SKILL.md`)](skills/code_review/SKILL.md)**: A systematic approach to reviewing pull requests, checking logic, security, performance, and providing actionable feedback.
*   **[Debugging (`debugging/SKILL.md`)](skills/debugging/SKILL.md)**: A diagnostic process to trace issues, formulate hypotheses, gather runtime info, and propose minimal side-effect fixes.
*   **[Refactoring (`refactoring/SKILL.md`)](skills/refactoring/SKILL.md)**: Principles for restructuring code safely (DRY, Single Responsibility) without altering external behavior.
*   **[Test Generation (`test_generation/SKILL.md`)](skills/test_generation/SKILL.md)**: Guidelines for writing comprehensive unit tests targeting happy paths, edge cases, and properly handling mocks.

### 🔄 Workflows (`workflows/`)
Workflows define step-by-step Standard Operating Procedures (SOPs) for common development actions. Users can invoke them to guide the AI, often via slash commands.

*   **[git-feature.md](workflows/git-feature.md)** (`/git-feature`): Steps to review requirements, draft an implementation plan, create a branch, and set up code boilerplate for a new feature.
*   **[git-commit.md](workflows/git-commit.md)** (`/git-commit`): Standardized workflow for staging, committing, and pushing code changes using semantic commit messages.
*   **[git-release.md](workflows/git-release.md)** (`/git-release`): Executing a release process by determining SemVer bumps, creating tags, and pushing to the remote.
*   **[review.md](workflows/review.md)** (`/review`): Review code changes for bugs, security issues, and improvements.

---

## 💻 Supported AI Coding Assistants

Different AI coding assistants (Agent/Copilot) generally rely on specific directories to load their project-level rules, workflows, and prompts. Here is the mapping for mainstream AI assistants:

*   **Google Antigravity**: Uses the `.agents` (or `.agent`) directory. It heavily utilizes this directory for structured `rules`, `skills`, and `workflows`.
    *   *Documentation*: [https://antigravity.google/docs/get-started](https://antigravity.google/docs/get-started)
*   **Google Gemini CLI**: Uses the `.agents` directory for custom rules, skills, and workflows, while using the `.gemini` directory for maintaining conversational state.
    *   *Documentation*: [https://geminicli.com/docs/](https://geminicli.com/docs/)
*   **GitHub Copilot**: Uses the `.github` directory (e.g., `.github/copilot-instructions.md`) to read custom repository instructions.
    *   *Documentation*: [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
*   **Windsurf (by Codeium)**: Uses the `.windsurf` directory to manage local rules and workspace context.
    *   *Documentation*: [Windsurf Documentation](https://docs.codeium.com/windsurf)
*   **Claude (Anthropic)**: Uses the `.claude` directory (or standard config files like `clinerules`) for repository-specific instructions.
    *   *Documentation*: [Claude Documentation](https://docs.anthropic.com/claude/docs)
