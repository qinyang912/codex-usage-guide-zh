# Codex 用量怎么看？先分清剩余额度、使用记录与恢复时间

资料核对：2026-09-25。本文根据官方文档整理，未操作读者账号，也没有执行付费测试。

想知道任务还能不能继续，先打开 [Codex Usage Dashboard](https://chatgpt.com/codex/settings/usage)。在 CLI 会话中可以输入 `/status` 查看当前状态和剩余限制；想回看账号的 Token 活动，则查看客户端提供的 `/usage`。已经用掉多少 Token 和还能继续多久，不是同一个数字。

## 选对查询入口

| 想确认的内容 | 查询位置 | 不要混淆什么 |
| --- | --- | --- |
| 套餐剩余用量、恢复时间 | 官方 Usage Dashboard | 短时限制、周限额与余额是不同项目 |
| CLI 当前会话的状态 | 会话中输入 `/status` | 上下文容量不等于套餐余额 |
| 按日、按周或累计的 Token 活动 | 支持时输入 `/usage daily`、`/usage weekly` 或 `/usage cumulative` | 历史消耗不能直接换算成剩余消息数 |
| API Key 产生的用量 | OpenAI Platform 的 Usage / Billing | API 账单不属于 ChatGPT 套餐 |

官方资料：[用量与查询入口](https://learn.chatgpt.com/docs/pricing#where-can-i-see-my-current-usage-limits)、[CLI 命令](https://learn.chatgpt.com/docs/developer-commands#view-account-usage-with-usage)。当前文档说明，`/usage` 需要 Codex service account auth；出现登录要求时，不能据此认定账号余额为零。客户端没有该命令时，可先用网页入口核对。

## 限额到了，先看恢复的是哪一项

官方定价页列出按模型估算的五小时用量范围，同时说明可能还有周限额。任务大小、模型、上下文和运行方式都会影响消耗，不能用别人一次对话的条数推算自己的额度。

查看时记下当前账号、限制项目和页面显示的恢复时间。某个短时窗口恢复，不代表所有限制都已恢复；新建会话、刷新网页也不是重置套餐用量的操作。

如果页面显示仍有余额，但任务报的是登录失败、网络断开或模型不可用，应按错误处理。不要因为所有错误都出现在 Codex 里，就把它们归为缺少额度。

## 需要花钱之前，留下可比较的记录

可以自己记录“任务 / 模型 / 哪个限制触发 / 等待多久 / 是否补充额度”。这是一种判断方法，不是本站用户的实测记录。

偶发中断且能够等待，先等页面显示的恢复时间；短期项目需要继续，可核对额外 Credits；持续多个周期不足，再比较更高套餐。详细选择见[等待、Credits 还是 Pro](codex-credits-vs-pro.md)。

网页版提供更完整的限制说明与相关排错入口：[Codex 用量查询](https://hi-codex.com/guides/codex-usage-check/?utm_source=github&utm_medium=guide&utm_campaign=codex_usage_check)。

查询用量不需要把 `auth.json`、Session 或 API Key 发给第三方。分享截图时遮住邮箱、工作区和订单信息。

## 维护关系

本文由 Hi Codex 服务团队维护，含指向自有网站的链接。团队提供独立充值协助，与 OpenAI 无隶属或官方授权关系；充值不是技术故障的通用解决办法。
