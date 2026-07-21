# ChatGPT Plus 包含 API Key 吗？OpenAI API 充值与 Codex 登录

> 更新于 2026-07-21。先说结论：**ChatGPT Plus / Pro 不包含 OpenAI Platform API 余额。**API Key 也不是一张可以单独充值的卡；它只是某个 Platform 项目的访问凭证，调用费用从该组织的 API Billing 中结算。

## 30 秒分清两条路线

| 需求 | 登录与计费 | 查看位置 |
| --- | --- | --- |
| 在 Codex App、CLI 或 IDE 里交互式写代码 | `Sign in with ChatGPT`，使用套餐内 Codex Usage / Credits | ChatGPT / Codex Usage |
| 程序、SDK 或 CI/CD 调用模型 | API Key，按 Platform API 标准价格计费 | Platform Billing / Usage |
| 使用 Codex Cloud | 需要 ChatGPT 登录 | Codex Cloud 与 ChatGPT 工作区 |
| 解决 `insufficient_quota` | 先看当前是否使用 API Key；若是，看 Platform Billing | Platform 组织、项目与 Limits |

所以“买了 Plus 为什么 API 还报余额不足”的常见答案是：两套账单彼此独立。升级 Plus 不会给 API 组织增加美元余额，购买 API 预付费额度也不会把 ChatGPT Free 变成 Plus。

## API Key 怎么获取

1. 登录自己控制的 OpenAI Platform 账号，并选择正确的组织和项目。
2. 进入 [API Keys](https://platform.openai.com/api-keys) 创建 Secret。
3. 完整 Secret 只在创建时显示一次，应保存到秘密管理工具或环境变量，不要写进 Git 仓库。
4. 如果 Key 丢失或泄露，创建新 Key、更新应用并撤销旧 Key；不要向第三方购买“补发 Key”。
5. 按项目拆分开发、测试和生产 Key，同时设置预算、限额与告警。

不要把完整 Key 粘贴到聊天、工单、截图或网页表单中。共享 Key 的控制人可以随时撤销凭证、修改预算或查看用量，也无法保证付款来源和数据处理边界。

## OpenAI API 预付费 Billing 当前怎么工作

符合条件的 Platform 用户可以在 Billing 中购买预付费额度。设置时可配置自动充值阈值、充值金额和页面提供的月度上限，也可以关闭自动充值。

当前官方帮助说明还包括这些容易忽略的规则：

- 预付费额度有效期为一年，过期额度不退款。
- 购买后的余额显示可能延迟几分钟，不要因为短暂未同步连续重复付款。
- API 计费可能延迟入账，因此余额也可能短暂出现负数。
- 手动购买额度不计入自动充值月度上限。

付款方式、最低购买额、支持地区和税费可能变化，应以自己的 Platform Billing 页面为准。所谓“API Key 充值”更准确的说法，是为自己控制的 Platform 组织配置付款方式或购买预付费额度。

## Codex 用 ChatGPT 登录还是 API Key

个人交互式使用通常先选择 ChatGPT 登录：

```bash
codex login
codex login status
```

CI/CD 或明确需要按 API Token 计费时，可以通过标准输入登录 API Key：

```bash
printenv OPENAI_API_KEY | codex login --with-api-key
codex login status
```

不要把 Key 直接写进 shell history，也不要把它设置成整个 CI job 都能读取的全局变量。如果仓库代码或构建脚本不可信，应把凭证只暴露给一次 Codex 调用，或使用经过审核的官方 Action。

两种路线的差别不只在付款：本地 Codex App、CLI 和 IDE 支持两种认证；Codex Cloud 需要 ChatGPT 登录。API Key 路线按 Platform 模型权限和价格执行，也不应默认拥有套餐表里的全部云端能力。

## 常见错误怎么分流

| 现象 | 优先检查 |
| --- | --- |
| `Incorrect API key` | Key 是否完整、是否已撤销、环境变量是否仍是旧值 |
| `insufficient_quota` | 当前组织、项目余额、Billing 与月度限制 |
| `429 Too Many Requests` | RPM / TPM、并发、重试策略和组织等级；也保留完整错误判断是否为配额不足 |
| 买了余额仍报错 | 等待几分钟，确认付款成功且买到当前组织 |
| 有 Plus 但 API 报错 | 用 `codex login status` 确认是不是走 API Key 路线 |
| Codex Cloud 不出现 | 是否使用 ChatGPT 登录、套餐资格和仓库连接 |

保留错误全文、request ID、发生时间、组织/项目和模型，但不要在求助截图中暴露 Key。

## 国内付款、第三方中转与共享 Key

官方 Platform API 的 Billing、Usage、Key 和发票都位于用户自己控制的组织中。第三方中转 API 的域名、Key、模型、价格、日志和售后由第三方决定，不能描述成“OpenAI 官方 API 余额”。

不要通过伪造地址、绕过地区限制或购买共享 Key 解决付款问题。使用前应核对 OpenAI 当前支持地区、付款方式与账号要求；如果选择第三方服务，还要单独评估数据处理、日志保留、模型真实性、限流、退款和停服风险。

更完整的中文表格、官方来源和错误分流见：[ChatGPT Plus、OpenAI API Key、Billing 与 Codex 登录完整说明](https://hi-codex.com/guides/chatgpt-plus-api-key-openai-api-billing/?utm_source=github&utm_medium=guide&utm_campaign=plus_api_billing)。

## 来源与利益关系

本文依据 OpenAI Codex Authentication、Codex Pricing、API Prepaid Billing、API Key 官方帮助与 API Pricing 核对。价格、入口和地区规则会变化，应以当前官方页面和账号内 Billing 为准。

作者维护 Hi Codex，并提供用户自己 ChatGPT 账号的 Plus / Pro 独立人工充值协助，对会员主题存在商业利益；与 OpenAI、闲鱼或 API 中转平台不存在隶属、代理或官方授权关系。服务不出售 API Key、共享 Key、第三方中转额度或 OpenAI Platform API Credits。
