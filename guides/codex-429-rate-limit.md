# Codex 429 Too Many Requests：Rate Limit 与 insufficient_quota 排查

`429 Too Many Requests` 不是单一故障。它可能来自 ChatGPT 套餐的 Codex 用量窗口、OpenAI API 速率限制、API Credits / 月度使用上限，或自定义 provider 的上游限流。

## 1. 先确认认证方式

| Codex 认证方式 | 额度来源 | 检查位置 |
| --- | --- | --- |
| Sign in with ChatGPT | ChatGPT 套餐包含的 Codex 用量，以及账号支持时的 Credits | Codex Usage 与客户端上限提示 |
| OpenAI API Key | OpenAI Platform API 余额、组织/项目使用上限与模型速率限制 | Platform Usage、Billing、Limits |
| 自定义 provider / Base URL | 第三方服务商余额、并发和上游规则 | 服务商控制台、状态页和文档 |

ChatGPT Plus / Pro 与 OpenAI Platform API 分开计费。升级 ChatGPT 套餐不会自动增加 API 余额；给 API 账户充值也不会增加 ChatGPT 登录下的 Codex 套餐窗口。

## 2. 按完整错误原文分流

| 错误或现象 | 更可能的含义 | 第一动作 |
| --- | --- | --- |
| `Usage limit`，并给出恢复时间 | ChatGPT 套餐的 Codex 用量窗口耗尽 | 查看 Usage、窗口、Credits 与恢复时间 |
| `Rate limit reached for requests` | API 请求数速率超限 | 降低并发与突发，检查 RPM 和响应头 |
| `Rate limit reached for tokens` | API Token 速率超限 | 缩短上下文与输出，检查 TPM 和响应头 |
| `insufficient_quota` | API Credits 或月度使用上限 | 检查 Organization、Project、Billing 与 Limits |
| `exceeded retry limit` | 客户端多次重试仍失败 | 停止重试风暴，回看最早一条 429 |

## 3. API Rate Limit 要看什么

OpenAI API 可能按 RPM（每分钟请求数）、RPD（每天请求数）、TPM（每分钟 Token）、TPD（每天 Token）等指标限流。任意一项先达到都可能触发 429，限制通常按组织和项目计算，并随模型变化。

重点保留这些脱敏响应头：

- `x-ratelimit-remaining-requests`
- `x-ratelimit-remaining-tokens`
- `x-ratelimit-reset-requests`
- `x-ratelimit-reset-tokens`
- 项目级 `x-ratelimit-*-project-tokens`

短时间集中发送请求也可能触发限制，即使一分钟的平均值看起来不高。先降低并发，再减少无关上下文和过大的输出 Token 预算。

## 4. insufficient_quota 不是普通限流

OpenAI Error codes 把两类 429 分开：

- `Rate limit reached for requests`：请求过快，应控制节奏。
- `You exceeded your current quota`：Credits 用尽或达到最大月度消费上限，应检查 Billing。

排查时确认 API Key 实际属于哪个 Organization 和 Project。不要只看另一个组织的余额，也不要用 ChatGPT Plus / Pro 充值来处理 API `insufficient_quota`。

## 5. 安全重试边界

OpenAI 建议使用指数退避，并添加随机抖动和最大重试次数。失败请求也会计入每分钟限制，所以立即无限重试只会加重问题。

安全实现至少应做到：

1. 实际响应提供 `Retry-After` 时，至少按该值等待。
2. 读取 `x-ratelimit-reset-*`，按真正瓶颈的恢复时间等待。
3. 每次等待指数增长并加随机抖动。
4. 设置最大次数和总超时。
5. 遇到 `insufficient_quota` 或明确的套餐 Usage limit 时停止重试。

## 6. 提交故障前保存什么

- 发生时间和时区；
- Codex 客户端版本与运行环境；
- ChatGPT 或 API Key 认证方式；
- 脱敏后的第一条完整 429；
- 模型、request ID、Organization 与 Project；
- 相关 `x-ratelimit-*` 响应头；
- 最小复现步骤。

不要公开 API Key、Cookie、Session、`auth.json`、设备代码或项目源码。

更详细的中文症状表与官方来源：[Hi Codex 429 排查指南](https://hi-codex.com/guides/codex-429-rate-limit-too-many-requests/?utm_source=github&utm_medium=guide&utm_campaign=codex_429_rate_limit)。

## 官方来源

- [OpenAI API Rate limits](https://developers.openai.com/api/docs/guides/rate-limits)
- [OpenAI API Error codes](https://developers.openai.com/api/docs/guides/error-codes)
- [如何解决 429：Too Many Requests](https://help.openai.com/zh-hans-cn/articles/5955604-how-can-i-solve-429-too-many-requests-errors)
- [Using Codex with your ChatGPT plan](https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan)

## 利益披露

本资料由 Hi Codex 服务团队维护；维护者提供独立人工充值协助，因此与 ChatGPT / Codex 套餐主题存在商业利益关系。Hi Codex 与 OpenAI 不存在隶属、代理或官方授权关系。本文不提供第三方 API 中转、共享 Key、限流绕过或凭证代管。
