# Codex Windows 安装核对清单：原生 CLI、Windows App 与 WSL2

更新日期：2026-07-21。

Codex 在 Windows 上有三条官方路径。先根据项目位置和工具链选一种主要环境，再安装和登录，避免把 PowerShell、WSL、PATH 与仓库目录混在一起。

## 路线选择

| 路线 | 适合场景 | 项目建议位置 |
| --- | --- | --- |
| ChatGPT Windows App | 图形界面、项目列表、并行聊天、审查和集成终端 | 原生 agent 优先 Windows 文件系统 |
| 原生 Codex CLI | PowerShell、Windows Terminal、.NET 或 Windows 工具链 | `C:\Users\...` 下的项目目录 |
| WSL2 Codex CLI | bash、Linux 包管理、容器或仓库已在 WSL | `~/code` 等 Linux home 路径 |

OpenAI 当前把 Windows 11 列为推荐基线。完整更新的 Windows 10 属于 best effort，实际至少需要 1809 或更新版本提供现代控制台能力。

## 原生 Windows CLI

只从 OpenAI 官方页面复制 Windows 独立安装命令：

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
```

安装后重新打开终端：

```powershell
codex --version
cd C:\Users\你的用户名\code\your-project
codex
```

脚本域名应为 `chatgpt.com`。不要运行网盘、论坛附件或所谓“绿色版”里的安装脚本。

## ChatGPT Windows App

从 Microsoft Store 安装，或使用：

```powershell
winget install --id 9PLM9XGG6VKS -s msstore
```

应用默认让 Codex agent 在 Windows 原生环境中运行 PowerShell。需要 Linux agent 时，可在设置里切换到 WSL，并重启应用。集成终端和 agent 运行环境是独立设置，应分别确认。

## WSL2 CLI

管理员 PowerShell：

```powershell
wsl --install
wsl
```

进入 WSL 的 Linux shell 后：

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
codex --version
codex
```

Windows 与 WSL 是两个独立环境。在 Windows 安装 Codex 不会自动让 WSL PATH 出现 `codex`，反过来也一样。

OpenAI 当前文档说明，WSL1 支持到 Codex 0.114；从 0.115 起 Linux sandbox 使用 `bubblewrap`，所以当前版本应使用 WSL2。

## VS Code 与仓库位置

WSL2 工作流应从 WSL shell 打开项目：

```bash
mkdir -p ~/code && cd ~/code
git clone https://github.com/your/repo.git
cd repo
code .
```

确认 VS Code 左下角显示 `WSL: <distro>`，终端路径是 `/home/...`。大型仓库放在 `/mnt/c` 可能出现更慢的 I/O，以及符号链接、权限和文件监听差异。

## 常见错误分流

- `codex` 找不到：确认当前是 PowerShell 还是 WSL，并重开终端；在目标环境单独安装。
- WSL 中大型仓库很慢：把仓库迁移到 `~/code` 后重试。
- PowerShell 阻止脚本：确认来源是官方命令，并遵循 Microsoft 或组织的执行策略，不要长期关闭安全限制。
- 401 / 403：安装和认证是两个阶段；运行 `codex login status`，再检查浏览器回调、凭证、工作区和账号状态。
- 429 / Usage limit：检查 ChatGPT Usage、恢复时间、Credits 或 API Billing，不要反复重装。

更完整的中文版本矩阵、沙箱和 FAQ： [2026 Codex Windows、CLI 与 WSL2 安装指南](https://hi-codex.com/guides/codex-windows-install-wsl/?utm_source=github&utm_medium=guide&utm_campaign=codex_windows_wsl)。

## 官方资料

- [OpenAI Codex CLI](https://developers.openai.com/codex/cli)
- [ChatGPT desktop app for Windows](https://developers.openai.com/codex/windows/windows-app)
- [OpenAI Windows sandbox](https://developers.openai.com/codex/windows/windows-sandbox)
- [OpenAI Codex WSL](https://developers.openai.com/codex/windows/wsl)

## 利益披露

本文由 Hi Codex 服务团队维护；维护者提供独立人工充值协助，因此与 ChatGPT / Codex 套餐主题存在商业利益关系。Hi Codex 与 OpenAI、Microsoft 不存在隶属、代理或官方授权关系。本文不提供第三方 API 中转、地区绕过、来源不明安装包或共享认证文件。
