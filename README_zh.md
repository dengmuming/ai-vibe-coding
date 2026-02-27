# Vibe-Coding: 技能、工作流与规则

[English](README.md) | [中文](README_zh.md)

Vibe-coding 是一种直觉驱动、注重心流的编码方法，它将创造力与结构相结合，专注于流畅的开发节奏和代码质量。其核心是在直觉编码与系统化执行之间取得平衡，以实现敏捷和富有创造力的编程。

核心技能：直觉性问题解决、快速原型设计、自适应逻辑、整洁易维护的代码、实时调试以及具备上下文感知的实现。
工作流：进入专注状态、构思核心逻辑、渐进式编码与测试、根据反馈调整、优化完善，并平滑集成。

关键规则：在不牺牲质量的前提下优先考虑节奏，保持结构清晰，避免过度工程化，确保代码的易审查性和可扩展性，保持风格一致性，并在创造力与稳定性之间取得平衡。

---

## 🤖 AI 智能体协作框架

本仓库在 `.agents` 目录下包含了一个全面的 "AI Vibe Coding" 框架。它为 AI 智能体提供了标准操作程序（规则）、特定能力（技能）和分步流程（工作流），以便与软件工程师进行高效、自主的协作。

### 📜 规则 (`.agents/rules/`)
规则定义了 AI 在本仓库中操作时应始终遵循的核心准则和约束。

*   **[01-general-behavior.md](.agents/rules/01-general-behavior.md)**：一般行为原则，包括安全第一、简洁明了、主动积极和逐步解决问题。
*   **[02-code-style.md](.agents/rules/02-code-style.md)**：标准代码风格指南，侧重于一致性、命名约定以及利用自动化格式化工具。
*   **[03-git-conventions.md](.agents/rules/03-git-conventions.md)**：Git 分支模式和语义化提交信息的约定。
*   **[04-documentation.md](.agents/rules/04-documentation.md)**：关于编写精确的代码注释、API 文档字符串和架构描述的指导。

### 🛠️ 技能 (`.agents/skills/`)
技能提供了专业的方法论，AI 可以通过阅读这些方法论系统地执行复杂任务。

*   **[代码审查 (`code_review/SKILL.md`)](.agents/skills/code_review/SKILL.md)**：一种审查拉取请求 (PR) 的系统方法，检查逻辑、安全性、性能，并提供可操作的反馈。
*   **[调试 (`debugging/SKILL.md`)](.agents/skills/debugging/SKILL.md)**：诊断过程，用于追踪问题、提出假设、收集运行时信息，并提出副作用最小的修复方案。
*   **[重构 (`refactoring/SKILL.md`)](.agents/skills/refactoring/SKILL.md)**：在不改变外部行为的情况下安全重构代码（DRY 原则、单一职责原则）的原则。
*   **[测试生成 (`test_generation/SKILL.md`)](.agents/skills/test_generation/SKILL.md)**：编写全面单元测试的指南，涵盖正常路径 (happy path)、边缘情况，并正确处理模拟 (mocks)。

### 🔄 工作流 (`.agents/workflows/`)
工作流定义了常见开发操作的分步标准操作程序 (SOP)。用户可以通过斜杠命令等方式调用它们来引导 AI。

*   **[init-feature.md](.agents/workflows/init-feature.md)** (`/init-feature`)：审查需求、起草实现计划、创建分支以及设置代码样板的步骤。
*   **[create-pull-request.md](.agents/workflows/create-pull-request.md)** (`/create-pull-request`)：通过运行测试、格式化、创建语义化提交、推送更改以及起草 PR 描述来完成工作的步骤。
*   **[release.md](.agents/workflows/release.md)** (`/release`)：执行发布流程，通过确定语义化版本升级、更新变更日志 (changelog) 和给 Git 仓库打标签。

---

## 💻 支持的 AI 编程助手

不同的 AI 编程助手（Agent/Copilot）通常依赖特定的目录来读取其项目级的规则、工作流和提示词。以下是主流 AI 助手的映射关系：

*   **Google Antigravity**: 使用 `.agents`（或 `.agent`）目录。它大量利用此目录来构建结构化的 `rules`（规则）、`skills`（技能）和 `workflows`（工作流）。
    *   *文档*: [https://antigravity.google/docs/get-started](https://antigravity.google/docs/get-started)
*   **Google Gemini CLI**: 使用 `.agents` 目录存放自定义规则、技能和工作流，同时使用 `.gemini` 目录维护会话状态。
    *   *文档*: [https://geminicli.com/docs/](https://geminicli.com/docs/)
*   **GitHub Copilot**: 使用 `.github` 目录（例如 `.github/copilot-instructions.md`）读取自定义仓库指令。
    *   *文档*: [GitHub Copilot 文档](https://docs.github.com/en/copilot)
*   **Windsurf (由 Codeium 提供)**: 使用 `.windsurf` 目录管理本地规则和工作区上下文。
    *   *文档*: [Windsurf 文档](https://docs.codeium.com/windsurf)
*   **Claude (Anthropic)**: 使用 `.claude` 目录（或标准的配置文件，如 `clinerules`）存放仓库专属指令。
    *   *文档*: [Claude 文档](https://docs.anthropic.com/claude/docs)
