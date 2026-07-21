# 2026 Codex Windows 安装：App、PowerShell CLI 与 WSL2

> 核对日期：2026-07-21。本文根据 OpenAI 当前 Codex CLI、Windows App 与 WSL 官方文档整理。

很多中文教程仍把“先安装 Node.js，再用 npm 安装 Codex，Windows 最好全部放进 WSL”当作唯一答案。OpenAI 当前已经提供 Windows 独立 CLI 安装器和原生 Windows agent，因此应该先根据仓库与工具链选择环境，再安装。

## 三条官方路径怎么选

| 路径 | 适合人群 | 命令运行位置 | 项目建议位置 |
| --- | --- | --- | --- |
| ChatGPT Windows App | 需要项目列表、审查、内置终端与图形界面 | 默认 PowerShell，也可切 WSL2 agent | 原生 agent 使用 Windows 文件系统 |
| 原生 Windows CLI | 习惯 PowerShell、Windows Terminal 或 .NET 工具链 | Windows PowerShell | `C:\Users\...\code` |
| WSL2 CLI | 使用 bash、Linux 包管理、容器或仓库本来就在 WSL | Ubuntu 等 WSL2 shell | `~/code`，避免大型仓库长期位于 `/mnt/c` |

选择重点不是“哪种模式满血”，而是 Codex、Git、项目和依赖是否位于同一环境。Windows 和 WSL 可以并存，但路径、PATH、运行时与登录缓存默认各自独立。

## 路线一：ChatGPT Windows App

OpenAI 提供 Microsoft Store 下载入口，也可以运行：

```powershell
winget install --id 9PLM9XGG6VKS -s msstore
```

应用默认使用 Windows 原生 Codex agent，命令运行在 PowerShell。需要 Linux agent 时，可以在设置中把 agent 切换到 WSL，然后重启应用。

集成终端与 agent 是两个独立设置：终端显示 WSL 不代表 agent 一定已经切到 WSL2。排障时要分别确认。

## 路线二：原生 Windows CLI

OpenAI 当前 Windows 独立安装命令是：

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://chatgpt.com/codex/install.ps1 | iex"
```

只从 OpenAI 官方文档复制，并确认脚本域名是 `chatgpt.com`。安装后重新打开终端：

```powershell
codex --version
cd C:\Users\你的用户名\code\your-project
codex
```

当前独立安装器意味着首次安装 Codex CLI 不必先围绕 npm、全局目录和 Node 版本配置环境。Node.js、Python 或 .NET 仍可能是项目自身需要的开发工具。

## 路线三：WSL2 CLI

以管理员身份打开 PowerShell：

```powershell
wsl --install
wsl
```

首次安装可能需要重启 Windows。进入 WSL 的 Linux shell 后运行：

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
codex --version
codex
```

OpenAI 文档说明，WSL1 支持到 Codex 0.114；从 0.115 起 Linux sandbox 使用 `bubblewrap`，因此当前版本应使用 WSL2。可以用 `wsl -l -v` 查看发行版版本。

## VS Code 和项目路径

选择 WSL2 时，从 Linux home 下打开仓库：

```bash
mkdir -p ~/code && cd ~/code
git clone https://github.com/your/repo.git
cd repo
code .
```

确认 VS Code 左下角显示 `WSL: <distro>`，集成终端路径是 `/home/...`。OpenAI 建议 WSL2 工作流把仓库放在 Linux home；`/mnt/c` 可能有更慢的文件 I/O，并增加符号链接和权限差异。

## 安装后找不到 codex

先确认当前终端在哪个环境：

- Windows PowerShell：检查是否只在 WSL 安装；安装后重新打开终端。
- WSL shell：运行 `echo $WSL_DISTRO_NAME`，确认不是只在 Windows 原生侧安装。
- VS Code：确认窗口已经通过 `WSL: Reopen Folder in WSL` 进入 WSL 环境。

Windows 中安装的 Codex 不会自动进入 WSL PATH，反过来也一样。不要为了修 PATH 下载来源不明的“免安装版”。

## 登录、套餐与 API Key

安装客户端和账号计费是两个阶段：

- ChatGPT 登录使用当前 ChatGPT 套餐、工作区权限和 Codex 用量；
- API Key 由 OpenAI Platform 按标准 API 费率单独计费；
- 安装成功不代表 Plus、Pro、工作区或 API 账单已经正常。

出现 401、403 或浏览器回调卡住时，先运行 `codex login status`，不要重复安装，也不要复制别人的 `auth.json`。

## 安全边界

- 保留 Windows sandbox 或 WSL Linux sandbox，不默认开启 Full access；
- 不从网盘或群文件下载所谓“国内特别版”；
- 不长期以管理员权限运行日常任务；
- 不分享 `auth.json`、API Key、Session、设备代码或验证码；
- 不通过第三方 API 网关或地区绕过来冒充 OpenAI 官方环境。

完整中文对照、10 项 FAQ 和错误分流见：[Codex Windows 安装完整指南](https://hi-codex.com/guides/codex-windows-install-wsl/?utm_source=github&utm_medium=guide&utm_campaign=codex_windows_wsl)。

## 官方来源

- [Codex CLI](https://developers.openai.com/codex/cli)
- [ChatGPT desktop app for Windows](https://developers.openai.com/codex/windows/windows-app)
- [Codex on WSL](https://developers.openai.com/codex/windows/wsl)

## 利益披露

本文由 Hi Codex 服务团队维护。维护者提供独立人工充值协助，因此与 ChatGPT / Codex 套餐主题存在商业利益关系。Hi Codex 与 OpenAI、Microsoft 不存在隶属、代理或官方授权关系；本文不提供第三方安装包、地区绕过或共享凭证。
