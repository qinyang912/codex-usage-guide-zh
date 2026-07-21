# ChatGPT Payment Pending、预授权与 5 美元待处理核对清单

> 更新日期：2026-07-21。本文用于区分 ChatGPT 订阅付款、OpenAI API Billing、Apple / Google 订单和银行卡状态，不提供换区、虚假账单地址、来源不明虚拟卡或绕过支付风控的方法。

## 先不要再次付款

ChatGPT 页面显示 `Payment Pending`，或银行 App 出现“待处理”“预授权”“处理中”时，最重要的动作是停止重复提交。网页、Apple App Store 和 Google Play 可以形成彼此独立的订阅；原交易尚未确认时跨渠道再买，可能产生多笔预授权或真正的重复扣费。

先保存以下信息：

- ChatGPT 或应用商店显示的完整提示与时间；
- 银行交易的金额、币种、商户描述和状态；
- 实际购买渠道：chatgpt.com、Apple、Google Play 或第三方订单；
- 当前登录的 ChatGPT 账号与登录方式；
- 是否生成订阅、收据或 Billing 记录。

截图时隐藏完整卡号、CVC、验证码、密码、Session 和恢复码。

## 五种状态不要混为一谈

| 状态 | 通常含义 | 安全处理 |
| --- | --- | --- |
| Payment Pending / 待处理 | 交易尚未最终成功或失败 | 不重复购买，核对 Billing 和银行记录 |
| Authorization hold / 预授权 | 银行暂时占用可用额度 | 联系发卡行确认是否会释放及预计时间 |
| Declined / 被拒绝 | 交易没有获批 | 检查卡片资料、银行权限、3D Secure 与支持地区 |
| Completed / 已入账 | 已成为正式交易 | 权益未到账时核对原账号并联系实际收款方 |
| Reversed / 已撤销 | 原授权或交易被撤销 | 等待账单与可用额度同步 |

商户页面和银行页面可能暂时不同步。OpenAI 没有为所有 ChatGPT Plus 交易公布统一的 Pending 时长；不同发卡行、Apple、Google 和网页账单不能套用同一个固定天数。

## 银行卡出现 5 美元 Pending，先确认是不是 API Billing

OpenAI 官方当前说明：更新 OpenAI API 按量计费付款方式后，可能出现 5 美元 Pending。这是临时信用卡授权，会在 7 天内撤销；API 最终按照实际用量在账单周期结算。

这个说明有三个边界：

1. 它针对 OpenAI Platform API 的 pay-as-you-go 付款方式；
2. 它不是 ChatGPT Plus 的月费价格；
3. 它不能证明 Plus、Pro 或 Codex 套餐已经开通。

如果你从未进入 API Billing，却看到 5 美元记录，应核对商户描述、账号 Billing 和银行卡详情；无法确认时直接向发卡行询问该笔交易的类型和来源。

## 按这个顺序处理

1. **检查 ChatGPT Billing 或原应用商店订单。** 看是否生成有效订阅、收据、待支付账单或取消记录。
2. **确认登录的是购买时的 ChatGPT 账号。** Google、Apple、Microsoft 与邮箱密码可能进入不同账号；移动端订阅不能随意转移到另一个 ChatGPT 账号。
3. **向发卡行确认交易状态。** 提供发生时间、金额、币种和商户描述，请银行区分预授权、正式入账或撤销中。
4. **已入账但权益未生效时联系实际收款方。** 网页订单找 OpenAI，Apple / Google 订单回对应商店，第三方服务回原担保订单。
5. **只有在原交易明确失败、撤销或退款后再决定是否重试。** 重试前确认原订阅不会恢复扣款。

## 不采用这些做法

- 不引用“所有 Pending 都在 3–7 个工作日释放”之类的统一承诺；
- 不使用无法核验来源的固定成功率；
- 不伪造账单地区、使用卖家提供的地址或接码信息；
- 不购买来源不明的虚拟卡、共享账号或所谓预授权清除服务；
- 不向第三方发送完整付款资料、验证码或账号恢复材料。

## 官方资料

- [OpenAI：更新 API 付款方式后的 5 美元临时授权](https://help.openai.com/en/articles/7438062-after-updating-my-api-payment-method-i-noticed-a-5-charge-is-this-a-temporary-hold)
- [OpenAI：未授权 ChatGPT 或 API Credits 扣费处理](https://help.openai.com/en/articles/7242625-unauthorized-chatgpt-or-api-credit-purchase-charges-how-to-request-a-refund)
- [OpenAI：银行卡为何被拒绝](https://help.openai.com/en/articles/7232916-why-was-my-credit-card-declined)

更完整的中文错误原文、3D Secure、续费失败和支持材料清单见：[ChatGPT 银行卡被拒、Payment Pending 与付款未获批准](https://hi-codex.com/guides/chatgpt-plus-payment-failed/?utm_source=github&utm_medium=guide&utm_campaign=payment_pending)。

## 利益披露

本资料由 Hi Codex 服务团队维护；维护者提供独立人工充值协助，因此与第三方充值主题存在商业利益关系。Hi Codex 与 OpenAI、Apple、Google、发卡银行或闲鱼官方不存在隶属、代理或授权关系。最终支付状态以用户账号、实际收款平台与发卡行记录为准。
