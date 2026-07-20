# Codex Error running remote compact task 与上下文压缩排查

> 核对日期：2026-07-21。客户端、模型、命令和服务端行为会变化，请以当前 OpenAI 官方文档、实际日志和账号内显示为准。

Codex 长任务出现 <code>Automatically compacting context</code>、<code>/compact</code> 失败，或提示 <code>Error running remote compact task</code> 时，不要只根据总提示判断根因。真正决定处理路线的是后面的错误文字。

## 先看完整错误后缀

| 错误关键词 | 更可能发生在哪一层 | 优先动作 |
| --- | --- | --- |
| <code>stream disconnected before completion</code> | 压缩请求的响应流中断 | 查状态、网络、TLS、provider 和版本 |
| <code>unexpected status 404 Not Found</code> | 模型、端点、路由或第三方路径不匹配 | 记录完整 URL、模型、认证方式和 provider |
| <code>429 Too Many Requests</code> | Codex Usage、API 限流/账单或第三方上游 | 停止并发重试，检查恢复时间与 request ID |
| <code>input exceeds the context window</code> | 当前请求超过模型上下文窗口 | 减少输入，保存状态后压缩或迁移会话 |
| <code>timeout waiting for child process to exit</code> | 本地命令或子进程未及时退出 | 保存工作，记录相关进程和超时前操作 |
| <code>expected exactly one compaction output item</code> | 压缩输出结构异常 | 保存版本、request ID 和复现，提交反馈 |

同一句 <code>Error running remote compact task</code> 可以对应多类故障。不要把它们全部归因于“网络不好”“额度不足”或“上下文太长”。

## 上下文压缩是什么

OpenAI 对原生压缩的说明是：长任务接近上下文窗口时，系统分析先前的会话状态，生成节省 Token 的压缩项，并让后续窗口继续携带关键状态和高价值信息。Codex 使用这一机制维持跨窗口的多步骤任务。

当前 Slash commands 文档列出：

- <code>/status</code>：显示聊天 ID、上下文使用和速率限制；
- <code>/compact</code>：压缩当前聊天的上下文；
- <code>/fork</code>：把本地聊天复制到新的本地聊天或 worktree；
- <code>/feedback</code>：打开反馈流程并可选择附带日志。

可用命令会因客户端、环境和访问权限不同而变化，应以当前客户端输入斜杠后显示的命令列表为准。

压缩不是删除聊天，也不是重置套餐额度。它会重新表示需要继续工作的状态，因此关键决策、接口约束、文件路径、部署版本、测试结果和下一步不应只保存在聊天记录中。

## 出错后先保住工作

1. 停止继续发送大段日志、图片或重复消息。
2. 把目标、已经完成的改动、关键结论、验证结果和下一步写入项目文件或 issue。
3. 检查工作树，适合提交时创建清晰 commit；不适合提交时至少保存补丁和未提交文件清单。
4. 记录 Codex 与系统版本、客户端类型、发生时间、完整错误、request ID、认证方式、模型和 provider。
5. 新建一个最小任务，确认只有当前长会话失败，还是所有会话、所有客户端都失败。

如果新会话正常、原长会话稳定失败，更偏向当前会话状态或压缩路径；如果所有任务都失败，应先检查服务状态、账号、网络和配置。

## stream disconnected 分支

压缩请求在完成前断流时：

