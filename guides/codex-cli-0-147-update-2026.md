# Codex CLI 0.147.0 更新指南：Agent Plugins、自动审批与升级注意事项

> 核对日期：2026-08-08。npm 当前稳定版与 OpenAI 官方 Changelog 均显示 `0.147.0`，正式发布时间为 2026-08-07。

Codex CLI 0.147.0 不是一次只修补界面的更新。它扩展了 Agent Plugins、会话组织、自动审批、其他 Agent 迁移和 MCP 协议支持，同时移除了旧的 `codex exec --full-auto` 兼容参数。

如果你的团队把 Codex CLI 用在脚本、CI、插件目录或多人共享配置中，升级前应先检查命令参数和权限策略；个人交互式使用则可以按原安装渠道更新后，用一个非生产仓库完成冒烟测试。

## 先确认版本，再决定是否更新

查看当前版本：

```bash
codex --version
```

通过 npm 安装的用户可以使用官方 Changelog 给出的版本化命令：

```bash
npm install -g @openai/codex@0.147.0
```

如果最初使用 Homebrew 或独立安装器，请继续使用原渠道更新，避免机器上同时存在多份 `codex`。更新后再检查：

```bash
codex --version
codex --help
codex exec --help
```

安装路径混乱、版本没有变化或出现 `command not found` 时，先看 [Codex macOS / Linux 安装与更新](codex-macos-linux-install-update.md)，不要连续叠加 npm、Homebrew 和安装脚本。

## 0.147.0 最值得关注的六项变化

### 1. Agent Plugins 与多来源目录

新版可以安装可移植的 Agent Plugins，并跨本地、个人、工作区和远端目录搜索插件。对团队来说，这意味着插件不再只是某台机器上的孤立配置，而更适合进入可追踪、可复用的目录工作流。

升级后应先确认 Codex 实际识别了哪些 Marketplace 和 Plugin，再安装或启用。插件可能包含 Skills、MCP Server、Hooks 或外部连接，目录可见不等于已经完成权限审查。创建和安装流程见 [Codex Plugins 指南](codex-plugins-create-install-marketplace.md)。

### 2. 长会话可以分区和增量浏览

0.147.0 可以把会话放进持久化、可手动排序的区段，并对很长的历史记录进行增量浏览。它解决的是“任务越来越多以后怎么找”，不是自动替你决定项目边界。

建议仍按仓库、客户或长期目标建立清楚的分区；不要把互不相关的所有聊天塞进同一项目。需要长期保留的仓库约定应写入 `AGENTS.md`，而不是只依赖某段旧对话。

### 3. 新增 `--approve-for-me`

官方发布说明新增了 `--approve-for-me`，用于让符合条件的审批请求经过自动审查。它不是“关闭 Sandbox”，也不等于所有命令都会自动执行。

使用前至少确认三层边界：

- Sandbox 允许命令读写哪些路径；
- Approval / 自动审查决定哪些越界请求可以继续；
- 组织策略、Hooks 和 Rules 是否又增加了限制。

先在低风险仓库验证自动审查会批准和拒绝什么。不要为了少一次确认而改用 `--dangerously-bypass-approvals-and-sandbox`；后者会同时绕过审批和 Sandbox，只适合已有外部隔离的受控环境。

### 4. 从 Cursor、Claude 迁移更完整

新版可以导入 Cursor 管理的 Skills，并同步从 Claude 和 Cursor 导入的对话变化，减少重复记录。导入仍然需要人工检查：路径、环境变量、MCP 认证、Hooks、命令占位符和权限语义不会因为“导入成功”就自动变得等价。

迁移后建议新建一个测试项目，逐项验证：

1. 仓库说明是否落在正确的 `AGENTS.md` 作用域；
2. Skills 的触发描述是否仍准确；
3. MCP Server 是否需要重新登录；
4. Hooks 是否会执行外部命令；
5. 导入会话能否找到原项目路径。

