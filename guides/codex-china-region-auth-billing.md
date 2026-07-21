# 2026 Codex 国内使用：地区、安装、登录与计费分流

> 核对日期：2026-07-21。本文不提供地区绕过、共享账号、共享认证文件或第三方 API 中转。

中文搜索里的“Codex 国内怎么用”通常混合了五个独立问题：

1. 实际所在地是否在 OpenAI 支持范围；
2. Codex App、CLI 或 IDE 是否正确安装；
3. 使用 ChatGPT 订阅登录还是 API Key；
4. 购买 Plus、Pro、Credits 还是 API 余额；
5. 遇到的是地区、认证、网络、配置还是用量错误。

## 支持地区先于安装和付款

截至本次核对，OpenAI 的 ChatGPT 支持国家和地区列表未列出中国大陆、香港和澳门，台湾已列出。OpenAI 同时说明，从未列出的地区访问或提供服务访问，可能导致账号被封禁或暂停。

能下载客户端、打开付款页、兑换礼品卡或找到第三方代充，都不能替代官方支持地区判断。

## 两种官方登录方式

OpenAI 当前 Authentication 文档说明：

| 登录方式 | 适用入口 | 账单 |
| --- | --- | --- |
| Sign in with ChatGPT | 桌面 App、CLI、IDE；Cloud 必须使用 | ChatGPT 套餐、工作区权限与 Codex Usage |
| API Key | 本地桌面 App、CLI、IDE | OpenAI Platform 标准 API 费率 |

API Key 登录不消耗 ChatGPT 套餐包含用量，ChatGPT Plus / Pro 也不会自动抵扣 API 账单。部分依赖 ChatGPT 工作区或 Cloud 的功能在 API Key 模式下不可用。

CLI 可使用 `codex login status` 检查当前认证方式。不要复制他人的 `auth.json`、Session、Cookie 或 API Key。

## 官方安装路线

macOS / Linux 的当前独立安装器：

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

OpenAI 当前还提供 npm、Homebrew、Windows PowerShell 和 IDE 扩展路线。只从官方页面复制命令或下载客户端，不使用网盘“国内特别版”或预置凭证的安装包。

安装、登录与套餐是不同阶段：

- `command not found`：先查安装来源、PATH、系统与用户；
- 401 / 403：查认证方式、工作区、账号与地区；
- 429 / Usage Limit：区分 ChatGPT 套餐用量与 API Rate Limit；
- stream disconnected：查状态页、DNS/TLS、企业网络与 provider；
- Model not found：查模型、配置、账号权限与 endpoint。

重复充值不会修复 PATH、TLS、配置、MCP、Sandbox 或文件权限。

## Plus、Pro、Credits 与 API

| 项目 | 当前公开定位 | 不要混淆 |
| --- | --- | --- |
| Plus | 个人月度套餐，包含一档 Codex 用量 | 不是 API 余额 |
| Pro 5x | 相对 Plus 更高的 Codex 用量档 | 5x 不等于所有功能无限 |
| Pro 20x | 面向持续重度使用的更高档 | 仍可能有窗口、周限额与模型限制 |
| ChatGPT Credits | 符合条件账号的额外灵活用量 | 不是 API Credits |
| API Billing | API Key 调用按量计费 | 不由 Plus / Pro 抵扣 |

付款前先确认实际所在地、账号状态、原订阅渠道、登录方式、发卡地区、账单地址、套餐档位和退款责任。

## 官方来源

- [ChatGPT 支持的国家和地区](https://help.openai.com/zh-hans-cn/articles/7947663-chatgpt-supported-countries)
- [不受支持国家和地区的服务说明](https://help.openai.com/zh-hans-cn/articles/9131992-chatgpt-and-api-services-in-unsupported-countries-and-territories)
- [Codex Authentication](https://learn.chatgpt.com/docs/auth?surface=cli)
- [Codex CLI](https://learn.chatgpt.com/docs/codex/cli)
- [Using Codex with your ChatGPT plan](https://help.openai.com/zh-hans-cn/articles/11369540-using-codex-with-your-chatgpt-plan)

完整中文分流、Windows/macOS/Linux 入口、套餐购买与错误表见：[Codex 国内怎么用、购买、订阅、安装与登录](https://hi-codex.com/guides/codex-china-use-buy-login/?utm_source=github&utm_medium=guide&utm_campaign=codex_china_use)。

## 利益关系说明

本文仓库维护者也运营 Hi Codex 独立人工充值协助服务，因此与 ChatGPT / Codex 套餐主题存在商业利益关系。人工充值不能改变支持地区、账号状态或客户端技术条件。
