# 2026 ChatGPT 充值、直充、代充与 Codex 用量指南

> 更新日期：2026-07-21。本指南面向使用自己 ChatGPT 账号的用户，帮助区分 ChatGPT Plus、ChatGPT Pro、Codex 用量、ChatGPT Credits 与 OpenAI API 账单。

很多“ChatGPT 充值”“ChatGPT 直充”“ChatGPT 代充”“Codex 充值”的问题，实际混合了两套完全不同的计费体系：

- 使用 ChatGPT 账号登录 Codex：消耗 ChatGPT 套餐包含的 Codex 用量，或账号可购买的 ChatGPT Credits。
- 使用 API Key 登录 Codex：按 OpenAI Platform API 的实际用量单独计费。

两者的余额和账单不要混为一谈。

## 先用一张表判断你需要什么

| 你的需求 | 应查看的位置 | 常见误区 |
| --- | --- | --- |
| 在 Codex 应用、CLI 或 IDE 中使用 ChatGPT 账号 | ChatGPT 套餐与 Usage | 误把 API 余额当作套餐额度 |
| Codex 提示用量达到上限 | ChatGPT Usage / Credits | 没先看恢复时间就直接升级 |
| 脚本或 CI 通过 API Key 调用模型 | OpenAI Platform Billing | 以为 Plus 会抵扣 API 费用 |
| 修改手机端订阅 | Apple App Store 或 Google Play | 在错误的平台查不到订阅 |
| 国内支付方式不便 | 官方支持的支付渠道或独立人工协助 | 把共享账号当成自己账号直充 |

## Codex 入门、安装与 Skills

如果刚开始使用 Codex，先不要把 App、CLI、IDE 扩展和 Cloud 当成不同套餐：

- [Codex App、CLI、IDE 与 Cloud 怎么选](guides/codex-app-cli-ide-cloud.md)：按代码位置、运行环境、权限和远端任务需求选择入口。
- [Codex macOS / Linux 安装与更新](guides/codex-macos-linux-install-update.md)：对比 install.sh、npm、Homebrew，排查 command not found、PATH 和多份旧版本。
- [Codex Skills 与 SKILL.md](guides/codex-skills-skill-md.md)：创建聚焦工作流，区分 Skill、Plugin、MCP 和 AGENTS.md。
- [Codex 国内使用：地区、安装、登录与计费](guides/codex-china-region-auth-billing.md)：先核对支持地区，再分清 ChatGPT / API Key、套餐和技术错误。

安装、登录和套餐是三个不同阶段。`codex: command not found` 应先检查安装与 PATH；401/403 更接近认证或工作区；Usage Limit 才需要查看套餐用量、恢复时间和 Credits。

## Plus、Pro、Credits 与 API 的区别

### ChatGPT Plus

OpenAI 当前 Codex 定价文档将 Plus 列为个人套餐之一，并说明它包含 Codex 的多个使用入口。具体价格、功能和地区可用性可能变化，购买前应以账号内页面和官方定价页为准。

### ChatGPT Pro 5x / 20x

官方文档把 Pro 描述为相对于 Plus 提供更高的 Codex rate limits，并提供 5x 与 20x 档位。这里的倍数表示 Codex 使用上限级别，不等于 API Token 余额，也不是“速度永久提高 5 倍或 20 倍”。

截至 2026 年 7 月 21 日，OpenAI Codex Pricing 列出的本地消息范围如下；本地消息与云端任务共享同一个 5 小时窗口，另外可能还有周限额：

| 模型 | Plus / 5 小时 | Pro 5x / 5 小时 | Pro 20x / 5 小时 |
| --- | ---: | ---: | ---: |
| GPT-5.6 Sol | 15–90 | 75–450 | 300–1,800 |
| GPT-5.6 Terra | 20–110 | 100–550 | 400–2,200 |
| GPT-5.6 Luna | 50–280 | 250–1,400 | 1,000–5,600 |

这些数字是范围而不是固定可发送条数。模型、代码库和任务复杂度、上下文、推理、工具、检索、缓存以及本地或云端运行都会影响实际消耗。20x 也不等于所有模型和功能无限使用。

