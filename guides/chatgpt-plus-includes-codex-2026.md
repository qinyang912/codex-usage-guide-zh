# ChatGPT Plus 包含 Codex 吗？是否单独付费与 Plus 够不够

> 核对日期：2026-07-22。本文只讨论 OpenAI 官方 ChatGPT 套餐与 Codex 的关系，不把第三方“激活码”、共享账号或 API 中转余额当作官方 Codex 套餐。

## 先给结论

ChatGPT Plus 包含 Codex 使用权益。你不需要在 Plus 之外再购买一份名为“Codex 会员”的独立月费；开通后，用显示 Plus 的同一个 ChatGPT 账号登录 Codex 即可。

但“包含”不等于无限使用，也不等于所有 Codex 入口都必须订阅 Plus：

- Free 可以探索快速编码任务；
- Go 面向轻量编码；
- Plus 面向每周几次聚焦的编码任务；
- Pro 提供相对 Plus 更高的 Codex 用量；
- API Key 是另一条按 Token 计费的路线，不消耗 Plus 月费。

所以真正需要回答的是三个问题：你在哪里用 Codex、当前账号是什么套餐、实际任务是否经常撞到 Usage Limit。

## ChatGPT、Plus 和 Codex 是什么关系

可以把三者理解为不同层级：

| 名称 | 它是什么 | 主要解决什么 |
| --- | --- | --- |
| ChatGPT | 产品与账号体系 | 聊天、文件、工具和多种工作入口 |
| Plus | ChatGPT 的个人付费套餐 | 更高用量与更多功能，包括一档 Codex 用量 |
| Codex | 面向软件工程的 AI 编程智能体 | 读取代码、修改文件、运行命令、测试与代码审查 |

Codex 不是“Plus 里的一个聊天模型名称”，也不是另一个必须单独续费的账号。它有网页、App、CLI、IDE 和云端工作流等入口；不同入口仍要核对登录方式、权限与地区。

## 哪些套餐能用 Codex

OpenAI 当前 Codex Pricing 的定位如下：

| 路径 | 当前定位 | 适合谁 |
| --- | --- | --- |
| Free | 快速编码任务的探索入口 | 想先验证工作流的人 |
| Go | 轻量编码任务 | 低频、预算优先 |
| Plus | 每周几次聚焦编码；网页、CLI、IDE、iOS 和云端集成 | 大多数个人开发者 |
| Pro | Plus 能力以及相对 Plus 5x 或 20x 的 Codex 用量 | 高频、长任务、多项目 |
| API Key | CLI、SDK、IDE，按 API Token 结算 | CI、自动化、独立 API 账单 |

Free 和 Go 的存在意味着“没有 Plus 就完全不能用 Codex”已经不是准确表述。Plus 的优势是更稳定的个人使用入口与更高用量，而不是把一个原本不存在的产品突然解锁出来。

## 开通 Plus 后怎么登录 Codex

先在 ChatGPT 套餐设置中确认当前账号确实显示 Plus，然后按入口操作：

1. 网页版：打开 Codex，并确认右上角账号与 Plus 账号一致。
2. App：选择使用 ChatGPT 登录，不要因为看不到入口就新建另一个账号。
3. IDE 扩展：选择 `Sign in with ChatGPT`，在浏览器授权页核对邮箱。
4. CLI：运行 `codex login` 完成浏览器授权，再运行 `codex login status` 查看当前认证方式。

如果 CLI 或 IDE 实际使用的是 API Key，那么费用进入 OpenAI Platform API 账单，不会自动使用 Plus 的包含用量。相反，购买 Plus 也不会赠送 API Key 或 Platform API Credits。

## Plus 的 Codex 额度够用吗

当相关功能对套餐可用时，Codex、ChatGPT Work、ChatGPT for Excel 和 Workspace Agents 会共享同一个 agentic usage 与 credit pool。因此，用量下降不一定只来自 CLI 或 IDE；判断前应先查看账号的 Usage 明细和可用功能。

OpenAI 对 Plus 的公开定位是“每周几次聚焦的编码任务”，但没有一个适用于所有人的固定消息数。以下因素会改变消耗：

