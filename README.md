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

安装、登录和套餐是三个不同阶段。`codex: command not found` 应先检查安装与 PATH；401/403 更接近认证或工作区；Usage Limit 才需要查看套餐用量、恢复时间和 Credits。

## Plus、Pro、Credits 与 API 的区别

### ChatGPT Plus

OpenAI 当前 Codex 定价文档将 Plus 列为个人套餐之一，并说明它包含 Codex 的多个使用入口。具体价格、功能和地区可用性可能变化，购买前应以账号内页面和官方定价页为准。

### ChatGPT Pro 5x / 20x

官方文档把 Pro 描述为相对于 Plus 提供更高的 Codex rate limits，并提供 5x 与 20x 档位。这里的倍数表示 Codex 使用上限级别，不等于 API Token 余额，也不是“速度永久提高 5 倍或 20 倍”。

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

## 礼品卡、促销码和第三方激活码不是一回事

Google 搜索结果常把几种不同的“码”混在一起：

- **Apple Gift Card**：先兑换为 Apple 账户余额，再由 Apple 渠道购买符合条件的订阅；它不是 ChatGPT 专属礼品卡。
- **OpenAI 促销或推荐码**：通常带有活动对象、地区、有效期、新老用户和付款方式限制，不应默认可以转卖或适用于所有账号。
- **第三方激活码**：可能只是第三方平台自己的订单凭证、代付入口或授权链接，不自动等于 OpenAI 官方代码。
- **商家优惠码**：通常只用于确认活动价格，不能直接在 ChatGPT、Apple 或 Google 页面激活套餐。

判断时先确认发行方、兑换域名、账号归属、需要的权限、自动续费渠道和失败退款条件。不要在陌生页面提交密码、验证码、Session、Cookie 或 API Key。

官方参考：[OpenAI Plus promotions and referrals](https://help.openai.com/en/articles/10492689-chatgpt-plus-promotions-and-referrals)、[Apple 兑换礼品卡说明](https://support.apple.com/en-us/118242)。

更完整的中文对照表见：[ChatGPT Plus 礼品卡、兑换码与激活码区别](https://hi-codex.com/guides/chatgpt-plus-gift-card-promo-code/?utm_source=github&utm_medium=readme&utm_campaign=gift_card_code)。

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

- [ ] 套餐落在自己的账号，而不是共享账号。
- [ ] 已确认 Plus、Pro 5x 或 Pro 20x 的具体档位。
- [ ] 已确认页面价格、聊天优惠和最终支付价。
- [ ] 已确认预计到账时间及失败退款条件。
- [ ] 不发送 Session、Cookie、恢复码或长期有效令牌。
- [ ] 不相信“永久不封”“官方授权”等无法核验的承诺。
- [ ] 完成后在 ChatGPT 账号内核对套餐状态与下一次续费信息。
- [ ] 如果曾提供临时登录协助，完成后更新密码并启用 MFA。

需要先比较官方网页、App Store、Google Play 与人工协助，可查看 [ChatGPT 会员购买指南：Plus、Pro 与购买渠道怎么选](guides/chatgpt-membership-buy-plus-pro.md)。

## 常见问题

### ChatGPT 提示“您的银行卡被拒绝了”怎么办？

出现 `Your card has been declined`、付款未获批准或续费交易失败时，先停止连续提交，并检查银行 App 中是否已有待处理、预授权或完成扣款。随后按顺序核对卡号、有效期、CVC、姓名和账单地址，确认余额、外币线上交易、循环订阅与 3D Secure 权限。

OpenAI 当前把常见原因归纳为银行拒付、预付卡限制、不支持的所在地或发卡地区，以及浏览器缓存等问题。资料无误仍失败时，应联系发卡行确认拒付原因；OpenAI 通常看不到银行的详细拒付代码。续费失败还要检查卡片是否过期，以及原订阅属于网页、Apple 还是 Google，不要在旧订阅状态未确认时从另一渠道重复购买。

不要伪造账单地址、使用来源不明的卡片或高频反复重试。官方参考：[为什么我的信用卡交易被拒绝？](https://help.openai.com/en/articles/7232916-why-was-my-credit-card-declined)、[为什么 Plus / Pro 续订交易失败？](https://help.openai.com/en/articles/7242622)。

中文错误提示对照与排查步骤见：[ChatGPT 您的银行卡被拒绝了怎么办？](https://hi-codex.com/guides/chatgpt-plus-payment-failed/?utm_source=github&utm_medium=readme&utm_campaign=card_declined)。

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
