# Codex config.toml 加载失败与配置不生效排查

`failed to load configuration`、`TOML parse error`、`duplicate key` 和“配置不生效”不是同一个问题。先保存错误指向的文件、行列号和字段，再判断是读取/解析失败，还是某个配置层被覆盖或忽略。

## Codex 会读取哪些配置层

当前官方文档说明：

- 用户级配置默认位于 `~/.codex/config.toml`；
- 受信任项目可以使用 `<repo>/.codex/config.toml`；
- profile 文件位于 `$CODEX_HOME/profile-name.config.toml`，通过 `--profile profile-name` 选择；
- CLI flags 与 `-c key=value` 可以临时覆盖文件值；
- 组织托管的 requirements 还可能进一步限制有效配置。

Local、WSL、SSH、容器和 IDE Remote 可能是不同 host。它们的 home、`CODEX_HOME`、环境变量和客户端版本可能不同，不能用本机终端中的一份文件代表所有环境。

在支持的交互环境中，可以使用：

```text
/debug-config
/status
```

`/debug-config` 输出配置层与 requirements 诊断；`/status` 显示当前会话配置和 token 用量。

## “字段被忽略”不等于“解析失败”

项目级 `.codex/config.toml` 不能覆盖机器本地 provider、认证、host 托管请求元数据、通知、profile 选择和 telemetry 路由。当前官方列出的项目级受限键包括：

```text
openai_base_url
chatgpt_base_url
apps_mcp_product_sku
model_provider
model_providers
notify
profile
profiles
experimental_realtime_ws_base_url
otel
```

这些键写在项目配置中会被忽略并产生启动警告，应移到用户级配置或选中的 profile，而不是继续复制同一个配置块。

## TOML 常见错误

| 错误 | 常见原因 | 安全处理 |
| --- | --- | --- |
| `TOML parse error` | 引号、数组、表头、逗号或换行不合法 | 围绕行列号检查最近改动 |
| `duplicate key` | 同一表内重复定义 | 全文件搜索键名，只保留一处 |
| `duplicate table` | 表头重复，或普通键与表冲突 | 检查对应表及上游定义 |
| `invalid type` | 布尔值写成字符串，或数组/表类型错误 | 对照当前 Configuration Reference |
| `unknown variant` / `unknown field` | 拼写错误、旧字段或版本不支持 | 核对客户端版本和当前官方字段 |

不要把其他工具的 JSON 配置直接改名为 TOML，也不要把完整配置贴到公开在线校验器。配置里可能包含私有 URL、环境变量名和组织信息。

## 最小恢复顺序

1. 备份出错文件，记录 Codex、App 或 IDE 版本；
2. 根据错误路径确认它属于 Local、WSL、SSH 还是容器；
3. 只隔离错误行附近或最近新增的一个配置块；
4. 在没有项目级配置的空目录做对照；
5. 恢复默认值后，每次只加入一个配置项；
6. 不删除整个 `~/.codex`，不公开或发送 `auth.json`、API Key、Cookie 与 Session。

更完整的中文错误矩阵和报告清单：[Codex failed to load configuration 与 config.toml 配置错误排查](https://hi-codex.com/guides/codex-config-toml-failed-to-load/?utm_source=github&utm_medium=guide&utm_campaign=codex_config_sandbox)。

## 官方来源

- [Configuration Reference](https://developers.openai.com/codex/config-reference)
- [Advanced Configuration](https://developers.openai.com/codex/config-advanced)
- [Slash commands](https://developers.openai.com/codex/reference/slash-commands)

本文讨论本地配置问题。重复登录或升级 ChatGPT 套餐通常不能修复 TOML 语法、配置层级、项目信任或字段被忽略。

## 利益披露

本文由 Hi Codex 服务团队维护；维护者提供独立人工充值协助，因此与 ChatGPT / Codex 套餐主题存在商业利益关系。Hi Codex 与 OpenAI 不存在隶属、代理或官方授权关系。本文不提供来源不明配置、共享凭证或绕过组织策略的方法。
