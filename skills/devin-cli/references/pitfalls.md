# Devin CLI 低频排查

按症状查；同一错误最多重试一次，重复出现就换方向。先运行 `ai-codespec doctor --executor devin`：`devin.cli` 对应可用性/版本，`devin.auth` 对应认证；只有明确需要认证探测时再加 `--auth`。

## 认证与可用性

| 检查 | 命令 | 说明 |
|---|---|---|
| CLI 在不在 | `devin version` | 失败 → `unavailable`，检查 PATH 或 `DEVIN_CLI` |
| 登录状态 | `devin auth status` | 未登录 → 让用户 `devin auth login`，重试无意义 |
| 型号名 | `devin models list` | 型号名以 CLI 输出为准，不按口语填；填错 → `invalid-option` |

## 平台与环境

- **IP 白名单 / 代理报错**：多为本机网络出口问题（公司代理、VPN）。先查环境再怀疑任务；`environment` 类错误三连重试没有意义。
- **Windows 路径**：批次文件里的 `repo` 用绝对路径；`DEVIN_CLI` 指向含空格的 `.cmd`/`.bat` 会触发 `E_SPACED_CMD`（doctor check `devin.cli`），换无空格路径。Windows 远程安装脚本必须在 **PowerShell** 中由用户执行，doctor/setup 不代为运行。
- **shell 差异**：runtime 经子进程传参并走 `--prompt-file`，不依赖任何 shell 的引号规则；如果你在手工复现命令，注意 bash 与 PowerShell 传参写法不同。

## 输出陷阱

- **exit 0 但零输出**：非交互（`-p`）模式下权限模式遇到需要确认的调用会直接退出，完全没有开始。runtime 把它归类为 `empty-output` 并判失败；处理方式是确认任务配置后重派，不是原样重试。
- **进程一直 running 不发完成通知**：进程活着不证明工作未完成。用 `exec inspect` 看 Git 事实（提交数、worktree 状态），按证据判定。
- **空成功输出 vs 真成功**：真成功一定伴随 `commits`（Git 推导）；`succeeded` + 空 `commits` 按不可信处理。