### 5. 支持可选的 MCP 2026-07-28 协议

0.147.0 增加了对 MCP `2026-07-28` 协议的可选支持，包括分页发现、多轮请求和非阻塞 Server 启动。现有 MCP 正常工作时，不必只为追新协议修改配置；Server 与客户端双方都明确支持后再启用，并保留旧配置和错误日志。

MCP 启动失败、超时或工具不出现时，按 [Codex MCP 配置教程](codex-mcp-configuration.md) 分别检查传输、认证、超时、工具审批和客户端版本。

### 6. Amazon Bedrock 的检索与压缩改进

通过内置 Amazon Bedrock provider 使用 Codex 的场景，新版加入缓存 Web Search 和远端会话压缩支持。这是 provider 特定能力，不应推断所有自定义 provider 都自动获得相同功能。

## 一个会影响自动化脚本的变化：`--full-auto` 已移除

0.147.0 发布说明明确写明，旧的 `codex exec --full-auto` 参数不再发布，应改用显式 Sandbox：

```bash
codex exec --sandbox workspace-write "运行测试并报告失败原因"
```

这次迁移不要只做字符串替换。还要重新确认：

- 工作目录是不是预期仓库；
- 是否真的需要网络访问；
- 命令能否写入仓库外路径；
- 失败时脚本是否保留退出码、日志和产物；
- CI 是否已经在容器或临时 Runner 中提供外部隔离。

`workspace-write` 代表允许工作区写入，不代表任意网络、系统目录或生产凭证都可访问。

## 安全和稳定性修复

0.147.0 还包含几类值得尽快获得的修复：

- 展示命令和重放历史时更完整地遮蔽 Secret 与 Bearer Token；
- 修复终端重新获得焦点、MCP 初始化和 Ghostty 快捷键场景中的输入丢失或卡住；
- 修复日文字符、Emoji、超链接和视口边界附近的渲染与光标位置；
- 改进 Windows 后台进程中断与路径处理；
- 对陌生本地项目要求显式信任，并在使用凭证前执行托管认证限制；
- 加强插件隔离；策略更新失败时拒绝网络访问。

安全修复并不替代最小权限。仓库内的 Plugin、Hook、MCP 和脚本仍应像普通依赖一样审查。

## 团队升级检查表

1. 记录升级前的 `codex --version`、登录方式和安装渠道。
2. 搜索 CI、脚本和文档中的 `--full-auto`。
3. 备份或提交 `config.toml`、`AGENTS.md`、Rules、Hooks 与 Plugin Marketplace 的预期版本。
4. 更新 CLI，再检查 `codex --help` 和 `codex exec --help`。
5. 在非生产仓库测试只读、`workspace-write`、审批和拒绝路径。
6. 验证已有 MCP、Plugins、Skills、Subagents 和导入会话。
7. Windows 团队额外测试路径、进程中断和终端输入。
8. 通过后再更新共享镜像、CI Runner 或团队安装文档。

## 官方参考

- [OpenAI：ChatGPT & Codex Changelog（August 2026）](https://learn.chatgpt.com/docs/changelog#month-2026-08)
- [OpenAI：Codex CLI 0.147.0 Release](https://github.com/openai/codex/releases/tag/rust-v0.147.0)
- [OpenAI：Codex CLI](https://learn.chatgpt.com/docs/codex/cli)
- [OpenAI：Agent approvals & security](https://developers.openai.com/codex/agent-approvals-security)

Codex CLI 更新频率很高。本文固定记录 2026-08-08 可核对到的稳定版；之后阅读时，请先看官方 Changelog 和本机 `codex --version`。

## 利益披露

本文由 Hi Codex 服务团队维护。维护者提供独立 ChatGPT / Codex 套餐相关人工充值协助，因此与套餐主题存在商业利益关系；与 OpenAI 不存在隶属、代理或官方授权关系。CLI 升级、Sandbox 和插件安全判断不应以购买套餐代替。
