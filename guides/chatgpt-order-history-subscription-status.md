# ChatGPT 充值记录、订单号和余额在哪里看：订阅状态安全核对清单

很多人把“银行卡扣款”“商家订单完成”“ChatGPT 显示 Plus / Pro”和“还有多少额度”当成同一件事。它们其实来自不同系统，任何一个单独出现，都不一定能证明会员已经正确落到当前账号。

先记住两个结论：

1. ChatGPT 没有一个能汇总网页、Apple、Google Play 和第三方订单的通用“充值记录”页面。
2. 查询套餐和订阅状态不需要把 Session、access token、Cookie、验证码或 API Key 粘贴到第三方工具。

## 先按实际付款渠道查订单

| 实际付款渠道 | 订单或记录在哪里看 | 还要核对什么 |
| --- | --- | --- |
| `chatgpt.com` 网页 | ChatGPT 设置中的 Account / Payment / Manage，再看 Invoice History | 登录账号、套餐、金额、日期、下一账单信息 |
| Apple App Store | Apple 购买记录、订阅管理和邮件收据 | 付款 Apple ID、购买时登录的 ChatGPT 账号 |
| Google Play | Play 商店的付款和订阅、预算和订单记录 | 付款 Google 账号、订单编号、关联的 ChatGPT 账号 |
| 闲鱼或其他第三方人工订单 | 原平台订单详情、付款记录和聊天交付记录 | 商品套餐、成交价、完成时间、退款或售后约定 |

不同渠道的订单号不能互相查询。OpenAI Invoice 编号不是 Apple 或 Google 的订单编号，闲鱼订单号也不能拿到 ChatGPT 账单门户查询。

## 一笔交易至少有四层证据

从弱到强可以这样判断：

| 你看到的证据 | 它能证明什么 | 不能证明什么 |
| --- | --- | --- |
| 银行卡 Pending / 预授权 | 银行收到请求或暂时占用额度 | 不能证明订单最终入账，更不能证明会员生效 |
| OpenAI、Apple、Google 或第三方订单 | 原渠道建立了一笔交易 | 不能单独证明当前 ChatGPT 账号已经显示套餐 |
| 原渠道显示订阅有效或订单完成 | 收款平台认可订单状态 | 仍需确认关联的是哪个 ChatGPT 账号 |
| ChatGPT 设置显示目标套餐和日期 | 当前登录账号识别到 Plus / Pro | 还应保存原订单，便于处理后续退款或掉订阅 |

人工订单尤其要同时满足最后两层：平台内订单已按约定交付，并且用户自己的 ChatGPT 账号显示正确套餐。商家回复“已完成”不等于账号一定到账。

## 网页订阅怎样查历史 Invoice

OpenAI 当前帮助页给出的 Plus / Pro 路径是进入设置中的 Account，再从 Payment 选择 Manage，在客户账单门户的 Invoice History 下载过去的发票。如果列表为空，先确认登录的是正确 ChatGPT 账号或工作区。

Apple App Store 和 Google Play 发起的订阅不会出现在这里，它们的收据应回到对应应用商店查询。Business 工作区也不是个人 Plus / Pro 的同一入口，通常由工作区所有者在 Billing / Invoices 中查看。

如果网页中入口名称略有变化，以当前账号实际显示为准；不要因为找不到某个旧版按钮就直接再买一次。

## Apple 或 Google 已扣款，为什么 ChatGPT 仍是 Free

移动订阅同时涉及两个身份：

- Apple ID 或 Google 账号负责付款、续费、取消和收据；
- 购买时登录的 ChatGPT 账号接收套餐权益。

两者任意一边登录错账号，都可能出现“应用商店有订单，但当前 ChatGPT 仍是 Free”。这时应先找回原账号，并在官方 ChatGPT App 的设置中使用页面提供的 Restore purchases。不要在网页、Apple 和 Google 三个渠道连续补买，否则可能形成多份独立订阅。

## 第三方订单状态应该怎样理解

第三方商家通常会显示待支付、已支付、处理中、已完成、退款中或已退款。这些都是该商家的业务状态，不是 OpenAI 的官方订阅状态。

- **已支付**：通常只说明付款完成，尚未证明交付完成。
- **处理中**：应按下单前约定的时间等待，并保留原订单沟通。
- **已完成**：还要回到 ChatGPT 设置验证套餐和日期。
- **退款中 / 已退款**：继续在原支付渠道查看入账，不应把已退款订单当成有效订阅。

忘记第三方订单号时，只能使用商家正式提供的查询方式或回到原平台账号查找。不要向陌生人提供手机号、身份证、完整卡号或登录凭证来“找回订单”。

