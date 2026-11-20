---
name: commit
version: 1.0.0
description: 按团队 Git 规范生成并校验提交——强约束 commit 标题 <scope>[<type>]: 描述、正文、分支命名、提交粒度与版本/changelog 联动。当用户要提交代码、写 commit message、拆分提交、定分支名，或检查提交信息是否合规时使用。
license: MIT
metadata:
  origin: ai-vibe-coding
  invocation: model
---

# commit — 团队 Git 提交规范

提交前按本 skill 走一遍，产出符合团队规范的 commit 与分支名。规范全文见 `references/git-convention.md`。

## 铁律

- **显式加文件**：`git add -- <路径1> <路径2>`。禁止 `git add -A`、`git add .`、`git commit -a`。

## 提交流程

1. **看范围**：`git status --short`、`git diff --stat`。多仓库场景逐仓执行，不要跨仓一把提交。
2. **定 scope**：取改动所属的模块 / 目录 / 服务名，单个、小写。
3. **定 type**：从六类里选一个（见下表），拿不准时选最贴近"用户/系统可感知变化"的那一类。
4. **写标题**：`<scope>[<type>]: <description>`，用下面的正则校验。
5. **写正文**（可选）：标题后空一行，讲动机与影响，不复述 diff。
6. **拆分**：一个提交 = 一个主题 + 一个 scope。混了就拆。
7. **版本联动**：仓库有版本/changelog 约定时同步更新（见下）。
8. **暂存并提交**：`git add -- <paths>` → `git commit`。

## 标题格式

```text
<scope>[<type>]: <description>
```

- `scope`：必填、唯一、全小写字母，取模块/目录/服务名。
- `type`：`feat` | `fix` | `refactor` | `perf` | `docs` | `chore`，只能一个。
- `description`：小写英文字母开头，不加句号，写清做了什么。
- 全篇纯 ASCII、半角标点，禁止中文和全角符号。

校验正则：

```bash
pattern='^[a-z]+\[(feat|fix|chore|perf|refactor|docs)\]: [a-z].{0,76}$'
printf '%s\n' "$msg" | grep -Eq "$pattern" || echo "INVALID SUBJECT"
```

### type 选型

| type | 用途 |
|-|-|
| `feat` | 新增功能、新增能力 |
| `fix` | 修复缺陷 |
| `refactor` | 重构，对外行为不变 |
| `perf` | 性能优化 |
| `docs` | 文档、注释说明 |
| `chore` | 构建、依赖、配置、脚本等杂务 |

## 正文

- 可选，与标题之间**空一行**。
- 写"为什么改、影响什么"，不写"改了哪几行"。
- 单行 ≤100 字符，纯英文 ASCII。
- **不写**验证/测试/审计/构建过程，**不写** "phase N"、"第一步/第二步"这类阶段描述。

## 分支命名

```text
<category>/<topic>
```

- `category`：`feature`、`fix`（按仓库约定可扩展 `hotfix`、`chore`）。
- `topic`：全小写、连字符分词，例：`feature/ptp-alignment`、`fix/point-cloud-upload-timeout`。
- 一个分支只承载一个主题。

## 豁免

以下前缀不受标题格式约束：`Merge`、`Revert`、`revert:`、`fixup!`、`squash!`。

## 拆分与多仓库

- 单一提交只包含一个主题，不混功能、格式化、重命名、依赖升级。
- 多仓库改动**逐仓提交**，各用各自 scope，不拼一个提交。
- 跨模块的大改优先按"可独立回滚"的边界拆成多个提交。

## 版本与 changelog 联动（仓库有该约定时）

- 以仓库约定的版本文件（如 `version.cmake`、`package.json` 等）作为版本号唯一来源。
- changelog 条目格式：`## V<M.m.p> — <主题>（<YYYY-MM-DD>）`，下设 `### 新增` / `### 变更` / `### 修复` / `### 移除`。
- 版本号、changelog、业务代码改动放在**同一个提交**里。
- 发版版本号只在正式发版时更新，日常开发提交不动。
- 版本号提升后，同步清理仓库约定需移除的旧版本构建产物（如 `lib*.so.<旧版本>`）。

## 代码与注释里

不写 spec / plan / task / round / "第 N 轮" 等过程引用，这类信息属于提交说明或任务系统。

## 提交前自检

- [ ] 标题匹配正则，`scope` 唯一且小写
- [ ] `type` 六选一，未混用
- [ ] `description` 小写字母开头，无结尾句号
- [ ] 全篇 ASCII + 半角标点
- [ ] 正文空一行，讲动机，无验证过程 / 阶段字样
- [ ] 一个提交一个主题一个 scope
- [ ] `git add -- <路径>` 逐个添加
- [ ] 版本 / changelog 已同步（如仓库有约定）

## 正反例

| ❌ 错误 | ✅ 正确 |
|-|-|
| `feat: add ptp alignment` | `ptp[feat]: add hardware timestamp alignment` |
| `PTP[Feat]: Add PTP alignment.` | `ptp[feat]: add ptp alignment` |
| `trainer[feat]: 增加点云配准` | `trainer[feat]: add point cloud registration` |
| `api[feat/fix]: fix upload` | `api[fix]: fix upload timeout` |
| `api[feat]: implement export (phase 2)` | `api[feat]: implement export` |
| 正文写 "verified on test env, tests pass" | 正文写动机："export runs per request and times out on large projects" |
| `git add .` + 一次提交全部改动 | 按主题 `git add -- <paths>` 分批提交 |

## 参考

- `references/git-convention.md` — 规范细则、判定边界与常见疑问
