---
name: dev
description: "从想法到上线的一条龙研发流程——Grill 压实想法、Spec 写成规格、Tickets 拆成工单、Implement 落地实现、Review 双轴审查。所有产物默认写入 .scratch/<feature-slug>/。用户输入 /dev 或要求按 dev 流程推进时触发，也可从任一段中途进入。"
license: MIT
metadata:
  origin: ai-vibe-coding
  orchestrates: grill-with-docs, grill-me, to-spec, to-tickets, implement, implement-spec, code-review
  invocation: user
---

# dev — 从想法到代码

五段流程，按顺序走，每段之间一道**闸门**。每段委托给已有子 skill，本 skill 只负责编排、产物路径约定与闸门把关——不复述子 skill 的内部步骤。

## `.scratch/` 产物约定

除项目 `docs/agents/issue-tracker.md` 已显式配置 GitHub / GitLab 等外部 tracker 外，走 `dev` 流程的所有产物默认采用本地 Markdown 约定，统一写入 `.scratch/<feature-slug>/`：

```text
.scratch/<feature-slug>/
├── grill.md                  # 可选：关键决策摘要、已沉淀的 ADR / Glossary 指针
├── spec.md                   # 第 2 段 to-spec 产出的规格说明书（PRD）
├── issues/                   # 第 3 段 to-tickets 拆分的垂直切片工单（每单一个文件）
│   ├── 01-<ticket-slug>.md
│   └── 02-<ticket-slug>.md
└── review.md                 # 可选：第 5 段 code-review 双轴审查结论归档
```

- `<feature-slug>` 使用简短小写短横线命名（如 `usb-audio-aec`），整个流程共用同一目录。
- 工单文件必须按 `01`、`02` 独立存放，带 `Status:` 与 `Blocked by:` 行，**不要**把所有工单合并进单个文件，也不要让产物只留在聊天记录里。

## 选入口

先看用户手里已有材料，再决定入口（同时兼容轻量需求）：

- 只有一个想法 / 需求描述 → 从 **1. Grill** 开始
- 已经讨论充分，只差写规格 → 直接进 **2. Spec**
- 已有 `.scratch/<slug>/spec.md`（或外部 spec），只差拆工单 → 直接进 **3. Tickets**
- 已有工单（`.scratch/<slug>/issues/*.md` 或明确单任务），只差写代码 → 直接进 **4. Implement**
- 代码已经写完，只差做审查收尾 → 直接进 **5. Review**
- **轻量通道**（小改动 / 单文件修复）：经用户确认可跳过 `Spec` 与 `Tickets`，走 `1. Grill (grill-me)` → `4. Implement (implement)`

不确定用户在哪段时，问一句"你现在手里有什么"，再按答案定位。

## 1. Grill — 压实想法

- 涉及架构、领域概念或跨模块改动：加载并遵循 `grill-with-docs`（联动 `grilling` + `domain-modeling`，必要时同步更新 `GLOSSARY.md` 与 `docs/adr/`）。
- 轻量需求或纯方案推演：加载并遵循 `grill-me`。
- 讨论收敛后，可将关键边界与决策摘要写入 `.scratch/<feature-slug>/grill.md` 便于后续阶段引用。

**闸门：** 用户明确确认双方就**要做什么、为什么做、边界在哪**达成共识后，才进下一段；没共识就继续追问。

## 2. Spec — 写成规格

加载并遵循 `to-spec`：

1. 结合代码库现状与 `GLOSSARY.md` / `docs/adr/`，先向用户确认测试接缝（seams）。
2. 将最终规格按模板写入 `.scratch/<feature-slug>/spec.md`（若项目配置了外部 tracker，则按 `docs/agents/issue-tracker.md` 同步发布）。

**闸门：** `spec.md` 落盘并经用户确认无误后，才进下一段；需要调整设计就改 `spec.md`，不要留到拆单或写代码时偷偷改。

## 3. Tickets — 拆成工单

加载并遵循 `to-tickets`：

1. 基于 `.scratch/<feature-slug>/spec.md` 拆分成端到端可验证的 tracer-bullet 垂直切片，并标明 `Blocked by:` 依赖边。
2. 先以编号列表向用户展示拆分方案确认粒度与依赖，批准后再一单一个文件写入 `.scratch/<feature-slug>/issues/<NN>-<slug>.md`（初始 `Status: ready-for-agent`）。

**闸门：** 工单拆分经用户批准并写入 `.scratch/<feature-slug>/issues/` 后，才进下一段；拆单中若暴露规格漏洞，回到第 2 段修 `spec.md`。

## 4. Implement — 落地实现

按任务规模选择执行器，避免无意义的流程开销或漏掉并发调度：

- **多工单 / 完整 Spec 交付**：加载并遵循 `implement-spec`。读取 `.scratch/<feature-slug>/spec.md` 与 `issues/*.md` 依赖图，在隔离 worktree 中按可执行 frontier 推进（每个工单内部走 `tdd`），合入集成分支后及时将对应工单的 `Status:` 更新为 `done`（或在 `## Comments` 追加实现摘要）。
- **单工单 / 轻量通道**：加载并遵循 `implement`，按 `tdd` 推进、定期跑类型检查与测试，并提交到当前工作分支；更新对应工单 `Status: done`（如有）。

**闸门：** 目标工单全部完成、相关测试与类型检查通过、改动已提交到工作分支。

## 5. Review — 双轴审查

加载并遵循 `code-review`（如显式需要额外代码异味基线，可改用 `grill-code-review`）：

- 以主干基线（如 `main`）作为 fixed point，结合 `.scratch/<feature-slug>/spec.md` 并行或隔离执行 **Standards** 与 **Spec** 双轴审查。
- 若第 4 段走的 `implement` 已刚跑过一次 `code-review` 且此后无新改动，只需确认审查结论并补齐对应 `.scratch/<feature-slug>/review.md`（可选），不重复空跑。
- 发现阻断项时修复后重新跑全量测试并复核审查。

**闸门：** 双轴无未解决的阻断问题、全量测试绿、`.scratch/<feature-slug>/` 工单状态同步完成，流程结束。

## 原则

1. **五段顺序不跳步**：Grill → Spec → Tickets → Implement → Review。允许从中段或轻量通道进入，但既已进入就按顺序过闸门。
2. **每段只委托，不重述**：具体方法论由各子 skill 定义，本 skill 只管调度、`.scratch/` 落盘约定与把关。
3. **闸门是硬门**：上一段没过闸门，不进下一段。
4. **上游漏洞回上游修**：Spec 发现想法没压实回 Grill；Tickets 发现规格缺口回 Spec；Implement 发现范围偏差回 Spec / Tickets 更新后再改代码。
5. **变更必须显式落盘**：讨论结论同步更新到 `.scratch/<feature-slug>/spec.md` 与对应 `issues/*.md`，不留隐式口头约定。