- 代码库和需要读取的文件数量；
- 上下文、日志与输出长度；
- 模型和推理强度；
- 本地或云端运行方式；
- 测试、工具调用和失败后的返工次数。

因此，不要只看别人截图里的“还能发多少条”，也不要因一次大型重构撞限额就直接买最高档。

更稳妥的判断方法是记录 7 天：

1. 记下 Usage 页面显示的窗口、剩余量和恢复时间。
2. 记录每次被限制时正在做什么任务。
3. 先缩小目录、拆分目标、排除依赖与构建产物。
4. 偶发峰值比较等待或额外 Credits；每周稳定中断再比较 Pro。
5. 只有节省的交付时间长期高于套餐差价，升级才真正划算。

## Credits、Pro 和 API 不要混在一起

- 套餐包含用量：跟随 ChatGPT Free、Go、Plus 或 Pro，使用时优先扣减。
- ChatGPT Credits：达到套餐限制后，符合条件的账号可在 Usage 页面按需购买；余额可被账号中受支持的 agentic 功能共享，但不是 API Credits，也不能转让或兑换现金。
- Banked rate-limit reset：部分推荐活动提供的限额重置，不是 Credits；除非活动另有说明，通常应在入账后 30 天内使用。
- Pro 5x / 20x：相对 Plus 的更高 Codex 使用档位，不是 API Token 包。
- Platform API：使用 API Key 后按输入、输出等 Token 独立计费。

Plus 用量耗尽后不一定必须升级 Pro。可以先等待恢复；账号显示 Credits 入口时，也可以比较短期补充与长期升级的成本。

## 已经是 Plus，为什么 Codex 仍不可用

按顺序检查：

1. 是否登录了另一个 ChatGPT 账号或工作区；
2. `codex login status` 是否显示预期的 ChatGPT 认证；
3. 所在地区是否属于官方支持范围；
4. CLI、App 或 IDE 扩展是否需要更新；
5. 错误是套餐 Usage Limit、401/403、网络、PATH，还是 API `insufficient_quota`；
6. OpenAI 状态页是否有公开事件。

充值不能修复安装失败、PATH、OAuth 回调、网络阻断或 API 账单不足。先按完整错误原文分流，再决定是否需要套餐。

## 第三方协助开通时看什么

如果由第三方协助付款，最终仍应以自己账号内的套餐状态为准：

- 套餐必须落在自己的账号，而不是共享号；
- 下单前确认档位、总价、人工处理时间和失败退款条件；
- 密码、验证码、Cookie、Session 和 API Key 都是敏感凭证；
- 第三方付款方式本身不会额外解锁或保证 Codex；
- 完成后在 ChatGPT 套餐页和 Codex 登录状态中分别验收。

完整的中文套餐矩阵、登录步骤、Plus 用量判断和购买边界，可查看：[ChatGPT Plus 包含 Codex 吗？要单独付费吗、Plus 够不够](https://hi-codex.com/guides/codex-recharge-plus-pro/?utm_source=github&utm_medium=guide&utm_campaign=plus_includes_codex)。

## 官方资料

- [OpenAI Codex Pricing](https://learn.chatgpt.com/docs/pricing)
- [Using Codex with your ChatGPT plan](https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan)
- [Using Credits for Flexible Usage in ChatGPT](https://help.openai.com/en/articles/12642688)
- [Codex Referral Promotions](https://help.openai.com/en/articles/20001271-codex-referral-promotions)
- [Codex rate card](https://help.openai.com/en/articles/20001106-codex-rate-card)
- [Codex Authentication](https://learn.chatgpt.com/docs/auth)
- [ChatGPT and Platform billing are separate](https://help.openai.com/en/articles/9039756-billing-settings-in-chatgpt-vs-platform)

## 内容与利益关系

本文由 Hi Codex 服务团队整理。团队提供独立的 ChatGPT Plus / Pro 人工充值协助，因此与套餐购买主题存在商业利益关系；团队与 OpenAI、GitHub 或闲鱼不存在隶属、代理或官方授权关系。套餐、价格、地区、用量与 Credits 规则可能调整，最终以用户账号中的 Pricing、Usage 和 Billing 页面为准。