1. 查看 [OpenAI Status](https://status.openai.com/)，记录同一时间是否有 Codex、ChatGPT 或 API 事件。
2. 比较同账号的新最小会话，以及 App、CLI、IDE 是否都失败。
3. 确认使用 ChatGPT 登录、API Key 还是自定义 provider，记录错误中的实际端点。
4. 企业设备检查 DNS、TLS 检查、防火墙与代理策略，不要关闭证书校验。
5. 更新客户端后仍复现时，保存版本和 request ID，用 <code>/feedback</code> 提交脱敏日志。

普通短回答正常，也不能证明压缩请求的流式路径一定正常，因为请求规模、持续时间和路径可能不同。

## 404 分支

404 的完整 URL 很关键。ChatGPT 登录后端、OpenAI API 和第三方 provider 并不共享完全相同的模型清单和路由。

依次核对：

- 当前选择的模型是否仍受账号和客户端支持；
- 使用的是 ChatGPT 登录还是 API Key；
- 是否设置了自定义 provider 或 base URL；
- 项目配置是否覆盖用户配置；
- 第三方 endpoint 是否需要不同路径或部署名；
- 同时间是否有公开后端事件。

404 表示已经取得明确 HTTP 响应，不等于 DNS 失败。不要只凭一条 404 重装整个客户端或删除认证文件。

## 429 分支

ChatGPT 登录时查看 Codex Usage 与恢复时间；API Key 模式需要检查 RPM、TPM、项目预算、Billing 与模型共享限制；第三方 provider 还要查它自己的上游限流。

API 账户有余额也不能排除速率限制。失败请求和并发重试可能继续占用速率窗口，因此应停止重试风暴，保存 request ID、响应头和首次错误。

只有错误明确属于 Codex Usage、套餐资格或 Credits 时，升级套餐才相关。404、断流、TLS、provider 和压缩实现故障不会因为重复充值自动修复。

## context window 超限

不要照搬第三方文章中的固定“258K”“1M 上下文”或实验开关。模型、客户端和访问资格会变化，应以当前官方模型资料、<code>/model</code> 和 <code>/status</code> 为准。

更稳妥的做法：

- 大日志保存在文件中，让 Codex 按关键词和范围读取；
- 排除依赖、构建产物、缓存和二进制文件；
- 每个阶段记录目标、事实、改动、验证、风险和下一步；
- 将超大任务拆为可独立验证的单元；
- 在接近限制前主动整理和压缩，不等到输入已被拒绝。

更大的窗口仍是有限窗口，也不意味着永远不需要压缩。

## 卡住、timeout 与安全恢复

先确认是否仍有命令、工具或子进程运行。强制退出可能留下不完整文件。

推荐顺序：

1. 保存工作树和诊断信息；
2. 新建最小任务对照；
3. 记录仍在运行的命令和完整超时文字；
4. 更新客户端后一次只改变一个变量复测；
5. 当前会话仍可操作时尝试 <code>/compact</code>；
6. 工作已保存且当前会话反复失败时，可用 <code>/fork</code> 做隔离恢复；
7. 稳定复现时用 <code>/feedback</code> 提交脱敏材料。

第三方会话恢复工具、批量删除缓存目录和直接编辑 Session JSON 可能更改本地状态、读取项目内容或接触凭证，不应作为通用第一步。必须使用第三方工具时，应先审查源码、权限、数据流和备份方式，并在不含机密的复制环境验证。

## 反馈材料

建议提供：

- 发生时间和时区；
- Codex、App、CLI 或 IDE 版本；
- 操作系统与 Local、WSL、SSH 或容器运行侧；
- ChatGPT 或 API Key 认证方式，不填写真实密钥；
- 默认或自定义 provider 与脱敏域名；
- 从第一行到最后一行的完整错误；
- request ID；
- 当前会话是否包含大量图片、日志或工具输出；
- 只影响当前会话还是所有会话；
- 最小复现步骤。

不要公开 <code>auth.json</code>、API Key、Session、Cookie、MFA、设备代码、完整本地路径或项目机密。

## 官方资料

- [Codex Slash commands](https://developers.openai.com/codex/reference/slash-commands)
- [OpenAI：当上下文窗口填满时自动压缩](https://openai.com/zh-Hans-CN/index/equip-responses-api-computer-environment/)
- [OpenAI Status](https://status.openai.com/)
- [openai/codex 公开故障记录 #13784](https://github.com/openai/codex/issues/13784)
- [openai/codex 公开故障记录 #15468](https://github.com/openai/codex/issues/15468)

更完整的中文错误矩阵、上下文保护清单与相邻专题入口见：[Hi Codex 上下文压缩排查](https://hi-codex.com/guides/codex-context-compaction-remote-compact-task-error/?utm_source=github&utm_medium=guide&utm_campaign=codex_context_compaction)。

## 利益关系与安全说明

本资料由 Hi Codex 服务团队维护；维护者提供独立人工充值协助，因此与套餐主题存在商业利益关系。Hi Codex 与 OpenAI 不存在隶属、代理或官方授权关系。

本文不提供地区绕过、第三方 API 中转、共享凭证、限流绕过、关闭 TLS 校验或会话文件篡改。
