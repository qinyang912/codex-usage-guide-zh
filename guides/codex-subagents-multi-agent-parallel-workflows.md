# 2026 Codex Subagents 多代理教程：并行任务、配置与避坑

> 核对日期：2026-07-27。本文依据 OpenAI 当前 Subagents、Codex 配置、最佳实践与安全文档整理。

Codex Subagents 可以把任务拆给多个子代理同时处理，再由主代理汇总结果。比如，让不同代理分别检查代码、运行独立测试或核对资料。不过，并行不一定更快：每个代理都会消耗用量；分工不清时，还可能重复工作、改到同一个文件，增加后续检查的负担。

## 30 秒结论

- 当前 Codex 版本默认启用 Subagents；需要并行时，直接在 Prompt 中明确要求“使用子代理”或“并行代理”。
- App、CLI 和 IDE 扩展都能显示子代理活动；CLI 可用 `/agent` 查看和切换代理线程。
- 第一轮从 2～3 个互相独立、以读取为主的子任务开始，不要一上来让多个代理同时改同一批文件。
- 子代理继承当前会话的权限边界。并行不会自动获得更高权限，也不能绕过 Sandbox 或审批。
- 并行任务通常消耗更多 Token。应比较节省的等待时间、发现的问题和最终审查成本，而不是只看代理数量。

## 什么任务适合并行

| 任务 | 推荐拆法 | 原因 |
| --- | --- | --- |
| 大型代码库摸底 | 架构、数据流、测试各一个代理 | 搜索范围独立，原始日志不必挤进主线程 |
| Pull Request Review | 正确性、安全、测试缺口各一个代理 | 关注点清楚，结果容易按类别汇总 |
| 多平台故障排查 | macOS、Windows、Linux 各一个代理 | 环境证据可以分别收集 |
| 文档或 API 核对 | 每组来源或章节一个代理 | 适合并行查证，再统一去重 |
| 相互独立的测试 | 单元、集成、端到端分别运行 | 等待时间可以重叠 |

以下任务通常先用一个主代理更稳：

- 根因尚未确定、每一步依赖上一步结果的线性故障；
- 只改一两个文件的小修复；
- 多个任务必须同时编辑同一个核心文件；
- 涉及删除、发布、付款、发送消息或权限变更的高风险操作；
- 无法写清每个子任务的输入、边界和验收标准。

## 一条可直接复制的并行 Prompt

一个稳定的 Subagents Prompt 至少写清四件事：

1. **总目标**：最终要回答或交付什么。
2. **拆分方式**：每个代理负责哪一块。
3. **工作边界**：只读还是可修改，哪些文件不能碰。
4. **汇总要求**：是否等待全部完成，按什么格式合并和去重。

例如：

```text
请用 3 个并行子代理 Review 当前分支相对 main 的改动：

1. 一个代理检查正确性和潜在回归；
2. 一个代理检查安全与权限边界；
3. 一个代理检查测试缺口和文档漂移。

三个代理都只读，不修改文件。请等待全部完成后再汇总，
按严重程度去重，只报告有文件和行号证据的问题；
如果某一类没有发现问题，明确写“未发现”。
```

不要只写“多开几个代理帮我看看”。任务、范围和输出不明确时，多个代理往往会重复搜索同一件事。

## App、CLI 与 IDE 中怎么看

当前 Codex 的三个本地入口都支持子代理工作流：

- **ChatGPT 桌面 App**：主任务中会显示子代理线程，可打开查看过程和返回结果。
- **Codex CLI**：运行中可使用 `/agent` 查看或切换活跃代理线程；审批请求可能来自当前没有打开的线程。
- **IDE 扩展**：支持该界面时，输入框上方会出现后台代理面板，可查看状态、停止活跃代理或打开单个线程。

如果希望 Codex 使用 Subagents，应在本轮 Prompt 中直接提出。项目的 `AGENTS.md` 或被调用的 Skill 也可以要求特定工作流使用并行代理，但只适用于一次任务的偏好不必长期写进仓库规则。

## 用 config.toml 控制并行规模

个人默认配置可以放在 `~/.codex/config.toml`；项目级配置可放在受信任仓库的 `.codex/config.toml`。下面是一个保守起点：

```toml
[agents]
enabled = true
max_concurrent_threads_per_session = 4
default_subagent_reasoning_effort = "medium"
```

字段含义：

- `enabled`：是否启用多代理工具；当前默认值为 `true`。
- `max_concurrent_threads_per_session`：限制一个会话同时打开的子代理线程数，不包含主线程。
- `default_subagent_model`：需要时为子代理设置默认模型。
- `default_subagent_reasoning_effort`：设置默认推理强度。

