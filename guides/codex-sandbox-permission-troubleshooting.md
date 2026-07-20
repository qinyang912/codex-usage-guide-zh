# Codex sandbox permission denied、read-only 与网络权限排查

`permission denied` 只说明操作被拒绝，不自动等于 Codex sandbox 故障。根因还可能是目标不在 workspace、受保护路径、操作系统权限、ACL、执行位、只读挂载、容器用户或企业安全策略。

## Sandbox 与 Approval 是两层控制

- **Sandbox mode** 决定命令技术上能读取/写入哪些位置，以及能否联网；
- **Approval policy** 决定何时暂停并请求用户批准。

常见 Auto 组合是：

```bash
codex --sandbox workspace-write --ask-for-approval on-request
```

把 approval 改成 `never` 只会取消审批提示，不会扩大 sandbox。工作区外写入或命令网络仍可能直接失败。

## workspace-write 仍有受保护路径

OpenAI 当前安全文档明确说明，在默认 `workspace-write` 中，即使目录位于 writable root，以下路径仍递归只读：

- `<writable_root>/.git`
- `<writable_root>/.agents`
- `<writable_root>/.codex`

当 `.git` 是 `gitdir: ...` pointer file 时，解析后的真实 Git 目录也受保护。不要用符号链接、直接改 Git 内部文件、全盘 `chmod -R 777` 或永久 full access 绕过这些边界。

## 为什么浏览器能联网，命令却不能

本地 `workspace-write` 默认关闭 spawned commands 的网络访问。确实需要且风险可接受时，可以在用户级配置中启用：

```toml
[sandbox_workspace_write]
network_access = true
```

该设置只决定命令能否联网，不会扩大文件写入范围。内置 web search、浏览器和 shell 命令也可能采用不同网络策略，因此“Codex 能搜索网页”不能证明终端中的 `curl`、包管理器或测试进程一定能访问公网。

## 快速分流

| 现象 | 优先检查 |
| --- | --- |
| `EACCES` / `permission denied` | OS 权限、属主、ACL、执行位，也可能是 sandbox |
| `EPERM` / `operation not permitted` | sandbox、系统保护、企业安全策略 |
| `read-only file system` | read-only mode、挂载或受保护路径 |
| 能读不能写 | 真实目标是否在 workspace writable roots |
| 普通文件能写，`.git` 不能写 | workspace-write 受保护路径 |
| 浏览器能上网，命令不能联网 | spawned command 网络默认关闭或 host 不同 |
| 没有弹审批，命令直接失败 | approval 为 `never` 或组织策略禁止 |

使用 `/permissions` 查看当前权限选择，使用 `/status` 查看当前会话配置。若目标不在 workspace，应该让用户明确授权或切换到正确项目，而不是借助符号链接越界。

## Windows、WSL 和 Remote

Windows 与 WSL2 是两套文件系统和权限环境；VS Code Remote、SSH 与容器也可能在远端 host 执行。排查时应从实际运行 Codex 的一侧检查目标路径、用户、挂载方式和安全策略。

同一命令在用户终端成功，只能证明那个 host 和用户环境可用。它不能自动证明 WSL、容器或 IDE Extension Host 拥有同样权限。

更完整的权限矩阵和最小恢复步骤：[Codex sandbox permission denied、read-only 与网络权限排查](https://hi-codex.com/guides/codex-sandbox-permission-denied-read-only/?utm_source=github&utm_medium=guide&utm_campaign=codex_config_sandbox)。

## 官方来源

- [Agent approvals & security](https://developers.openai.com/codex/agent-approvals-security)
- [Configuration Reference](https://developers.openai.com/codex/config-reference)
- [Slash commands](https://developers.openai.com/codex/cli/slash-commands)

本文讨论本地执行环境控制。升级 ChatGPT 套餐通常不能修复 sandbox、workspace、操作系统权限或命令网络问题。