## “余额”可能指四种完全不同的东西

### Plus / Pro 订阅

Plus 和 Pro 是按周期订阅，不是一个可以查看剩余人民币的储值钱包。判断是否有效，应看套餐名称、订阅管理和日期。

### ChatGPT Credits

符合条件的账号可能在 ChatGPT Usage 页面看到可用于 Codex 等功能的 Credits。是否可购买、余额和适用范围以该账号 Usage 页面为准。

### OpenAI Platform API Credits

使用 API Key 调用模型时，应查看 OpenAI Platform 的 Usage 与 Billing。ChatGPT Plus / Pro 不会自动抵扣 API 账单，API Credits 也不会自动变成个人套餐。

### Apple 或 Google 账户余额

应用商店余额属于 Apple 或 Google 支付体系。它是否能用于某次订阅取决于地区、账号、税费和商店规则，不能因为商店有余额就推断 ChatGPT 已经开通。

## 为什么不应该用 Session 查询套餐

有些页面要求用户复制 `chatgpt.com/api/auth/session` 返回的整段内容，再粘贴到所谓订阅查询工具。即使页面声称“只在本地解析”，Session JSON 仍可能包含 access token 或其他登录信息。

安全做法是使用：

- ChatGPT 官方设置与账单入口；
- 原付款渠道的订单和收据；
- 已遮盖敏感信息的套餐截图；
- 原平台内的售后沟通。

不要把 Session、Cookie、access token、邮箱验证码、MFA 恢复码、完整银行卡信息或 API Key 发送给商家，也不要粘贴到不完全信任的网页。

## 查不到记录时按这个顺序排查

1. 确认 ChatGPT 登录方式与购买时完全相同，包括 Google、Apple、Microsoft 或邮箱密码。
2. 检查 Apple Hide My Email 是否使用了私密转发邮箱。
3. 根据银行卡账单描述或邮件收据确认实际收款方。
4. 回到该收款渠道查询订单，不在错误平台反复搜索。
5. 只有 Pending / 预授权时先不要重复付款。
6. 有正式订单但仍显示 Free 时，保存订单号、金额、时间、登录方式和当前套餐截图。
7. 向实际收款方或对应官方支持提交非敏感证据。

## 付款后建议保存的最小证据

- 购买渠道和实际收款方；
- 订单号或 Invoice 编号；
- 套餐名称、金额和付款时间；
- 当前 ChatGPT 登录方式；
- 套餐页面与下一账单或到期信息；
- 第三方订单的交付、退款和售后约定。

截图前隐藏完整卡号、CVC、地址、验证码、Session 和 access token。证据的目的是帮助定位订单，不是把账号控制权交给客服或卖家。

## 官方参考

- [OpenAI：如何查找过去的 ChatGPT 发票](https://help.openai.com/zh-hans-cn/articles/12356340-how-can-i-find-my-past-chatgpt-invoices)
- [OpenAI：管理 ChatGPT 网页端和平台的账单设置](https://help.openai.com/zh-hans-cn/articles/9039756-%E7%AE%A1%E7%90%86-chatgpt-%E7%BD%91%E9%A1%B5%E7%AB%AF%E5%92%8C%E5%B9%B3%E5%8F%B0%E7%9A%84%E8%B4%A6%E5%8D%95%E8%AE%BE%E7%BD%AE)
- [OpenAI：避免网页、iOS、Android 重复订阅](https://help.openai.com/en/articles/20001043-how-do-i-avoid-being-charged-twice-if-i-subscribe-to-chatgpt-on-ios-android-and-the-web)
- [OpenAI：在 ChatGPT 中使用 Credits](https://help.openai.com/zh-hans-cn/articles/12642688-using-credits-for-flexible-usage-in-chatgpt-freegopluspro-sora)

需要按网页、Apple、Google Play 和闲鱼逐项对照，可继续阅读：[Hi Codex：ChatGPT 充值记录、订单号和订阅状态查询](https://hi-codex.com/guides/chatgpt-plus-subscription-check/?utm_source=github&utm_medium=guide&utm_campaign=chatgpt_order_status)。

## 利益披露与安全边界

本文由 Hi Codex 服务团队维护；维护者提供独立人工充值协助，因此与 ChatGPT 套餐和第三方订单主题存在商业利益关系。Hi Codex 与 OpenAI、Apple、Google 或闲鱼不存在隶属、代理或官方授权关系。

本文不提供绕过地区限制、伪造订单、共享账号、窃取 Session、规避付款审核或获取他人订阅信息的方法。界面名称和规则可能变化，最终以用户账号内页面、原付款渠道和官方帮助文档为准。
