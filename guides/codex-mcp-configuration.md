# 2026 Codex MCP 配置教程：添加 Server、config.toml 与 OAuth

MCP（Model Context Protocol）让 Codex 调用外部工具和上下文，例如文档、浏览器、代码托管平台或企业系统。配置时最重要的不是复制一段很长的 TOML，而是先确认 Server 的传输方式、运行位置和认证方式。

本文按当前 OpenAI Codex 文档整理，适用于 Codex CLI、IDE 扩展和同一台主机上的 Codex App。不同主机不会自动共享本地文件：macOS、WSL、SSH、容器和远程 IDE 各自有独立的 home、环境变量与进程。

## 一、先判断使用 STDIO 还是 HTTP

| 类型 | Codex 如何连接 | 适合场景 | 常见问题 |
| --- | --- | --- | --- |
| STDIO | Codex 启动本机命令，通过标准输入输出通信 | 本地脚本、npm / Python Server | 命令不在 PATH、依赖缺失、工作目录错误 |
| Streamable HTTP | Codex 连接一个 `https://` 地址 | 远程托管、团队共享、OAuth 服务 | URL、TLS、Token、OAuth 回调或网络策略错误 |

如果文档给的是 `command` 和 `args`，通常是 STDIO；如果给的是 HTTPS endpoint，通常是 Streamable HTTP。不要把其他客户端的 JSON 配置原样贴进 `config.toml`。

## 二、优先用 CLI 添加并检查 Server

添加一个 STDIO Server：

```bash
codex mcp add docs -- npx -y your-mcp-package
```

添加一个远程 HTTP Server：

```bash
codex mcp add docs --url https://mcp.example.com/mcp
```

添加后先列出当前配置：

```bash
codex mcp list
```

进入 Codex 交互界面后可使用：

```text
/mcp
```

它适合确认 Server 是否被加载、有哪些工具可用。能在列表中看到名字，只说明配置被读取；还要实际执行一个无副作用的只读工具，才能验证认证、权限和响应是否正常。

## 三、手工编辑 config.toml

用户级配置默认放在：

```text
~/.codex/config.toml
```

受信任项目还可以使用：

```text
<repo>/.codex/config.toml
```

一个最小 STDIO 示例：

```toml
[mcp_servers.docs]
command = "npx"
args = ["-y", "your-mcp-package"]
startup_timeout_sec = 20
tool_timeout_sec = 60
enabled = true
```

一个最小 HTTP 示例：

```toml
[mcp_servers.docs]
url = "https://mcp.example.com/mcp"
enabled = true
```

需要 Bearer Token 时，优先让配置引用环境变量，而不是把密钥明文写进仓库：

```toml
[mcp_servers.docs]
url = "https://mcp.example.com/mcp"
bearer_token_env_var = "DOCS_MCP_TOKEN"
```

然后在实际运行 Codex 的同一个 host 中设置 `DOCS_MCP_TOKEN`。在 macOS 终端设置的变量，不一定会进入从图形界面启动的 App；Windows 与 WSL 也不是同一个环境。

## 四、OAuth 登录与工具授权

支持 OAuth 的远程 Server 可以使用：

```bash
codex mcp login docs
```

浏览器授权成功但 Codex 仍未登录时，依次核对：

1. 终端和浏览器是否属于同一台主机与同一用户；
2. 回调端口是否被占用或被企业策略拦截；
3. Server 名是否与 `codex mcp list` 完全一致；
4. 远程服务是否允许当前账号、组织与 OAuth scope。

MCP 工具还会受到 Codex approval 设置约束。`auto`、`prompt`、`writes` 和 `approve` 的行为不同；不要为了让工具“跑起来”就全局放开写权限。更稳妥的做法是先启用只读工具，再逐项增加需要的能力。

还可以使用 `enabled_tools` 或 `disabled_tools` 缩小暴露给 Codex 的工具集合。对于能发消息、创建资源、修改代码或执行付款的工具，保留人工确认尤其重要。

## 五、从“读到配置”到“真正可用”的验证顺序

1. 执行 `codex mcp list`，确认 Server 名和类型；
2. 在 Codex 中运行 `/mcp`，确认工具已发现；
3. 对 STDIO Server，在同一终端单独运行其命令，检查 PATH 与依赖；
4. 对 HTTP Server，核对 URL、HTTPS、认证方式和组织权限；
5. 先调用一个只读工具，再测试需要写权限的操作；
6. 保存 Codex 版本、操作系统、Server 名、脱敏日志和完整错误原文。

默认启动等待时间可能不足以覆盖首次下载依赖或冷启动。只有确认 Server 最终能够启动时，才适度增加 `startup_timeout_sec`；如果工具已经连接但执行超时，再评估 `tool_timeout_sec`。无限加大超时只会隐藏启动失败、网络错误或死锁。

## 六、常见失败如何分流

| 现象 | 优先检查 |
| --- | --- |
| `command not found` / `ENOENT` | 命令、PATH、运行 host 与包管理器 |
| `startup failed` / `handshaking failed` | Server 是否启动、STDOUT 是否被日志污染、协议版本 |
| OAuth 浏览器成功但 Codex 未登录 | 回调、Server 名、账号与 scope |
| 401 / 403 | Token、OAuth、组织权限；不是超时问题 |
| 工具已发现但调用超时 | `tool_timeout_sec`、Server 后端、网络与单次任务规模 |
| CLI 可用但 IDE 不可用 | IDE 实际运行在 Local、WSL、SSH 还是容器 |

不要公开 `auth.json`、API Key、Bearer Token、Cookie、Session 或完整环境变量。也不要用关闭 TLS 校验、导入来源不明根证书或共享长期令牌来“修复”连接。

## 官方资料与继续排查

- [OpenAI：Model Context Protocol](https://learn.chatgpt.com/docs/extend/mcp?surface=cli)
- [OpenAI：Configuration Reference](https://developers.openai.com/codex/config-reference)
- [OpenAI：Agent approvals & security](https://developers.openai.com/codex/agent-approvals-security)

需要截图式操作顺序、完整字段说明和安全检查表，可继续阅读：[Hi Codex：2026 Codex MCP 配置教程](https://hi-codex.com/guides/codex-mcp-config-add-server/?utm_source=github&utm_medium=guide&utm_campaign=codex_mcp_config)。

本文核对日期为 2026-07-21。Codex 与 MCP 的入口、字段和审批行为可能变化，应以当前官方文档及本机 `codex mcp` 输出为准。

## 利益披露

本文由 Hi Codex 服务团队维护；维护者提供独立人工充值协助，因此与 ChatGPT / Codex 套餐主题存在商业利益关系。Hi Codex 与 OpenAI 不存在隶属、代理或官方授权关系。本文不提供共享凭证、来源不明 Server、权限绕过或无法核验的“官方授权”服务。
