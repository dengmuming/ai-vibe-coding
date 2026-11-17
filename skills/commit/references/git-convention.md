# 团队 Git 规范 — 细则

## 1. 提交标题

格式：

```text
<scope>[<type>]: <description>
```

| 位置 | 要求 |
|-|-|
| `scope` | 必填，**单个**，全小写字母（`[a-z]+`），取模块 / 目录 / 服务名 |
| `type` | 必填，**六选一**：`feat` `fix` `refactor` `perf` `docs` `chore` |
| `description` | 小写英文字母开头，不加结尾句号，说明做了什么 |

校验正则：

```text
^[a-z]+\[(feat|fix|chore|perf|refactor|docs)\]: [a-z].{0,76}$
```

即标题 `description` 部分最长 77 个字符。

类型语义：

| type | 覆盖范围 |
|-|-|
| `feat` | 新增功能、新增对外能力 |
| `fix` | 缺陷修复 |
| `refactor` | 重构，外部行为不变 |
| `perf` | 性能优化 |
| `docs` | 文档、说明性注释 |
| `chore` | 构建脚本、依赖、配置、杂务 |

判定边界：

- 同时改了功能又改了格式：拆成两个提交，而不是挑一个 type 糊过去。
- 一个提交牵涉多个模块：说明拆分粒度不够，按可独立回滚的边界拆开。
- 纯注释补充说明 → `docs`；注释导致行为变化 → 按实际行为归类。

## 2. 提交正文

- 可选。与标题之间**空一行**。
- 讲**为什么**：背景、动机、影响面、注意事项。不复述 diff。
- 单行不超过 100 字符。
- 纯英文 ASCII，半角标点。
- 禁止出现：验证过程、测试过程、审计过程、构建过程、"phase N"、"第一阶段/第二阶段"等阶段描述。这些属于任务系统或 PR 描述，不属于提交历史。

## 3. 语言与字符

- 提交信息全英文。
- 纯 ASCII：不使用中文、全角标点、弯引号、破折号等非 ASCII 字符。

## 4. 豁免前缀

以下由 Git 自动生成或工具生成，不受本规范约束：

- `Merge ...`
- `Revert "..."` / `revert: ...`
- `fixup! ...`
- `squash! ...`

## 5. 分支命名

```text
<category>/<topic>
```

- `category`：`feature`、`fix`（可按仓库约定扩展 `hotfix`、`chore`）。
- `topic`：全小写、连字符分词。
- 示例：`feature/ptp-alignment`、`fix/upload-timeout`。
- 一个分支承载一个主题；主题漂移时另开分支。

## 6. 提交粒度

- 一个提交 = 一个主题 + 一个 scope。
- 功能改动、格式化、重命名、依赖升级互不混装。
- 多仓库改动逐仓提交，各自使用本仓 scope；不做跨仓"一个提交"。
- 拆分标准：每一笔都能独立回滚而不牵连其他改动。

## 7. 暂存与提交操作

- 显式指定路径：`git add -- <path1> <path2>`。
- 禁止 `git add -A`、`git add .`、`git commit -a`：避免把无关文件和未完成的实验代码带进提交。
- 多仓库场景先逐仓确认状态（每仓独立 `git status` / `git diff`），再分别提交。

## 8. AI 协作约定

- AI 不得自行执行 `git commit`。
- 提交前必须把：标题、正文、待提交文件清单交用户确认，用户明确同意后再提交。

## 9. 代码与注释

- 不写 spec / plan / task / round / "第 N 轮"等过程性引用。
- 过程信息放在提交说明、PR 描述或任务系统里，不留在代码与注释中。

## 10. 版本号与 changelog 联动（仓库有此约定时）

- 以仓库约定的版本文件（如 `version.cmake`、`package.json` 等）作为版本号唯一来源，其他地方不重复维护版本。
- changelog 条目格式：

  ```markdown
  ## V<M.m.p> — <主题>（<YYYY-MM-DD>）

  ### 新增
  ### 变更
  ### 修复
  ### 移除
  ```

- 版本号、changelog、对应代码改动放在**同一个提交**里。
- 发版版本号只在正式发版时更新，日常开发提交不改。
- 版本号提升后，清理残留的旧版本构建产物（如 `lib*.so.<旧版本>`）。

## 11. 反例与正例

| 反例 | 问题 | 正例 |
|-|-|-|
| `feat: add ptp alignment` | 缺 scope | `ptp[feat]: add hardware timestamp alignment` |
| `PTP[Feat]: Add PTP alignment.` | scope 大写、type 大写、描述首字母大写、句号 | `ptp[feat]: add ptp alignment` |
| `trainer[feat]: 增加点云配准` | 中文、非 ASCII | `trainer[feat]: add point cloud registration` |
| `api[feat/fix]: fix upload` | 双 type | `api[fix]: fix upload timeout` |
| `api[feat]: implement export (phase 2)` | 阶段字样 | `api[feat]: implement export` |
| 正文：`verified on test env, all tests pass` | 验证过程写进提交 | 正文写动机：`export serializes the whole project per request` |
| `git add .` 后一并提交 | 夹带无关改动 | `git add -- app/export/service.go` |

## 12. 一句话记忆

**先 scope，再 type，小写英文一句话；一笔一主题，一仓一提交，提交前问过用户。**