不要为了“越多越快”盲目提高线程数。仓库规模、测试资源、API 限流、文件冲突和审查能力都会形成新的瓶颈。先用 2～3 个任务记录实际耗时和用量，再决定是否扩大。

## 创建一个只读 Review 代理

Codex 自带通用、执行和探索类代理。需要稳定复用团队角色时，可以在个人的 `~/.codex/agents/` 或项目的 `.codex/agents/` 下添加独立 TOML 文件。

例如 `.codex/agents/reviewer.toml`：

```toml
name = "reviewer"
description = "检查正确性、安全风险、行为回归和缺失测试的只读 Reviewer。"
sandbox_mode = "read-only"
model_reasoning_effort = "high"
developer_instructions = """
像代码所有者一样 Review。
优先报告正确性、安全、行为回归和测试缺口。
每个结论必须给出文件、位置和可复现证据。
不要修改文件，不报告纯风格偏好。
"""
```

自定义代理至少需要：

- `name`
- `description`
- `developer_instructions`

模型、推理强度、Sandbox、MCP 和 Skills 等设置按需添加。角色的职责越具体，主代理越容易判断何时调用它，你也更容易比较每次运行的结果。

## 权限、文件冲突与安全边界

子代理会继承父会话的 Sandbox 和当前运行时权限选择。自定义代理可以进一步设为 `read-only`，但不应假设它能越过父会话的限制。

并行写入前至少满足这些条件：

- 每个代理有互不重叠的文件或目录；
- 主代理负责最终合并、测试和差异复查；
- 开始前已经检查工作树，知道哪些改动属于用户；
- 不让多个代理同时格式化或重写同一个公共文件；
- 外部发送、发布、删除和购买仍保留明确的人工确认。

第一次使用多代理时，优先让子代理做只读探索，由主代理统一修改。这样既能减少上下文噪音，也能避免“每个建议都对，但合在一起不能运行”。

## 用量和速度怎么评估

每个子代理都要单独推理和调用工具，所以处理同一个问题时，多代理通常会消耗更多 Token。它的好处是让独立工作同时进行，并从不同角度检查问题，而不是增加免费额度。

可以连续记录三次真实任务：

| 指标 | 要记录什么 |
| --- | --- |
| 总耗时 | 从发出任务到最终汇总完成 |
| 用量 | Usage 页面显示的变化或 Credits 消耗 |
| 重复率 | 多个代理是否报告同一问题 |
| 有效发现 | 最终被采纳并可验证的问题数 |
| 冲突成本 | 合并修改、重跑测试和人工去重时间 |

如果重复率高或合并时间超过并行节省的时间，应减少代理数量，或把子任务边界写得更具体。

## Subagents 不工作时怎么排查

1. 更新 Codex App、CLI 或 IDE 扩展，并确认当前入口支持子代理界面。
2. 在 Prompt 中直接写“使用两个子代理并行处理”，不要只期待自动拆分。
3. 检查配置中是否把 `agents.enabled` 设为 `false`。
4. 检查并发上限，以及是否已有未结束的代理线程占用容量。
5. 把任务缩小为两个只读搜索，排除文件冲突和写权限问题。
6. 查看其他代理线程是否正在等待审批、用户输入或工具返回。
7. 让主代理列出每个子任务的状态和阻塞原因，再决定继续、转向或停止。

如果任务本身无法独立拆分，退回单代理串行处理通常比继续增加线程更有效。

## 官方参考

- [OpenAI：Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)
- [OpenAI：Codex configuration basics](https://learn.chatgpt.com/docs/config-file/config-basic)
- [OpenAI：Codex configuration reference](https://learn.chatgpt.com/docs/config-file/config-reference)
- [OpenAI：Codex best practices](https://developers.openai.com/codex/learn/best-practices)
- [OpenAI：Agent approvals & security](https://developers.openai.com/codex/agent-approvals-security)

Codex 的入口、配置字段、模型与界面会变化。使用前应重新核对当前官方文档和本机版本，不要把本文的核对日期当成永久保证。

## 利益披露

本文由 Hi Codex 服务团队维护；维护者提供独立 ChatGPT / Codex 套餐相关人工充值协助，因此与套餐主题存在商业利益关系。Hi Codex 与 OpenAI 不存在隶属、代理或官方授权关系。是否增加套餐或 Credits 应根据 Usage 的真实消耗决定；升级不能修复任务拆分、权限、文件冲突或 Prompt 边界问题。
