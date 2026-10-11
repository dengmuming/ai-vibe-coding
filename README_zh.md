# Vibe-Coding: 技能、工作流与规则

[English](README.md) | [中文](README_zh.md)

Vibe-coding 是一种直觉驱动、注重心流的编码方法，它将创造力与结构相结合，专注于流畅的开发节奏和代码质量。其核心是在直觉编码与系统化执行之间取得平衡，以实现敏捷和富有创造力的编程。

核心技能：直觉性问题解决、快速原型设计、自适应逻辑、整洁易维护的代码、实时调试以及具备上下文感知的实现。
工作流：进入专注状态、构思核心逻辑、渐进式编码与测试、根据反馈调整、优化完善，并平滑集成。

关键规则：在不牺牲质量的前提下优先考虑节奏，保持结构清晰，避免过度工程化，确保代码的易审查性和可扩展性，保持风格一致性，并在创造力与稳定性之间取得平衡。

---

## 🤖 AI 智能体协作框架

本仓库在 `rules`、`skills` 和 `workflows` 目录下包含了一个全面的 "AI Vibe Coding" 框架。它为 AI 智能体提供了标准操作程序（规则）、特定能力（技能）和分步流程（工作流），以便与软件工程师进行高效、自主的协作。

### 📜 规则 (`rules/`)
规则定义了 AI 在本仓库中操作时应始终遵循的核心准则和约束。

*   **[01-general-behavior.md](rules/01-general-behavior.md)**：一般行为原则，包括安全第一、简洁明了、主动积极和逐步解决问题。
*   **[02-code-style.md](rules/02-code-style.md)**：标准代码风格指南，侧重于一致性、命名约定以及利用自动化格式化工具。
*   **[03-git-conventions.md](rules/03-git-conventions.md)**：Git 分支模式和语义化提交信息的约定。
*   **[04-documentation.md](rules/04-documentation.md)**：关于编写精确的代码注释、API 文档字符串和架构描述的指导。

### 🛠️ 技能 (`skills/`)
技能为 AI 智能体提供了专业化的方法论和 Standard Operating Procedures（SOP），覆盖 40+ 实战技能并分类呈现：

#### 1. 核心工程与交付闭环 (`dev` 研发全流程)
*   **[dev (`dev/SKILL.md`)](skills/dev/SKILL.md)**：端到端研发全流程串联（`grill-with-docs` -> `to-spec` -> `to-tickets` -> `implement` -> `grill-code-review`）。
*   **[to-spec (`to-spec/SKILL.md`)](skills/to-spec/SKILL.md)**：将对话中的设计推演转化为正式的技术规格书（Specification）。
*   **[to-tickets (`to-tickets/SKILL.md`)](skills/to-tickets/SKILL.md)**：将规格书拆解为可独立验证和测试的曳光弹工单（Tickets）。
*   **[implement (`implement/SKILL.md`)](skills/implement/SKILL.md)** / **[implement-spec](skills/implement-spec/SKILL.md)**：严格依循 Spec 与测试驱动进行增量落地。
*   **[tdd (`tdd/SKILL.md`)](skills/tdd/SKILL.md)**：测试驱动开发，强制“先写红测试、再写绿代码、最后重构”。
*   **[wayfinder (`wayfinder/SKILL.md`)](skills/wayfinder/SKILL.md)**：长程任务路线图规划，跨会话协同与进展推演。
*   **[epic (`epic/SKILL.md`)](skills/epic/SKILL.md)** 与 **[dispatch (`dispatch/SKILL.md`)](skills/dispatch/SKILL.md)**：多工单批次调度与隔离 worktree 推进。

#### 2. 方案质询、对齐与高信息密度可视化 (Matt Pocock & 社区)
*   **[grill-me (`grill-me/SKILL.md`)](skills/grill-me/SKILL.md)** 与 **[grilling](skills/grilling/SKILL.md)**：苏格拉底式高强度决策树质询，逐个节点压实方案。
*   **[grill-with-docs (`grill-with-docs/SKILL.md`)](skills/grill-with-docs/SKILL.md)**：结合既有文档深度质询并自动生成架构决策记录（ADR）。
*   **[show-me (`show-me/SKILL.md`)](skills/show-me/SKILL.md)**：拒绝废话的高信噪比可视化表达（Mermaid 图、目录树、带注释的 Diff）。
*   **[domain-modeling (`domain-modeling/SKILL.md`)](skills/domain-modeling/SKILL.md)**：领域驱动设计（DDD）领域建模与通用词汇表（Glossary）建立。
*   **[prototype (`prototype/SKILL.md`)](skills/prototype/SKILL.md)**：快速抛弃型原型验证，回答关键可行性疑问。

#### 3. 代码质量、代码审查与安全审计
*   **[code-review (`code-review/SKILL.md`)](skills/code-review/SKILL.md)** 与 **[grill-code-review](skills/grill-code-review/SKILL.md)**：双轴代码审查（正确性、安全性、性能与工程品味）。
*   **[security-audit (`security-audit/SKILL.md`)](skills/security-audit/SKILL.md)** 与 **[security-review](skills/security-review/SKILL.md)**：全方位安全审查（Cloudflare 安全审计框架 + 语言级防漏洞清单）。
*   **[debugging (`debugging/SKILL.md`)](skills/debugging/SKILL.md)** 与 **[diagnosing-bugs](skills/diagnosing-bugs/SKILL.md)**：假设驱动的科学排错与长周期难 Bug 诊断。
*   **[refactoring (`refactoring/SKILL.md`)](skills/refactoring/SKILL.md)**：安全重构、单一职责与消除重复代码。
*   **[test-generation (`test-generation/SKILL.md`)](skills/test-generation/SKILL.md)**：针对边界情况与核心主路径的自动化测试套件构建。
*   **[neat-freak (`neat-freak/SKILL.md`)](skills/neat-freak/SKILL.md)**：代码库洁癖规范与目录/配置清单自检。

#### 4. 架构设计与专项脚手架
*   **[frontend-design (`frontend-design/SKILL.md`)](skills/frontend-design/SKILL.md)**：生产级前端高水准交互与 UI 原型设计指南。
*   **[go-init (`go-init/SKILL.md`)](skills/go-init/SKILL.md)**：实战级 Go 微服务后端脚手架规范（Gin + GORM + Redis + Zap + CLI）。
*   **[codebase-design (`codebase-design/SKILL.md`)](skills/codebase-design/SKILL.md)** 与 **[improve-codebase-architecture](skills/improve-codebase-architecture/SKILL.md)**：深模块（Deep Modules）设计与可视化架构评估报告。
*   **[skill-creator (`skill-creator/SKILL.md`)](skills/skill-creator/SKILL.md)**：构建、评测和打包新 Skill 的标准化流水线。

> 完整第三方开源来源、上游仓库地址与许可证信息请参阅 [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md)。

### 🔄 工作流 (`workflows/`)
工作流定义了常见开发操作的分步标准操作程序 (SOP)。用户可以通过斜杠命令等方式调用它们来引导 AI。

*   **[git-feature.md](workflows/git-feature.md)** (`/git-feature`)：审查需求、起草实现计划、创建分支以及设置代码样板的步骤，用于启动新功能开发。
*   **[git-commit.md](workflows/git-commit.md)** (`/git-commit`)：使用语义化提交信息暂存、提交和推送代码更改的标准化工作流。
*   **[git-release.md](workflows/git-release.md)** (`/git-release`)：执行发布流程，通过确定语义化版本升级、创建标签并推送到远程仓库。
*   **[review.md](workflows/review.md)** (`/review`)：审查代码更改，检查错误、安全问题和改进建议。

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
