---
name: dev
description: "从想法到上线的一条龙研发流程——grill 压实想法、to-spec 写成 spec、to-tickets 拆成工单、implement 落地代码。用户输入 /dev 触发，也可从任一段中途进入。"
license: MIT
metadata:
  source: https://github.com/mattpocock/skills
  upstream_commit: ed37663cc5fbef691ddfecd080dff42f7e7e350d
  adapted_by: ai-vibe-coding
  invocation: user
---

# dev — 从想法到代码

四段流程，按顺序走，每段之间一道**闸门**。每段委托给一个已有 skill，本 skill 只负责编排和把关——不复述子 skill 的过程。

## 选入口

按用户手里已有什么决定从哪段开始：

- 一个想法/计划，什么都没有 → 从 **Grill** 开始
- 已经讨论充分、只差 spec → 直接进 **Spec**
- 已有 spec、只差拆工单 → 直接进 **Tickets**
- 已有工单、只差写代码 → 直接进 **Implement**

不确定用户在哪段时，问一句"你现在手里有什么"，按答案定位。

## 1. Grill — 压实想法

加载并遵循 `grill-me` skill。relentless 追问，直到双方对**要做什么、为什么做、边界在哪**达成共识。

**闸门：** 用户确认"我们达成共识了"才进下一段。没共识就继续 grill，不要带着半成品想法往下走。

## 2. Spec — 写成 spec

加载并遵循 `to-spec` skill。把 grill 阶段的讨论综合成一份 spec（PRD），发布到项目 issue tracker。

**闸门：** spec 发布后，用户确认 spec 内容正确才进下一段。需要调整就改 spec，不要把改动留到工单阶段偷偷做。

## 3. Tickets — 拆成工单

加载并遵循 `to-tickets` skill。把 spec 拆成一组 tracer-bullet 工单，声明阻塞关系，发布到 tracker。

**闸门：** 工单拆分经用户批准并发布后，进下一段。拆工单时发现 spec 有漏洞——回到 Spec 修，不要在工单里改 spec。

## 4. Implement — 落地代码

加载并遵循 `implement` skill。按工单实现，TDD 优先，定期跑类型检查和测试，完成后用 `grill-code-review` 自审，提交到当前分支。

**闸门：** 代码通过审查、测试绿、提交完成，流程结束。

## 5. Review — 代码审查

加载并遵循 `grill-code-review` skill。对实现的代码进行审查，确保代码质量、测试覆盖率和代码规范。

**闸门：** 代码通过审查、测试绿、提交完成，流程结束。

## 原则

1. **顺序不可跳着走**：Grill → Spec → Tickets → Implement → Review。允许从中段入口，但一旦进入就按顺序往下，不回跳（除非某段发现上游有漏洞，回到上游修完再继续）。
2. **每段只委托，不重述**：四个子 skill 各自定义自己的过程，本 skill 不复述。
3. **闸门是硬门**：上一段没过闸门，不进下一段。
4. **上游漏洞回上游修**：spec 阶段发现想法没压实，回 Grill；tickets 阶段发现 spec 有漏洞，回 Spec。不要在下游偷偷改上游的产物。
5. **想法在 grill 阶段变，spec 要跟着变**；spec 在 tickets 阶段变，工单要跟着变。变更要显式，不要隐式。
