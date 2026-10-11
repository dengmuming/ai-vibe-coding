---
name: devin-cli
description: 完成一次 Devin CLI 执行。Start 新起一次执行、Resume 续接上一次、Inspect 检查执行事实。当用户要求启动或恢复一次 Devin 执行、查询某次执行的产出，或排查 Devin CLI 的报错、认证、权限、会话问题时使用。跨多个工作项的编排与验收不归本 skill。
compatibility: 依赖：devin CLI（在 PATH 上，或用 DEVIN_CLI 环境变量指定路径；执行前已认证）、ai-codespec runtime。执行一律发生在 runtime 管理的隔离 worktree 中；平台报错先查环境（代理 / 出口 IP），再怀疑任务本身。
---

# devin-cli — 一次 Devin 执行

中心问题：**如何可靠地完成这一次 Devin 执行，并拿到可验证的结果。**

执行机制由共享 runtime 承担（隔离 worktree、会话身份、错误分类、提交取证），你的职责是把任务讲清楚、选对操作、按 Git 事实读结果。本 skill 的视野只有这一次执行：编排多个工作项、验收生命周期都不在它这里。

## 选操作

- 用户要开始一个新任务 → **Start**
- 上一次执行中断或失败，要接着做完 → **Resume**
- 用户问"那次执行怎么样了 / 产出了什么" → **Inspect**
- 用户在问 Devin CLI 的报错、权限、会话 → 先 **Inspect** 拿事实，再查 [pitfalls](references/pitfalls.md)

## Start：新起一次执行

1. 把任务整理成一个工作项：`id`（短横线命名）、`prompt`（见"写任务"）、`acceptance`（验收标准）。
2. 写批次文件（单工作项，执行器 devin）：

```json
{
  "batchId": "<稳定执行标识，恢复时复用>",
  "repo": "<仓库绝对路径>",
  "executor": "devin",
  "executorOptions": { "permissionMode": "smart", "model": "<型号，可选>" },
  "items": [
    {
      "id": "<工作项 id>",
      "title": "<一句话标题>",
      "prompt": "<任务说明>",
      "acceptance": { "type": "auto", "checks": ["<验证命令>"] }
    }
  ]
}
```

3. 运行 `ai-codespec dispatch --batch <批次文件>`。
4. 按"读结果"核对产出；把 `batchId`、`executionId` 和 `session.id` 记进上下文；Resume 复用 `batchId`，Inspect 使用 `executionId`。

## Resume：续接一次执行

- 用**同一 `batchId`** 再次 dispatch 同一工作项：已完成的项会跳过，未完成的项由 runtime 尝试续接。
- 续接条件由 runtime 判定：会话 ID 与 worktree 绑定一致且会话仍存活，才精确续接（结果 `session.resumed = true`）；身份不可靠时带上下文新起（`session.resumed = false`，`session.note` 说明原因）。**不要在身份不可靠时坚持续接**——新起并保留上下文是正确行为，不是失败。
- 不要绕过 runtime 手工调用 `devin -c` / `devin -r`：会话身份记录会丢失，下次恢复就无从判断。

## Inspect：检查一次执行

运行 `ai-codespec exec inspect <executionId>`，得到 run 记录与当前事实的合并：worktree 是否还在、提交数、会话身份。向用户报告时区分**记录里说的**和**现在的事实**，两者不一致要以事实为准。

## 写任务（所有操作共用）

prompt 是写给一个拿着任务单的工程师的：

- 讲清目标与验收标准：做什么、做到什么程度算完、用什么命令验证。
- 写"实现并验证"，不要只描述方案。
- runtime 已在提示词里强制：不 push、不合并、只在本 worktree 内改动、收工自清产物——你只需补充任务特有的约束。
- 长任务给绝对路径与充分上下文，不要让执行者猜。

## 读结果

- `commits` 来自 Git 事实（base..HEAD），不是执行者自述。`succeeded` 但 `commits` 为空不可信。
- `error.kind` 分类处理：
  - `unavailable`：CLI 未安装或 `DEVIN_CLI` 路径不对，先让用户安装或修正路径
  - `auth`：未登录，让用户运行 `devin auth login`，不要重试
  - `invalid-option`：检查 `executorOptions`（如型号名是否真实存在）
  - `permission`：权限模式拒绝，确认任务是否需要写权限
  - `empty-output`：非交互下权限模式直接放弃、零产出——改用实现类配置重派，不要原样重试
  - `environment`：网络 / 出口 IP 白名单，先查本机代理，同一错误不要三连重试

## 原则

1. 只经 runtime 启动执行；会话身份留在 runtime 里，不手工管理。
2. 完成的证据是提交与检查结果，执行者的自述只是候选证据。
3. 默认使用 `smart` 权限模式；只有用户明确授权且任务确有需要时才使用 `dangerous`。认证、凭证、平台问题转达用户处理，不猜、不提权。
4. 一次执行只做一件事；需要多件事并行或先后依赖时，那不是本 skill 的职责。

## References

- [pitfalls](references/pitfalls.md) —— Devin CLI 低频排查：平台差异、认证状态、型号名、输出陷阱
