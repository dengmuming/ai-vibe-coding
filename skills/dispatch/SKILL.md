---
name: dispatch
description: 安全执行一个已经明确的工作项批次并返回可验证结果。Prepare 把批次整理到可执行、Dispatch 安排隔离 worktree 与执行器执行、Resume 恢复中断的执行。当用户给出一批已经明确的工作项（任务单、ticket、改动清单）要求执行时使用。跨会话的目标管理与验收生命周期不归本 skill。
compatibility: 依赖：ai-codespec runtime（Git worktree 隔离、执行器适配、结构化结果）、可用执行器（fake 干跑 / devin）。批次所需信息全部显式传入，不依赖外部状态与全局配置。
---

# dispatch — 一个批次的执行

中心问题：**如何安全地执行这个给定批次，并返回可验证的结果。**

确定性机制（隔离 worktree、并发调度、会话身份、提交取证）由共享 runtime 承担；你的职责是把批次整理到无歧义、选对参数、按证据读结果。一个批次是有界的：明确的仓库、明确的工作项、明确的执行器。

## 选分支

- 批次还不够明确（缺验收标准、任务说明含糊、依赖关系没说清）→ **Prepare**
- 批次已明确，要执行 → **Dispatch**
- 上一次派发中断 / 部分失败，要接着做完 → **Resume**

## Prepare：把批次整理到可执行

逐项核对，任何一项不满足就先补齐再派发——runtime 会拒绝歧义项，但提前整理比事后读拒绝信息更快：

1. 每个工作项有 `id`（唯一、短横线命名）、`prompt`（要交给执行者的任务说明：目标、验收标准、约束）、`acceptance`。
2. 验收标准三选一，如实声明：
   - `auto`：给出验证命令列表（`checks`），命令必须可自动判定成败
   - `human`：需要人工验收，不写假命令
   - `external`：完成依赖外部条件（上游服务、他人决策）
3. 工作项之间的依赖写进 `dependsOn`（只依赖同批次内的项）。
4. 仓库（`repo`）给绝对路径；执行器选 `fake`（干跑 / 演示 / CI）或 `devin`（真实执行）。
5. 含糊就向用户提问细化；**不要带歧义项进入执行**。

## Dispatch：执行批次

1. 写批次文件：

```json
{
  "batchId": "<稳定批次标识，供幂等重派>",
  "repo": "<仓库绝对路径>",
  "runDir": "<run 记录目录>",
  "worktreeRoot": "<隔离 worktree 根目录>",
  "executor": "devin",
  "concurrency": 2,
  "items": [
    {
      "id": "<工作项 id>",
      "title": "<一句话标题>",
      "prompt": "<任务说明>",
      "acceptance": { "type": "auto", "checks": ["<验证命令>"] },
      "dependsOn": []
    }
  ]
}
```

2. 运行 `ai-codespec dispatch --batch <批次文件>`。
3. 读结构化结果，逐项核对：

| 字段 | 含义 |
|---|---|
| `batchId` | 批次身份，幂等重派与清理都靠它 |
| `items[].status` | `succeeded` / `failed` / `rejected` / `skipped` / `pending-acceptance` / `external-blocked` |
| `items[].commits` / `items[].diff` | Git 推导的提交与变更摘要，不是执行者自述 |
| `items[].worktree` / `branch` | 该项的隔离工作区与分支 |
| `items[].rejection.reason` | 被拒原因，按它细化后重派 |
| `items[].session` | 执行器会话身份（devin），`resumed` 区分续接 / 新起 |

- `rejected` 项没被执行：按 reason 细化，修好后重派。
- 并发执行的工作项各自位于独立 worktree，互不覆盖；原工作区未提交的改动不受影响。
- `auto` 由 runtime 执行 checks；`human` 返回 `pending-acceptance`；`external` 返回 `external-blocked`。完成的证据是提交与检查，执行者自述只是候选证据。

## Resume：恢复中断的执行

- 用**同一 `batchId`** 重新 dispatch 同一批次：已完成的项跳过（引用先前提交与执行身份），失败 / 被拒的项重新执行，devin 执行器在会话身份可靠时自动续接（`session.resumed = true`）。
- 恢复前先用 `ai-codespec exec inspect <executionId>` 看事实：worktree 是否还在、提交数是否与记录一致。事实与记录不符时以事实为准。
- 同批次不得换仓库；批次身份与仓库绑定。

## 收尾

- 批次全部结算后，如需释放磁盘：`ai-codespec dispatch cleanup <batchId>`（只删本批次 worktree，run 记录保留作证据）。
- 向用户报告：每项的最终状态、提交、未解决问题（`unresolved`），以及等待人工验收的项。

## 原则

1. 批次所需信息全部显式传入，不依赖任何外部状态。
2. 歧义项执行前拒绝并细化，不带病执行。
3. 幂等：同批次重派不重复已完成的工作。
4. 结果按证据读：commits 与 checks 是事实，报告是说法。
5. 一次有界派发做一轮事；跨会话持续推进不是本 skill 的职责。
