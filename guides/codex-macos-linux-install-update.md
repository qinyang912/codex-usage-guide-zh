# Codex macOS / Linux 安装与更新：install.sh、npm、Homebrew

> 核对日期：2026-07-21。本文用于区分安装来源、PATH 和多版本问题，不提供第三方镜像或来源不明二进制文件。

## 先给结论

OpenAI 当前同时列出独立安装器、npm 和 Homebrew。选择一种主要来源，并一直使用同一来源更新：

| 来源 | 安装 | 更新 |
| --- | --- | --- |
| OpenAI 独立安装器 | `curl -fsSL https://chatgpt.com/codex/install.sh \| sh` | 重新运行同一命令 |
| npm | `npm install -g @openai/codex` | 重新运行同一命令 |
| Homebrew | `brew install --cask codex` | `brew upgrade --cask codex` |

不要为了“确保成功”连续使用多种来源。它们可能各自安装一份 Codex，但 shell 只会执行 PATH 中最先找到的那一份。

## 安装或更新后如何验证

```bash
command -v codex
type -a codex
codex --version
```

- `command -v codex`：显示当前 shell 优先执行的路径；
- `type -a codex`：尽量列出 PATH 中发现的所有同名命令；
- `codex --version`：显示实际运行版本。

再打开一个全新的终端窗口复测。旧 shell 可能缓存了命令位置，或者尚未读取更新后的启动配置。

## command not found 怎么排查

1. 确认安装命令已经成功结束；
2. 新开终端，再运行 `command -v codex`；
3. 确认当前使用 zsh、bash 还是 fish，以及对应启动文件是否生效；
4. 确认 Codex 是否安装在另一用户、容器、SSH host 或 Remote IDE 环境；
5. 若出现 `permission denied`，转为检查文件、挂载或 Sandbox 权限；
6. 若出现 `exec format error`，检查 `uname -m`、CPU 架构和安装来源。

本机、容器和远端环境不会自动共享安装。IDE 终端找不到 Codex 时，应先确认 Extension Host 和终端实际运行在哪一侧。

## 更新后为什么还是旧版本

`type -a codex` 返回多个路径时，逐一确认它们来自独立安装器、npm、Homebrew 还是旧的手动下载。选择一种主要来源，按原包管理器正常移除不再使用的副本，再新开终端核对。

不要直接删除不认识的系统目录。ChatGPT 桌面应用中的 Codex 与独立 CLI 也是不同入口；更新桌面应用不能证明 shell 中的 CLI 已更新。

## npm 出现 EACCES

先检查 npm 全局目录与当前用户权限，或换用 OpenAI 独立安装器/Homebrew。不要把 `sudo` 当作默认答案，也不要对系统目录递归执行 `chmod 777`。

## 安装完成后是否必须购买 API 余额

不一定。首次运行 `codex` 时，可选择使用 ChatGPT 登录或其他可用登录方式。ChatGPT 套餐中的 Codex 用量与 OpenAI Platform API 账单分开；CLI 安装成功不等于必须购买 API 余额，Plus / Pro 也不代表 API Key 调用免费。

官方参考：

- [Codex CLI](https://developers.openai.com/codex/cli)
- [Codex Authentication](https://developers.openai.com/codex/auth)

更完整的 PATH、多版本、权限、架构和登录分流表：[Hi Codex：Codex macOS / Linux 安装与更新](https://hi-codex.com/guides/codex-cli-macos-linux-install-update/?utm_source=github&utm_medium=guide&utm_campaign=codex_macos_linux_install)

利益关系披露：维护者参与 Hi Codex 人工充值服务，与 ChatGPT / Codex 套餐主题存在商业利益关系；与 OpenAI、Apple、Homebrew 或 npm 不存在隶属、代理或官方授权关系。
