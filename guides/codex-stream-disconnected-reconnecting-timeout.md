# Codex stream disconnected、Reconnecting 5/5 与超时排查

`stream disconnected before completion` 只说明响应流在完成事件前断开，不等于套餐额度耗尽。`Reconnecting 1/5` 到 `5/5` 也只说明客户端正在重试，真正根因通常在它前面的第一条错误。

## 先按错误发生阶段分流

| 现象 | 发生阶段 | 先检查 |
| --- | --- | --- |
| `stream closed before response.completed` | 已开始响应，完成前中断 | 服务状态、长连接、provider、客户端版本 |
| `error sending request for url` | 请求发送或建立连接 | 实际域名、DNS、TLS、公司网络 |
| `request timed out` / `ETIMEDOUT` | 连接或等待响应超时 | 超时时长、端点、是否只在大任务出现 |
| `certificate` / `SSL` / `TLS` | HTTPS 握手 | 系统时间、证书链、企业 TLS 检查 |
| 401 / 403 / 404 / 429 | 已获得明确 HTTP 语义 | 转到认证、模型或限流排查 |
| MCP startup / tool timeout | MCP 进程或工具 | 对应命令、依赖、环境变量和工具耗时 |

## 保存证据，不要先删除配置

记录：

- Codex、App 或 IDE 扩展版本；
- 操作系统，以及 Local、WSL、SSH 或容器环境；
- ChatGPT 登录还是 API Key；
- 默认 OpenAI provider 还是自定义 `base_url`；
- 第一条完整错误、时间、时区和 request ID；
- 影响一个任务、一个设备，还是多个成员。

交互界面中可以查看：

```text
/status
/debug-config
```

公开反馈前删除 API Key、Session、Cookie、`auth.json`、邮箱、私有路径和项目内容。不要上传整个 `.codex` 目录。

## 安全的排查顺序

1. 查看 [OpenAI Status](https://status.openai.com/)，确认是否有 Codex、ChatGPT 或 API 事件。
2. 用一个小文件和最小提示词建立对照，排除超大上下文与长时间无事件。
3. 比较 CLI、App 和 IDE；只有 IDE 失败时还要检查 Extension Host 与 app-server。
4. 确认错误中的实际域名，区分 OpenAI 默认端点和第三方 provider。
5. 在组织与地区规则允许的前提下，对照一条已知正常网络；恢复后让 IT 检查 DNS、TLS 和防火墙。
6. 备份后一次只回退一个自定义配置，不要删除全部设置或凭证。
7. 最小任务仍稳定失败时，提交时间、request ID、版本和脱敏日志。

不带凭证的基础连接测试：

```bash
curl -Iv https://chatgpt.com/
curl -Iv https://api.openai.com/
```

拿到 403、404 等 HTTP 状态，只能说明 DNS、TCP 和 TLS 已经走到 HTTP 响应阶段，不能证明认证和完整流式响应一定正常。

## os error 10054、10060、54、60

错误号必须结合操作系统和完整文字判断：

| 错误 | 常见含义 | 注意 |
| --- | --- | --- |
| Windows `10054` | 已有连接被远端或中间路径重置 | 不能只凭它认定 OpenAI 主动断开 |
| Windows `10060` | 建连或等待响应超时 | 不能区分防火墙、丢包、代理和上游等待 |
| macOS `54` | 常见 `Connection reset by peer` | 不能与 Windows 10054 机械合并 |
| macOS `60` | 常见 `Operation timed out` | 不能证明套餐、账号或模型有问题 |

这些错误适合定位传输阶段，不适合直接推出一个固定的代理设置。

## Reconnecting 5/5 不等于必须关闭 WebSocket

OpenAI 当前 Codex Configuration Reference 对自定义 model provider 列出：

- `request_max_retries`：HTTP 请求重试次数，默认 4；
- `stream_max_retries`：SSE 流中断重试次数，默认 5；
- `stream_idle_timeout_ms`：SSE 空闲超时，默认 300000ms；
- `supports_websockets`：声明该 provider 是否支持 Responses API WebSocket 传输。

这些是自定义 provider 配置字段，不能证明默认 ChatGPT 连接的所有 5 次重连都由 WebSocket 导致。不要复制来源不明的 `config.toml`，也不要安装定时任务持续改写配置。

若 Codex App 每次提问前重连 5 次、最后仍能回答，应记录 App 版本和第一条错误，并比较 CLI、App、更新前后与另一条正常网络。openai/codex 的相关 issue 被关闭为重复问题，只证明症状相似，不代表维护者确认某个代理方案是统一根因。

## 第三方 provider 断流

使用自定义 provider 时，检查：

- `model_provider` 与 `base_url` 是否真的是当前有效配置；
- provider 支持的 Responses API、SSE 或 WebSocket 方式；
- 网关是否提前关闭长响应；
- API Key 环境变量是否属于该 provider；
- 默认 OpenAI 配置是否能作为对照。

是否需要 `/v1` 由服务商文档和 Codex 配置方式决定，不存在适用于所有 provider 的统一后缀。不要把第三方故障描述为 OpenAI 官方故障，也不要公开任何 Key。

## 套餐升级能修复吗？

通常不能。Plus、Pro 或 Credits 只在错误被确认是账号访问资格或 Codex Usage limit 时相关。DNS、TLS、防火墙、自定义 provider、客户端回归、服务事件和 MCP timeout 不会因为重复充值自动修复。

更完整的中文错误矩阵、反馈清单与官方来源：

[Codex stream disconnected、Reconnecting 5/5、Network Error 与超时排查](https://hi-codex.com/guides/codex-stream-disconnected-reconnecting-timeout/?utm_source=github&utm_medium=guide&utm_campaign=codex_stream_disconnected)

## 资料与利益披露

技术依据包括 OpenAI Codex Configuration Reference、OpenAI Status 和 openai/codex 公开 issue。本文不提供地区绕过、第三方 API 中转、共享凭证、关闭 TLS 校验或来源不明根证书安装方法。

作者参与 Hi Codex 独立人工充值服务，与 ChatGPT / Codex 套餐主题存在商业利益关系；与 OpenAI 不存在隶属、代理或官方授权关系。
