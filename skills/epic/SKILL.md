---
name: epic
description: 持续实现一个由多个工作项组成的开发事项。Create 建 Epic 并拆工作项、Inspect 以 Git 与执行器事实对账、Advance 有界推进一轮。当用户给出一个要跨多次会话、多个工作项完成的开发结果时使用。小的一次性修改直接做或走 dispatch，不经本 skill。
compatibility: 依赖：ai-codespec runtime（Epic 状态、dispatch、对账、推进）。确定性机制由 runtime 承担，本 skill 只保留判断；台账载体只记录飞书文档身份，凭据由 lark-cli / 宿主密钥设施承担。
---

# epic — 一个 Epic 的生命周期

中心问题：**哪个行动最能推进这个 Epic 向它声明的结果。**

Epic 是持久的顶层开发结果，由多个可独立验证的工作项组成；状态跨会话保存在共享 runtime，你不需要、也不应该在对话里重建它。三个分支：Create（建立）、Inspect（对账）、Advance（推进）。

## 选分支

- 用户给出一个开发结果，要开一个多工作项的事项 → **Create**
- 用户问"现在什么状态 / 到哪了" → **Inspect**
- 用户说"继续做 / 推进 / 接着干"（或定时唤醒触发）→ **Advance**
- 一批工作项已经明确、只是要执行一次 → 用 `dispatch`，不建 Epic

## Create：建立 Epic

1. 把开发结果写成一句 `outcome`（要达成什么，不是要做什么任务）。
2. 拆工作项：每个可独立验证、一个上下文窗口内可完成。**推断依赖**并如实写进 `dependsOn`——能并行的不要串成链，必须先后的不要并行。
3. 每个工作项声明验收标准，如实选择：
   - `auto`：给出验证命令（`checks`），完成可自动判定
   - `human`：需要人工验收（涉及产品判断、视觉、外部承诺）
   - `external`：完成依赖外部条件
4. 台账载体（可选）：`--carrier <飞书文档>` 只记录文档身份；全局默认在 `~/.manifold/epic.yaml` 的 `carrier` 配置，Epic 级覆盖全局。
5. 运行：

```sh
ai-codespec epic create <id> --outcome "<结果>" --items <工作项.json> --repo <仓库绝对路径> [--carrier <飞书文档>]
```

工作项文件是 JSON 数组：`[{ "id", "title", "acceptance", "dependsOn" }]`。

## Inspect：以事实对账

```sh
ai-codespec epic inspect <id>
```

得到状态与当前事实的合并视图：每个执行的 worktree 是否存在、提交数、分支是否已并入、批次是否卡死、agent 报告原文。报告给用户时：

- 差异（`findings`）按事实说，不照抄状态文档——状态说在进行、事实说已死，就报已死。
- 完成候选只认 Git 证据；无提交支撑的"成功"不报完成。
- 台账同步（若声明载体）以本视图为准。

## Advance：有界推进一轮

```sh
ai-codespec epic advance <id> [--executor fake|devin] [--concurrency N]
```

1. advance 先对账（陈旧执行重排、证据完成项结算）再选择，本轮派发**全部就绪且未阻塞**的工作项，并发上限内并行执行。
2. 完成由验收标准决定：`auto` 检查通过即完成；`human` 完成后等待 owner 验收（`epic accept <id> <itemId>`）；`external` 挂起等待外部条件。
3. **一次推进后即停**。不要在同一个动作里连推多轮——把结果报告给用户（或等下一次唤醒）。
4. 唤醒是中立的：手动命令、Devin Automations、Qoder 循环、CI 定时触发同一行为；错过或重复唤醒都安全（幂等）。
5. 执行器与并发默认取全局配置（`epic.yaml` 的 `executor` / `concurrency`），显式 flag 优先。

## 原则

1. Epic 状态唯一存放在 runtime（`~/.manifold/epics/`，`MANIFOLD_HOME` 可覆盖）；不要在对话或文件里另立台账。
2. 判断留在这里，机制交给 runtime：锁、原子写、worktree、会话身份、调度都不是提示词的职责。
3. 完成的证据是提交与检查结果；执行者说"做完了"只是候选证据。
4. 依赖推断宁缺勿滥：不确定先后就先不声明，Advance 的跳过原因会告诉你哪里被阻塞。
5. 人工验收只在声明了 `human` 的工作项上出现，不搞全局一刀切。
6. 小的一次性修改不建 Epic——直接做，或用 `dispatch` 派一批。
