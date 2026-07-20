# Codex VS Code 插件不显示、一直转圈与 app-server 启动失败排查

适用于 VS Code、Cursor、Windsurf 中的 OpenAI Codex IDE 扩展。先按现象分流，不要把“图标不显示”“401”“Model not found”和 `failed to start codex app-server` 当成同一个问题。

## 30 秒分流

| 现象 | 优先判断 | 第一步 |
| --- | --- | --- |
| 安装后没有 Codex 图标 | 侧边栏入口、扩展未启用或装在错误环境 | 命令面板运行 `Codex: Open Codex Sidebar` |
| 只有 Logo、空白或一直转圈 | Extension Host、登录、app-server、网络或配置 | 运行 `Developer: Reload Window` 后查看 Output |
| `failed to start codex app-server` | 扩展需要的本地客户端服务未启动 | 保存完整错误、版本、环境和日志 |
| 401 / 403 | 认证、工作区、组织策略或账号状态 | 查看登录状态，不分享 `auth.json` |
| 404 / Model not found | 模型、旧配置、账号可用性或 provider | 使用 `/model` 和推荐模型做对照 |
| 429 / Usage limit | 用量、速率或 API Billing | 查看 Usage、恢复时间和登录方式 |
| 只在 WSL / SSH / Codespaces 失败 | 扩展安装侧、PATH、配置或远端网络错位 | 确认左下角环境与集成终端属于同一侧 |

## 图标不显示：先用官方命令打开

OpenAI 当前 IDE 文档说明，如果 Codex 图标不可见，可打开命令面板运行：

```text
Codex: Open Codex Sidebar
```

然后检查：

1. 扩展来自 OpenAI，Marketplace ID 为 `openai.chatgpt`。
2. 扩展没有被 Disabled、Disabled (Workspace) 或工作区信任策略停用。
3. Activity Bar 中的 Codex 入口没有被手动隐藏。
4. Remote WSL、SSH 或 Codespaces 中，扩展在实际运行侧启用，而不是只装在 Local。

## 一直转圈、空白或没有输入框

先保存编辑器版本、扩展版本、发生时间和 Local/WSL/SSH 环境，再按顺序执行：

1. 命令面板运行 `Developer: Reload Window`。
2. 更新编辑器与 Codex 扩展，关闭所有相关编辑器窗口后重开。
3. 新建一个最小对话，用无私密内容的小文件测试。
4. 确认界面是否正在等待权限批准、终端命令或文件写入。
5. 在 `View → Output` 查看 Codex 与 Extension Host 相关通道的第一条明确错误。

不要直接删除整个 VS Code 用户目录、`~/.vscode-server`、`~/.codex` 或 `auth.json`。这些位置可能包含扩展、设置、会话、日志和访问令牌；粗暴清理会丢失证据，也可能制造新的环境差异。

## WSL、Remote SSH 与 Codespaces

VS Code Remote 会把界面和 Extension Host 分到不同系统或机器。CLI 在本机正常，不能证明远端扩展也正常。

- WSL2：确认左下角显示 `WSL: <distro>`，集成终端使用 Linux 路径。
- Remote SSH：检查远端扩展状态、远端 PATH、远端配置与远端网络。
- Codespaces / 容器：检查容器内扩展、项目级配置、权限与重建后的依赖。
- Local：排除另一个用户、旧编辑器实例或多个扩展版本。

所有命令都应在扩展实际运行环境的集成终端中验证。

## `failed to start codex app-server`

OpenAI 官方 App Server 文档说明，`codex app-server` 用来驱动 VS Code 扩展等富客户端，负责认证、会话历史、审批和流式事件。这个错误表示扩展所需的本地服务没有正常建立，本身不是“Plus 到期”或“Codex 额度用完”的同义词。

在扩展实际运行环境中检查：

```bash
codex --version
codex login status
codex app-server --help
```

- 命令找不到：检查扩展运行侧、安装来源和 PATH。
- CLI 正常但扩展失败：比较 IDE 扩展版本、运行环境和 Output 日志。
- 日志明确是路径或权限错误：保留实际路径和错误码，检查安装完整性、安全软件与企业策略。
- 更新后突然出现：查看 OpenAI Codex changelog 与官方仓库是否有同版本、同错误字符串的 issue。

若同一环境的 CLI 稳定可用，可暂时从集成终端继续工作，同时提交可复现报告；不要为 app-server 错误重复充值。

## CLI 与 IDE 共享配置

OpenAI Developer settings 文档说明，CLI 与 IDE 扩展共享用户级 `~/.codex/config.toml` 和可信项目中的 `.codex/config.toml`。错误的模型、provider、MCP 启动命令或项目配置可能同时影响两者。

在交互界面查看当前状态和配置层：

```text
/status
/debug-config
```

如果问题只在某项目出现，用不含项目级 `.codex/config.toml` 的空目录做对照；每次只改一处并保留差异。

## 提交故障报告前的脱敏清单

可以提供：

- 操作系统和 Local、WSL2、Remote SSH、Codespaces 或容器环境；
- 编辑器版本、Codex 扩展版本和 `codex --version`；
- ChatGPT 登录或 API Key 登录方式；
- 完整但已脱敏的错误、request ID、退出码与最小复现；
- 同环境 CLI 是否可用，以及问题是否只在某项目出现。

不要公开：`auth.json`、API Key、Session、设备代码、验证码、完整环境变量、私有仓库代码、公司路径或未检查的整包日志。

更完整的中文症状表、10 项 FAQ 与日志入口：

https://hi-codex.com/guides/codex-vscode-extension-not-working/?utm_source=github&utm_medium=guide&utm_campaign=codex_vscode_extension

官方资料：

- https://chatgpt.com/docs/codex/ide
- https://chatgpt.com/docs/app-server
- https://chatgpt.com/docs/developer-settings
- https://github.com/openai/codex/issues

利益关系披露：本文由 Hi Codex 服务团队整理；维护者提供独立人工充值协助，与 ChatGPT / Codex 套餐主题存在商业利益关系。Hi Codex 与 OpenAI、Microsoft、VS Code、Cursor 或 Windsurf 不存在隶属、代理或官方授权关系。
