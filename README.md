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
Skills provide specialized methodologies the AI can read to perform complex tasks systematically. Over 40+ skills are organized into modular, battle-tested categories:

#### 1. Core Engineering & Delivery Loop (`dev` workflow)
*   **[dev (`dev/SKILL.md`)](skills/dev/SKILL.md)**: End-to-end delivery pipeline (`grill-with-docs` -> `to-spec` -> `to-tickets` -> `implement` -> `grill-code-review`).
*   **[to-spec (`to-spec/SKILL.md`)](skills/to-spec/SKILL.md)**: Convert conversational design into structured formal engineering specifications.
*   **[to-tickets (`to-tickets/SKILL.md`)](skills/to-tickets/SKILL.md)**: Decompose a spec into independent, testable tracer-bullet tickets.
*   **[implement (`implement/SKILL.md`)](skills/implement/SKILL.md)** / **[implement-spec](skills/implement-spec/SKILL.md)**: Execute implementation strictly adhering to specifications and tests.
*   **[tdd (`tdd/SKILL.md`)](skills/tdd/SKILL.md)**: Test-Driven Development workflow enforcing write-test-first discipline.
*   **[wayfinder (`wayfinder/SKILL.md`)](skills/wayfinder/SKILL.md)**: Long-range architectural mapping for multi-session epics.
*   **[epic (`epic/SKILL.md`)](skills/epic/SKILL.md)** & **[dispatch (`dispatch/SKILL.md`)](skills/dispatch/SKILL.md)**: Batch execution and cross-worktree task orchestration.

#### 2. Planning, Stress-Testing & Visualization (Matt Pocock & Community)
*   **[grill-me (`grill-me/SKILL.md`)](skills/grill-me/SKILL.md)** & **[grilling](skills/grilling/SKILL.md)**: Relentless Socratic interview to sharpen plans and resolve decision trees.
*   **[grill-with-docs (`grill-with-docs/SKILL.md`)](skills/grill-with-docs/SKILL.md)**: Stress-test plans against documentation while auto-generating ADRs.
*   **[show-me (`show-me/SKILL.md`)](skills/show-me/SKILL.md)**: High-signal output formatting (Mermaid diagrams, file trees, annotated diffs).
*   **[domain-modeling (`domain-modeling/SKILL.md`)](skills/domain-modeling/SKILL.md)**: Build ubiquitous domain models and glossary definitions.
*   **[prototype (`prototype/SKILL.md`)](skills/prototype/SKILL.md)**: Rapid throwaway prototyping to answer technical feasibility questions.

#### 3. Code Quality, Review & Security
*   **[code-review (`code-review/SKILL.md`)](skills/code-review/SKILL.md)** & **[grill-code-review](skills/grill-code-review/SKILL.md)**: Dual-axis review checking correctness, security, performance, and style.
*   **[security-audit (`security-audit/SKILL.md`)](skills/security-audit/SKILL.md)** & **[security-review](skills/security-review/SKILL.md)**: Full-surface vulnerability review (Cloudflare audit methodology + language checklists).
*   **[debugging (`debugging/SKILL.md`)](skills/debugging/SKILL.md)** & **[diagnosing-bugs](skills/diagnosing-bugs/SKILL.md)**: Hypothesis-driven diagnosis loops for complex regressions.
*   **[refactoring (`refactoring/SKILL.md`)](skills/refactoring/SKILL.md)**: Behavior-preserving code restructuring and DRY simplification.
*   **[test-generation (`test-generation/SKILL.md`)](skills/test-generation/SKILL.md)**: Comprehensive test suite generation for edge cases and happy paths.
*   **[neat-freak (`neat-freak/SKILL.md`)](skills/neat-freak/SKILL.md)**: Strict repository tidiness and configuration inventory auditing.

#### 4. Architecture & Specialized Frameworks
*   **[frontend-design (`frontend-design/SKILL.md`)](skills/frontend-design/SKILL.md)**: Production-grade frontend UX/UI design guidelines.
*   **[go-init (`go-init/SKILL.md`)](skills/go-init/SKILL.md)**: Battle-tested Go microservice scaffold (Gin + GORM + Redis + Zap + CLI).
*   **[codebase-design (`codebase-design/SKILL.md`)](skills/codebase-design/SKILL.md)** & **[improve-codebase-architecture](skills/improve-codebase-architecture/SKILL.md)**: Designing deep modules and visual architecture reports.
*   **[skill-creator (`skill-creator/SKILL.md`)](skills/skill-creator/SKILL.md)**: Developing, evaluating, and packaging new skills.

> See [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) for full provenance, upstream repository URLs, and MIT licenses.

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