中文价格、升级切换和完整模型表见：[ChatGPT Pro 5x / 20x 区别、额度与充值](https://hi-codex.com/guides/chatgpt-pro-recharge-5x-20x/?utm_source=github&utm_medium=readme&utm_campaign=pro_tier_limits)。

### ChatGPT Credits

Credits 用于在符合条件的 ChatGPT 套餐中扩展 Codex 等功能的使用量。是否可购买、价格和适用范围应以账号内 Usage 页面显示为准。

符合条件的 Plus / Pro 用户可在 ChatGPT 网页版或 Codex 应用进入 `Codex Settings → Usage / Usage Dashboard → Credits` 查看余额和购买入口。账号支持时还可以开启自动充值；启用前应设置触发余额、充值金额和页面提供的月度限制。

### OpenAI API

API Key 是单独的用量计费方式。使用 API Key 登录 Codex 时，OpenAI 按 Platform API 标准费率计费，而不是消耗 ChatGPT 套餐包含的用量。

## “直充”和“代充”通常是什么意思

这些词不是 OpenAI 官方套餐名称，不同商家的定义可能不同：

- **自己的账号直充**：最终套餐落在用户自己的 ChatGPT 账号中。
- **人工代充**：第三方人工协助完成支付或订阅操作，交付时间取决于人工沟通和账号状态。
- **共享号 / 租号**：用户拿到的是他人控制的账号或多人共用账号，不等同于自己的账号升级。
- **兑换码或礼品卡**：是否能用于目标地区、平台和套餐，需要逐项核对，不能默认通用。

如果选择第三方协助，应先确认交付的是自己的账号套餐，还是共享账号；同时确认总价、预计时间、失败退款条件和需要提供的信息。

独立充值网站、电商平台、应用商店和共享账号的判断维度不同。详细的账号归属、收款渠道、低价来源、凭证红线和退款清单见：[2026 ChatGPT 充值平台怎么选](guides/chatgpt-recharge-platform-safety.md)。

## 礼品卡、促销码和第三方激活码不是一回事

Google 搜索结果常把几种不同的“码”混在一起：

- **Apple Gift Card**：先兑换为 Apple 账户余额，再由 Apple 渠道购买符合条件的订阅；它不是 ChatGPT 专属礼品卡。
- **OpenAI 促销或推荐码**：通常带有活动对象、地区、有效期、新老用户和付款方式限制，不应默认可以转卖或适用于所有账号。
- **第三方激活码**：可能只是第三方平台自己的订单凭证、代付入口或授权链接，不自动等于 OpenAI 官方代码。
- **商家优惠码**：通常只用于确认活动价格，不能直接在 ChatGPT、Apple 或 Google 页面激活套餐。

判断时先确认发行方、兑换域名、账号归属、需要的权限、自动续费渠道和失败退款条件。不要在陌生页面提交密码、验证码、Session、Cookie 或 API Key。

官方参考：[OpenAI Plus promotions and referrals](https://help.openai.com/en/articles/10492689-chatgpt-plus-promotions-and-referrals)、[Apple 兑换礼品卡说明](https://support.apple.com/en-us/118242)。

更完整的中文对照表见：[ChatGPT Plus 礼品卡、兑换码与激活码区别](https://hi-codex.com/guides/chatgpt-plus-gift-card-promo-code/?utm_source=github&utm_medium=readme&utm_campaign=gift_card_code)。

## ChatGPT Plus 免费试用或首月免费是真的吗？

目前没有面向所有新用户长期固定开放的 Plus 免费试用。真实存在的是按活动定向提供的试用、折扣或推荐邀请；资格、时长、付款方式和地区由每次活动决定。

领取前先在官方结算页确认价格确实显示为 0 美元或活动承诺的折扣，并记录首次扣款日和续费价格。多数促销会在结束后自动续为标准 Plus 月付订阅；不想继续时，应从原订阅平台至少提前 24 小时取消。免费促销取消后可能立即结束访问。

当前美国、加拿大大学生的官方 Codex 活动提供 100 美元 ChatGPT Credits，用于 Codex 超额使用；它不是免费 Plus，也不是 API Credits。

学生资格、SheerID、2,500 Credits、有效期和 Edu 区别见：[2026 ChatGPT 学生优惠与 Codex Credits](guides/chatgpt-student-discount-codex-credits.md)。

详细 Promo Code、自动续费和骗局对照见：[2026 ChatGPT Plus 免费试用核对清单](guides/chatgpt-plus-free-trial.md)；完整中文页面见：[ChatGPT Plus 免费试用与促销资格](https://hi-codex.com/guides/chatgpt-plus-free-trial-promo/?utm_source=github&utm_medium=readme&utm_campaign=plus_free_trial)。

## iPhone / iOS 怎么充值 ChatGPT Plus？

使用官方 ChatGPT iOS App 订阅时，Plus 权益落在购买时登录的 ChatGPT 账号，扣款、续费和取消则由 Apple 管理。购买前应确认 ChatGPT 登录方式、Apple 账户地区、确认页总价和是否已有网页或 Google Play 订阅。

Apple 礼品卡兑换成功只代表余额进入 Apple 账户，不等于 Plus 已开通。Apple 已扣款但仍显示 Free 时，应先登录正确的 ChatGPT 账号并使用 `Restore purchases`，不要直接再次购买。删除 App 也不会取消订阅。

官方参考：[iOS 升级付费订阅](https://help.openai.com/en/articles/7905739-chatgpt-ios-app-upgrading-to-a-paid-subscription)、[恢复 Apple 购买](https://help.openai.com/en/articles/8346573-restoring-a-chatgpt-subscription-purchased-in-the-apple-app-store)。

完整步骤见：[2026 ChatGPT Plus iOS / App Store 充值核对清单](guides/chatgpt-plus-ios-app-store-recharge.md)。

## Apple 已扣款未到账和 ChatGPT 重复扣费怎么处理

先区分“只有一笔费用但 Plus 未显示”和“网页、Apple、Google 同时存在多份订阅”：

1. 暂停再次付款，保存 Apple / Google / OpenAI 收据和 ChatGPT 套餐页面。
2. 分别检查 iPhone 的 Apple ID 订阅、Google Play 订阅以及 chatgpt.com 的 Billing。
3. 如果通过 Apple App Store 购买且已经扣款，但当前账号仍显示 Free，在 ChatGPT iOS 应用打开 `Settings → Account → Restore purchases`。
4. 如果 Apple 续费因付款问题失败，不要直接把它当成已经取消。Apple 后续可能恢复续费；此时若已在网页或 Google Play 重买，就可能出现两份订阅。
5. 确认重复后，保留一份并取消其他订阅。Apple 收费向 Apple 申请退款，Google Play 收费按 Google 流程处理，chatgpt.com 收费通过 OpenAI 支持处理。

卸载 ChatGPT App 不会取消应用商店订阅；取消续费通常也不会自动退回已经发生的费用。

官方参考：[OpenAI：避免跨平台重复扣费](https://help.openai.com/en/articles/20001043-how-do-i-avoid-being-charged-twice-if-i-subscribe-to-chatgpt-on-ios-android-and-the-web)、[OpenAI：恢复 Apple App Store 购买](https://help.openai.com/en/articles/8346573-restoring-a-chatgpt-subscription-purchased-in-the-apple-app-store)。

中文排查步骤见：[ChatGPT Plus 重复扣费、Apple 已扣款未到账与退款](https://hi-codex.com/guides/chatgpt-plus-duplicate-charge-refund/?utm_source=github&utm_medium=readme&utm_campaign=duplicate_charge)。

## Codex 额度为什么变少，多久刷新

OpenAI 当前 Pricing 页面明确说明：Work mode 与 Codex 共用 pricing、Credits 和 usage limits；其他 agentic 功能在开始计费后也可能共享 Codex 限制，目前在 Plus / Pro 上明确列出的是 ChatGPT for Excel。因此，即使没有继续使用 Codex CLI，同一账号的 Work mode 或 ChatGPT for Excel 也可能让剩余用量下降。这里没有把所有 Workspace Agents 都列入个人套餐共享池，不应自行扩大范围。

Codex 也不是固定的“每发一条扣一次”。消耗取决于模型、代码库和任务复杂度、上下文大小以及本地或云端运行；小脚本可能只占很少额度，大型代码库、长任务和长上下文会显著增加单条消息的消耗。

搜索结果里的“5 小时限制”“周限额”和“额度重置”经常被混为一谈。实际账号可能同时受到短时间窗口、较长周期上限和套餐档位影响；具体数值、恢复时间与 Credits 入口会随套餐、模型、任务和账号状态变化，应以 `Codex Settings → Usage` 当时显示为准。

- **短时间窗口**：适合判断是否只需等待当前窗口恢复，不等于账号永久没有额度。
- **周限额或较长周期上限**：即使短窗口恢复，较长周期额度仍可能限制继续使用。
- **ChatGPT Credits**：符合条件的账号可在 Usage 页面购买额外用量；它不是 OpenAI Platform API 余额。
- **临时重置或测试变化**：个别账号一段时间内看不到原限制，不代表 OpenAI 永久取消所有 5 小时或周限制。

短时窗口恢复不代表周限额同时清零，也不能统一按北京时间每天 0 点推算。CLI 活跃会话可以用 `/status` 查看剩余限制；到达页面显示的时间后，应重新读取 Usage 状态，不要靠连续创建任务测试。如果在一个进行中的 turn 里达到上限，官方当前说明该 turn 可以在公平使用限制下继续完成，完成后再选择等待、Credits、可用 reset 或升级。

官方参考：[Codex Pricing](https://learn.chatgpt.com/docs/pricing)、[Using Codex with your ChatGPT plan](https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan)、[Codex rate card](https://help.openai.com/en/articles/20001106-codex-rate-card)。

中文排查表见：[Codex 额度变少、刷新时间、5 小时/周限额与 Credits](https://hi-codex.com/guides/codex-quota-limit-credits/?utm_source=github&utm_medium=readme&utm_campaign=codex_quota_refresh)。

## Codex 429、Rate Limit 与 insufficient_quota 不要混在一起

Codex 显示 `429 Too Many Requests` 时，先确认认证方式和完整错误原文：

- ChatGPT 登录并显示 Usage limit / 恢复时间：检查 Codex Usage、套餐窗口和 Credits。
- API Key 显示 `Rate limit reached for requests` 或 tokens：检查组织与项目的 RPM、TPM 等限制，读取 `x-ratelimit-*` 响应头并降低并发。
- API Key 显示 `insufficient_quota` 或 `exceeded your current quota`：检查 Platform Billing、Credits 与月度使用上限，继续重试不会产生余额。
- 自定义 provider / Base URL：还要检查第三方网关、共享上游与服务商自己的并发限制。

OpenAI 说明失败请求也会计入每分钟限制，因此不能无限立即重试。应使用带随机抖动、最大次数与总超时的指数退避；错误明确属于套餐用量或 `insufficient_quota` 时，应停止这条重试路线。

独立排查清单见：[Codex 429 Too Many Requests、Rate Limit 与 insufficient_quota](guides/codex-429-rate-limit.md)。

## Codex stream disconnected、Reconnecting 5/5 怎么排查

`stream disconnected before completion` 表示响应流在完成事件前断开，不自动等于套餐额度不足；`Reconnecting 1/5` 到 `5/5` 只表示客户端在重试，应回看第一条真实错误。

先确认错误发生在建立连接、TLS 握手、响应流、自定义 provider，还是 MCP 工具。保存 Codex 版本、操作系统、实际端点、时间、request ID 和脱敏日志，再对照 OpenAI Status、CLI/App/IDE、最小任务与一条已知正常网络。不要因为看到 5/5 就直接关闭 WebSocket、删除整个 `.codex` 或导入来源不明的根证书。

Windows `os error 10054/10060` 与 macOS `54/60` 必须结合系统和完整文字判断，错误号本身不能证明是 OpenAI、代理或本机导致。

完整诊断表见：[Codex stream disconnected、Reconnecting 5/5 与超时排查](guides/codex-stream-disconnected-reconnecting-timeout.md)。

## Codex 额度不够时的检查顺序

1. 确认当前登录方式是 ChatGPT 账号还是 API Key。
2. 打开 Usage 页面，记录剩余用量与恢复时间。
3. 记录触发问题时使用的模型、仓库规模、上下文长度和任务持续时间。
4. 排除登录失效、客户端版本、网络和服务异常。
5. 缩小上下文，把大任务拆成可验证的小步骤。
6. 对轻量任务选择更节省用量的模型或工作方式。
7. 只有在长期稳定不足时，再比较 Credits、Pro 5x 与 Pro 20x。

如果决定购买 Credits，先确认登录的是需要补充额度的 ChatGPT 账号，不要误入 OpenAI Platform 的 API Billing。页面没有 Credits 入口时，不要购买来源不明的“额度卡密”。

任务看起来相似，也可能因为模型、上下文、推理、工具调用、检索和缓存不同而消耗不同用量，因此不能仅按提示词长度估算。

## ChatGPT 充值前检查清单

充值前还要先确认账号所在地与付款方式发行地符合 OpenAI 当前支持范围。能打开页面、下载 App、兑换应用商店礼品卡或找到第三方代充，都不能单独证明所在地属于官方支持地区。

地区状态、`Unsupported Country` 与旅行后误判的安全排查见：[ChatGPT 支持国家、地区限制与 Unsupported Country](guides/chatgpt-supported-countries.md)。

- [ ] 套餐落在自己的账号，而不是共享账号。
- [ ] 已确认 Plus、Pro 5x 或 Pro 20x 的具体档位。
- [ ] 已确认页面价格、聊天优惠和最终支付价。
- [ ] 已确认预计到账时间及失败退款条件。
- [ ] 不发送 Session、Cookie、恢复码或长期有效令牌。
- [ ] 不相信“永久不封”“官方授权”等无法核验的承诺。
- [ ] 完成后在 ChatGPT 账号内核对套餐状态与下一次续费信息。
- [ ] 如果曾提供临时登录协助，完成后更新密码并启用 MFA。

## Android / Google Play 订阅要同时核对两个账号

通过 Google Play 购买 ChatGPT 套餐时，有两个彼此独立的身份：

- **Google Play 账号**负责付款、订单、续费和取消；
- **购买当时登录的 ChatGPT 账号**接收套餐权益。

同一部安卓手机可以同时登录多个 Google 和 ChatGPT 账号，因此付款前应核对 Google Play 确认页里的付款账号，以及 ChatGPT App 当前的登录方式。移动订阅一旦绑定，不能转移给另一个 ChatGPT 账号，也不能在多个 ChatGPT 账号之间共享。

如果 Google Play 已扣款但 ChatGPT 仍显示 Free，不要再次购买。先确认订单最终状态和收据对应的 Google 账号，再用购买当天相同的邮箱、Google、Microsoft 或其他登录方式进入原 ChatGPT 账号，并在 Android App 设置中尝试 `Restore purchases`。提示订阅关联其他账号时，重装 App 不会改变绑定关系。

取消 Google Play 订阅必须使用购买时的 Google 账号进入 Play 商店订阅管理；卸载 ChatGPT App 不会取消。更换到 chatgpt.com 网页订阅前，应先确认 Google Play 不再续费，否则网页、Apple 与 Google Play 可以分别存在订阅并分别收费。

详细步骤见：[ChatGPT Plus 安卓 / Google Play 充值、付款失败和恢复购买](https://hi-codex.com/guides/chatgpt-plus-android-google-play-recharge/?utm_source=github&utm_medium=readme&utm_campaign=android_google_play)。

需要先比较官方网页、App Store、Google Play 与人工协助，可查看 [ChatGPT 会员购买指南：Plus、Pro 与购买渠道怎么选](guides/chatgpt-membership-buy-plus-pro.md)。

## 常见问题

### Codex 提示 Error running remote compact task 怎么办？

先确认客户端版本并查看这句后面的完整错误。openai/codex 维护者在 2026 年 6 月说明，新版已把原先独立的远程压缩逻辑移到本地并复用普通请求端点；仍看到 `remote compact task` 时，应先更新并记录版本。`stream disconnected`、404、429、`context window` 超限、子进程 timeout 与压缩输出异常仍需分别处理。

先保存工作树和关键状态，再用新最小会话做对照；环境支持时用 `/status` 查看上下文和限制，当前会话仍可操作时再尝试 `/compact`。不要直接删除整个 `.codex`、手工修改 Session JSON 或并发无限重试。完整分流见：[Codex remote compact task 与上下文压缩排查](guides/codex-context-compaction-remote-compact-task.md)。

### ChatGPT Plus 退款多久到账？

退款至少包含申请审核、平台完成处理和支付方式入账三个阶段，不能用一个固定天数回答所有渠道。

- chatgpt.com 网页：OpenAI 当前说明符合资格时在 5-7 个工作日内处理；
- Google Play：OpenAI 当前说明符合资格时在 10 个工作日内处理，之后卡退款通常还需 3-5 个工作日显示；
- Apple：通常 24-48 小时提供申请状态更新，获批后 Apple 余额最多约 48 小时，卡等账单最多可能 30 天。

取消续费不会自动退回已经发生的费用。完整状态入口和超时清单见：[ChatGPT 退款时间与状态查询](guides/chatgpt-refund-timeline.md)。

### ChatGPT Plus 可以转移到另一个账号吗？更换邮箱后还在吗？

ChatGPT 订阅不能从一个账号转到另一个账号。需要改用新账号时，应先取消旧账号订阅，再在目标账号重新购买，避免两份订阅同时扣费。

如果只是在符合资格的同一个账号内通过 `Settings → Account` 修改邮箱，OpenAI 当前说明订阅、聊天记录、Projects、自定义 GPT、工作区成员关系与设置会保留。它与使用新邮箱注册另一个账号不同。

移动端订阅还会绑定购买时的 Apple ID / Google Play 账号和当时登录的 ChatGPT 账号，重新安装 App 不会换绑。导出的聊天 JSON 只能上传到一段新对话中参考，不能恢复原侧边栏或转移订阅。

官方参考：[订阅不能跨账号转移](https://help.openai.com/en/articles/9135236-can-i-transfer-my-chatgpt-subscription-to-a-new-account)、[修改邮箱](https://help.openai.com/en/articles/4936827-how-to-change-your-email-address)。完整核对清单见：[订阅转移、更换邮箱与聊天记录迁移](guides/chatgpt-subscription-transfer-email-change.md)。

### Codex VS Code 插件不显示或一直转圈怎么办？

先按现象分流，而不是直接清空缓存：图标不显示时运行 `Codex: Open Codex Sidebar`；空白或持续加载时重新加载窗口并查看 Output；出现 `failed to start codex app-server` 时检查扩展实际运行环境、版本和日志；401、403、404、429 应分别进入认证、模型和用量分支。

WSL、Remote SSH 与 Codespaces 会把 Extension Host 放在不同环境，本机 CLI 正常不能证明远端扩展正常。不要删除或分享 `auth.json`、Session、API Key 和整包 `.codex` 目录。

完整检查表见：[Codex VS Code 插件不显示、一直转圈与 app-server 启动失败排查](guides/codex-vscode-extension-troubleshooting.md)。

### Codex 提示 404 Model not found / unsupported model 怎么办？

先不要重装，也不要把所有 404 都解释为第三方接口少了 `/v1`。先运行 `codex --version` 和 `codex login status`，确认使用 ChatGPT 登录、OpenAI API Key 还是自定义 provider；再在交互会话运行 `/model`，从当前账号实际展示的推荐模型中选择一个做对照。

如果同一账号的推荐模型 A 正常、模型 B 稳定返回 404，更像模型可用性、旧配置、灰度或后端问题。检查 `config.toml`、profile、项目配置和脚本是否固定旧模型；如果错误 URL 属于第三方 provider，才按该 provider 文档核对模型 ID、wire API、权限和 base URL。不要公开 `auth.json`、API Key、Session 或验证码。

中文分流表、配置检查和可复现报告清单见：[Codex Model not found、unsupported model、404 排查](https://hi-codex.com/guides/codex-model-not-found-404/?utm_source=github&utm_medium=readme&utm_campaign=codex_model_404)。

### Codex Windows 应该原生安装还是用 WSL2？

OpenAI 当前提供三条 Windows 路径：ChatGPT Windows 桌面应用、原生 Windows CLI，以及在 WSL2 中运行的 CLI。项目和工具主要位于 Windows、习惯 PowerShell 时优先原生；仓库和 Linux 工具链已经位于 WSL2 时优先 WSL2。

Windows CLI 已有官方独立安装脚本，不必把 npm 当成唯一入口。WSL1 只支持到 Codex 0.114，从 0.115 起不再受支持。Windows 与 WSL 是两个独立环境：在一侧安装的 `codex` 不会自动出现在另一侧；WSL2 项目建议放在 `~/code`，避免大型仓库长期位于 `/mnt/c`。

中文三路线对照、官方安装命令和 PATH 排查见：[2026 Codex Windows、PowerShell 与 WSL2 安装指南](https://hi-codex.com/guides/codex-windows-install-wsl/?utm_source=github&utm_medium=readme&utm_campaign=codex_windows_wsl)。

### Codex CLI 登录失败，401、403 或浏览器授权后终端没反应怎么办？

先用 `codex login status` 确认当前认证状态，再运行 `codex doctor` 检查安装、配置、认证、运行环境、Git 与终端。ChatGPT 登录和 API Key 登录是两条不同的认证与计费路径，不要混在一起排查。

- 浏览器显示授权成功、终端仍等待：在 WSL、SSH、容器或远程终端中，可改用官方设备授权命令 `codex login --device-auth`。
- 401：优先检查凭证是否过期、CLI 与浏览器是否登录了同一账号。
- 403：优先检查工作区权限、管理员强制登录方式、地区支持与账号通知；它不自动等于封号。
- 429：先查看 Codex Usage、恢复时间、Credits 或 API 限额，反复退出重登通常无效。

需要重登时，保存并脱敏错误信息后执行 `codex logout`、`codex login` 和 `codex login status`。不要把 `auth.json`、API Key、设备代码或验证码发给第三方。完整排查流程见：[Codex CLI 登录失败：401、403、OAuth 与 API Key 排查](guides/codex-cli-login-errors.md)。

### ChatGPT 验证码收不到怎么办？

先确认页面要求的是哪一种验证：邮箱 6 位 OTP、ChatGPT App 登录审批、MFA 二次验证，还是 OpenAI Platform 首个 API Key 的手机验证。它们不是同一个流程，处理方式也不同。

- 邮箱 OTP：只使用最新一封验证码，检查垃圾邮件与企业邮箱隔离区，并允许 `noreply@tm.openai.com`、`otp@tm1.openai.com`。
- App 登录审批：没有收到推送时，改用页面提供的邮箱 OTP；陌生登录请求应拒绝。
- MFA：选择 `Try another method` 使用账号已启用的其他方式；恢复码应离线保存，不能发给代充卖家。
- 手机验证：OpenAI 当前说明，普通 ChatGPT 注册和使用通常不再要求手机号；首次创建 API Key 仍可能要求短信或部分地区的 WhatsApp 验证。

无论是邮箱验证码、短信验证码、MFA 验证码还是恢复码，都不应在闲鱼聊天或公开截图中发送。详细分流表与官方资料链接见：[ChatGPT 验证码收不到：邮箱 OTP、手机验证与 MFA 排查](https://hi-codex.com/guides/chatgpt-verification-code-not-received/?utm_source=github&utm_medium=readme&utm_campaign=verification_code)。

### ChatGPT 或 Codex 账号被封、显示 Account Deactivated 怎么办？

先区分真正的账号停用与其他故障：`Account Deactivated` 通常表示 OpenAI 账号已被停用；`Unsupported Country` 属于地区支持；验证码收不到属于登录验证；已扣款仍显示 Free 属于账号或订阅渠道核对。不要把清缓存、再次充值或换号当成通用“解封”办法。

OpenAI 当前建议的申诉顺序是：

1. 收到停用通知邮件时，优先使用邮件中的专属申诉链接。
2. 无法访问通知邮件时，使用官方 [Appeal Form](https://openai.com/form/appeal/)。
3. 没有收到通知邮件但已经无法访问账号时，通过 [OpenAI Help Center](https://help.openai.com/) 聊天联系支持。

准备账号邮箱、User ID、Organization ID、使用背景、准确时间线、疑似入侵范围和已经采取的安全措施。涉及未知扣费时，只按官方表单要求提供日期、金额、卡片后四位、品牌、发卡行和账单邮编；不要把完整卡号、CVC、验证码、Session、恢复码或 API Key 发给第三方。

账号已停用时应暂停 Plus、Pro、Credits 和代充付款。详细分流表、申诉证据与 Codex / API 分支见：[ChatGPT 账号被封/停用与 Deactivated 申诉指南](https://hi-codex.com/guides/chatgpt-account-deactivated-appeal/?utm_source=github&utm_medium=readme&utm_campaign=account_deactivated)。

### ChatGPT 在中国大陆、香港或澳门能用吗？

截至 2026 年 7 月 21 日核对，OpenAI 的 ChatGPT 网页端和移动端支持国家和地区列表未列出中国大陆、香港和澳门，台湾已列出。官方同时说明，从支持列表以外的地方访问或提供 ChatGPT / API 访问，可能导致账号被封禁或暂停。

列表会变化，不能把这句话当成永久状态；使用或付款前应重新打开官方支持列表。如果人在支持地区却被误判，OpenAI 建议先清除浏览器缓存和 Cookie，再尝试其他浏览器或无痕模式，仍异常时联系官方支持。不要伪造所在地、账单地址或购买无法证明归属的账号。

官方参考：[ChatGPT 支持的国家和地区](https://help.openai.com/zh-hans-cn/articles/7947663-chatgpt-supported-countries)、[不受支持国家和地区的服务说明](https://help.openai.com/zh-hans-cn/articles/9131992-chatgpt-and-api-services-in-unsupported-countries-and-territories)。完整中文分流表见：[ChatGPT 中国大陆、香港能用吗？](https://hi-codex.com/guides/chatgpt-supported-countries-unsupported-country/?utm_source=github&utm_medium=readme&utm_campaign=supported_regions)。

### ChatGPT 提示“您的银行卡被拒绝了”怎么办？

出现 `Your card has been declined`、付款未获批准或续费交易失败时，先停止连续提交，并检查银行 App 中是否已有待处理、预授权或完成扣款。随后按顺序核对卡号、有效期、CVC、姓名和账单地址，确认余额、外币线上交易、循环订阅与 3D Secure 权限。

OpenAI 当前把常见原因归纳为银行拒付、预付卡限制、不支持的所在地或发卡地区，以及浏览器缓存等问题。资料无误仍失败时，应联系发卡行确认拒付原因；OpenAI 通常看不到银行的详细拒付代码。续费失败还要检查卡片是否过期，以及原订阅属于网页、Apple 还是 Google，不要在旧订阅状态未确认时从另一渠道重复购买。

不要伪造账单地址、使用来源不明的卡片或高频反复重试。官方参考：[为什么我的信用卡交易被拒绝？](https://help.openai.com/en/articles/7232916-why-was-my-credit-card-declined)、[为什么 Plus / Pro 续订交易失败？](https://help.openai.com/en/articles/7242622)。

中文错误提示对照与排查步骤见：[ChatGPT 您的银行卡被拒绝了怎么办？](https://hi-codex.com/guides/chatgpt-plus-payment-failed/?utm_source=github&utm_medium=readme&utm_campaign=card_declined)。

### ChatGPT Plus 已扣款但仍显示 Free 怎么办？

先不要重复付款。依次确认这笔费用来自 `chatgpt.com`、Apple App Store、Google Play 还是第三方订单，再用购买时完全相同的 Google、Apple、Microsoft 或邮箱密码方式登录原 ChatGPT 账号。

OpenAI 当前的 Plus 帮助页把这类问题单独列为 `Charged but still on Free plan`，建议核对相同验证方式；使用 Apple 登录且启用了 Hide My Email 时，还要检查 Apple 私密转发邮箱。移动端可在当前 ChatGPT App 的 Settings 中尝试 `Restore purchases`，随后退出所有设备并用正确账号重新登录。

如果提示 `subscription is associated with another account`，表示移动订阅已经关联到购买时的应用商店账号和当时登录的 ChatGPT 账号。移动订阅不能转移到另一个 ChatGPT 账号，应找回原账号，而不是在新账号或另一个平台再次购买。

仍未生效时，准备实际收款方、订单或收据、购买时间、登录方式、当前套餐截图和错误原文，向对应平台支持提交。OpenAI 的官方说明提到可提供收据或被扣卡片末四位；不要发送完整卡号、CVC、密码、验证码、Session 或恢复码。

官方参考：[What is ChatGPT Plus?](https://help.openai.com/en/articles/6950777-what-is-chatgpt-plus)、[Why is my subscription associated with another account?](https://help.openai.com/en/articles/20001056-why-am-i-seeing-a-message-that-my-subscription-is-associated-with-another-account)、[Restore an Apple App Store subscription](https://help.openai.com/en/articles/8346573-restoring-a-chatgpt-subscription-purchased-in-the-apple-app-store)。

可保存的简明步骤见：[已扣款仍显示 Free 排查清单](guides/chatgpt-paid-but-still-free.md)。中文渠道分流表与完整证据清单见：[ChatGPT Plus 已扣款仍显示 Free、付款后会员没到账排查](https://hi-codex.com/guides/chatgpt-paid-but-still-free/?utm_source=github&utm_medium=readme&utm_campaign=paid_but_still_free)。

### ChatGPT Plus 支付一直 Pending，或出现 5 美元待处理怎么办？

`Payment Pending`、银行预授权和实际扣款不是同一种状态：

| 状态 | 能证明什么 | 下一步 |
| --- | --- | --- |
| Pending / 待处理 | 交易尚未最终成功或失败 | 停止重复付款，核对 ChatGPT Billing 与银行记录 |
| Authorization hold / 预授权 | 银行暂时占用部分可用额度 | 向发卡行确认释放时间 |
| Declined / 被拒绝 | 本次交易没有获批 | 按卡片、银行验证与支持地区排查 |
| Completed / 已入账 | 已成为正式交易 | 若仍显示 Free，核对账号并联系实际收款方 |
| Reversed / 已撤销 | 原授权或交易被撤销 | 等待银行账单和可用额度同步 |

OpenAI 当前有一篇明确的“5 美元 Pending”官方说明，但它专门针对更新 **OpenAI API 按量计费付款方式**：5 美元是临时信用卡授权，会在 7 天内撤销，最终仍按实际 API 用量计费。这个规则不能直接套用到所有 ChatGPT Plus 订阅，也不能证明 Plus 已开通。

遇到 Pending 时，不要连续刷新、跨网页/Apple/Google 重复购买，也不要用虚假地址或来源不明的虚拟卡。先保存交易时间、金额、币种和商户描述，再确认原购买渠道、ChatGPT 账号状态与银行记录；只有发卡行能确认一笔记录究竟是预授权、正式入账还是正在撤销。

独立核对清单见：[ChatGPT Payment Pending、预授权与 5 美元待处理](guides/chatgpt-payment-pending.md)。更完整的中文付款状态表见：[ChatGPT 银行卡被拒与 Payment Pending 排查](https://hi-codex.com/guides/chatgpt-plus-payment-failed/?utm_source=github&utm_medium=readme&utm_campaign=payment_pending)。

### ChatGPT 充值或代充能开发票吗？

先确认实际收款方和凭证类型：

- chatgpt.com 网页自助订阅：历史 Invoice 通常从 ChatGPT Billing Portal 获取。
- Apple App Store：从 Apple 购买记录或邮件收据查询。
- Google Play：从 Google 的确认邮件和订单历史查询。
- 第三方代充：是否能开中国税务发票取决于实际服务和开票主体，付款前应书面确认类型、抬头、税号、项目名称和时间。

OpenAI Invoice、Apple / Google 收据、银行卡流水和第三方订单都可能证明交易，但不能自动当作中国增值税发票。是否符合报销要求，应由所在单位财务按开票主体、币种和制度判断。

官方参考：[How can I find my past ChatGPT invoices?](https://help.openai.com/en/articles/12356340-how-can-i-find-my-past-chatgpt-invoices)、[Updating billing information, Tax ID, and VAT ID](https://help.openai.com/en/articles/9038389-updating-billing-information-tax-id-and-vat-id)。

中文对照表见：[ChatGPT Plus / Pro 发票、Invoice 与报销凭证](https://hi-codex.com/guides/chatgpt-invoice-reimbursement/?utm_source=github&utm_medium=readme&utm_campaign=invoice_reimbursement)。

### ChatGPT 代充需要提供账号密码吗？

不应该提供 ChatGPT 密码、邮箱密码、短信或邮箱验证码、MFA 验证码和恢复码。有些流程会要求临时登录权限或 Session，但“无需密码”不等于“没有账号访问权限”：有效 Session 可能让持有者以已登录状态访问账号，应按高敏感登录凭证处理。

如果确实需要临时登录权限，应先确认用途、提交渠道、授权范围、预计使用时间和完成后的删除方式；不要在公开聊天、评价、群聊、截图或网址参数中发送。交付完成后，应检查 Active sessions；无法判断时使用 Log out of all sessions，并在怀疑密码泄露时改密和启用 MFA。

详细检查表见：[ChatGPT 代充需要密码吗？Session 安全与账号保护](https://hi-codex.com/guides/chatgpt-account-security/?utm_source=github&utm_medium=readme&utm_campaign=recharge_credentials)。

### ChatGPT Plus 可以几个人共享？

OpenAI 官方账号共享政策没有把个人 Plus 定义为家庭或多人套餐。账号只供创建它的个人使用；其他人需要使用 OpenAI 产品时，应注册自己的账号。

需要区分两个概念：

- **同一个人多设备登录**：账号创建者可以在自己的电脑、手机等多个设备使用账号，但用量限制可能受账号活动和套餐影响。
- **多个人共享一个账号**：家人、同事或陌生买家共用密码、Cookie 或 Session，不符合官方账号共享政策，也会混合聊天、文件、记忆、付款资料和潜在违规责任。

公司或团队需要多人协作时，应让成员使用各自登录身份，通过 Business 工作区分配席位，不要把一个个人 Plus 密码包装成“家庭组”或“多人席位”。

官方参考：[OpenAI Account Sharing Policy](https://help.openai.com/en/articles/10471989-openai-account-sharing-policy)。中文对比见：[ChatGPT Plus 可以几人共享？买账号、共享号风险](https://hi-codex.com/guides/chatgpt-plus-account-buy-vs-own-recharge/?utm_source=github&utm_medium=readme&utm_campaign=account_sharing)。

### ChatGPT Plus 20 美元换算人民币为什么不固定？

OpenAI 当前 Plus 帮助页写明，ChatGPT Plus 为每月 20 美元、按月计费；但 20 美元不能直接代表每张人民币银行卡的最终入账数字。更完整的计算是：结算页显示的套餐金额，加上适用税费，再加上发卡行可能收取的换汇或跨境费用。

chatgpt.com 网页订阅由 OpenAI 管理；iOS 与 Android 订阅分别由 Apple 和 Google 管理，并按对应应用商店显示的本地价格结算。三个渠道的币种、总价、收据、续费和退款入口可能不同。网上常见“固定扣 22 美元”或“固定扣 24 美元”不能替代本人付款确认页、正式收据和银行账单。

OpenAI 当前多币种说明还指出，已有订阅会继续使用原币种；要切换到新支持币种，需要先取消并在当前账期结束后重新订阅。币种支持也不等于某个国家或地区已获得 ChatGPT 服务支持，应分别核对官方列表。

完整计算表与渠道对照见：[ChatGPT Plus 人民币价格、税费与多币种说明](https://hi-codex.com/guides/chatgpt-recharge-price/?utm_source=github&utm_medium=readme&utm_campaign=price_currency_tax)。

### ChatGPT Plus 有官方年付或包年吗？

截至 2026 年 7 月 21 日，OpenAI 官方帮助中心说明个人 ChatGPT Go、Plus 和 Pro 不支持年付，也不能一次预付多个月。第三方“包年”可能是卖家未来每月代续、共享或成品账号、应用商店商品或其他服务承诺，不应直接当成 OpenAI 官方年度权益。

官方参考：[What is ChatGPT Plus?](https://help.openai.com/en/articles/6950777-what-is-chatgpt-plus)。交付形式与风险对照见：[ChatGPT Plus 年付、包年和一年费用区别](https://hi-codex.com/guides/chatgpt-plus-annual-plan/?utm_source=github&utm_medium=readme&utm_campaign=annual_plan)。

### ChatGPT Plus 包含 Codex 吗？

按当前官方 Codex 定价文档，Plus 包含 Codex 的多个使用入口。功能、模型和用量上限会调整，应以官方页面和账号内显示为准。

### 充值 ChatGPT Plus 后，API 也能用吗？

不能据此推断。ChatGPT 套餐与 OpenAI Platform API 分开计费；API Key 使用量在 Platform 账单中结算。

### Codex 用量达到上限，必须升级 Pro 吗？

不一定。先检查恢复时间、模型选择和任务规模。偶发达到上限可能通过等待恢复或优化任务解决；只有持续稳定不足时才需要比较更高档位。

### ChatGPT 代充一般多久？

人工代充没有统一到账时间。账号验证、支付渠道、人工排队和风控检查都可能影响时间。下单前应要求对方明确预计处理时间和失败退款条件。

### 购买共享号和自己的账号直充有什么区别？

自己的账号直充后，账号控制权仍属于用户；共享号可能存在多人登录、历史记录暴露、密码被改和账号回收等风险。两者不能按同一种商品比较。

## 延伸阅读

如果你需要中文的套餐选择、充值前核对项和 Codex 用量说明，可阅读 [Codex 套餐、Credits 与 API 的区别](https://hi-codex.com/guides/codex-recharge-plus-pro/?utm_source=github&utm_medium=readme&utm_campaign=codex_guide)。

## 资料来源与利益披露

本仓库优先引用 OpenAI 官方资料，来源见 [SOURCES.md](SOURCES.md)。套餐价格、额度、模型、地区可用性和产品入口都会变化，最终以 OpenAI 官方页面及用户账号内显示为准。

本资料由 Hi Codex 服务团队维护；维护者提供独立人工充值协助，因此与文中的第三方充值主题存在商业利益关系。Hi Codex 与 OpenAI 不存在隶属、代理或官方授权关系。本仓库不是 OpenAI 官方文档，也不承诺第三方充值适合所有用户。

## 许可

除另有说明外，本仓库原创内容按 [MIT License](LICENSE) 提供。引用的产品名称与外部资料权利归各自权利人所有。
