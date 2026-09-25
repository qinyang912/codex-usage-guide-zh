# Codex GPT-5.6 Sol、Terra、Luna 怎么选：模型、Reasoning 与旧版迁移

> 核对日期：2026-08-08。OpenAI 当前把 GPT-5.6 Sol 作为旗舰模型；Codex 的默认 Power 设置使用 Sol 与 Medium reasoning。

选择 Codex 模型，不必每次都选最强的一档。Sol 更适合复杂、需要探索的任务，Terra 面向日常工作，Luna 适合要求明确的重复任务。选定模型后，还可以通过 Reasoning 调整它花多少精力分析和检查。

选择时可以问自己：任务需要多少探索？怎样才算完成？出错会有什么影响？想清楚这些问题，再比较速度、质量和用量。

## 30 秒选择表

| 模型 | 适合的任务 | 一个实用起点 |
| --- | --- | --- |
| GPT-5.6 Sol | 跨模块重构、疑难排错、安全审查、深度研究、需要成品质量的输出 | Sol + Medium；结果不足再提高 Reasoning |
| GPT-5.6 Terra | 日常功能、代码审查、测试补充、稳定工具调用 | Terra + Medium |
| GPT-5.6 Luna | 提取、分类、格式转换、结构化总结、批量明确任务 | Luna + Low 或 Medium |

拿不准时，OpenAI 当前建议先从 Sol 开始。任务边界清楚以后，再用同一组真实样本比较 Terra 或 Luna，不要只比较第一段回复有多快。

## Sol、Terra、Luna 的区别

### Sol：复杂、开放、错误代价高

Sol 适合需求仍有歧义、影响面较大、需要综合判断或最终成品要求高的工作。例如跨多个包的架构调整、难以复现的故障、安全审查和多来源技术方案。

Sol 能力强，不代表每次都应使用 Max。范围明确的小修改如果长期固定使用最高推理档，可能只增加等待和用量。

### Terra：日常工作的平衡点

日常开发需要推理和工具配合，但不一定每次都需要 Sol 的分析深度。普通功能、常规 Review 和测试任务，可以先用 Terra 跑一遍，再判断是否需要更强的模型。

### Luna：清楚、重复、高批量

Luna 适合输入、输出和验收标准都清楚的任务。字段提取、分类、固定格式转换和批量结构化摘要通常比开放式系统设计更匹配 Luna。

使用 Luna 时，Prompt 应明确字段、格式、异常处理和停止条件。任务定义含糊时，速度优势可能被返工抵消。

## 模型与 Reasoning 是两个维度

OpenAI 当前建议使用“能得到所需结果的最低 Reasoning”：

- **Light / Low**：范围清楚、步骤少、强调快速反馈；
- **Medium**：日常速度与深度的平衡起点；
- **High / Extra High**：多步骤、多来源或需要更多检查和权衡；
- **Max**：让所选模型在单个困难任务上投入更多时间；
- **Ultra**：使用 Subagents 并行处理可以真正拆开的复杂任务。

Max 和 Ultra 不是同一件事。Max 是让一个任务想得更深；Ultra 是把独立部分交给多个代理并行处理。任务不能清楚拆分时，Ultra 可能增加重复搜索、文件冲突和汇总成本。

## 在 App 和 CLI 中切换模型

ChatGPT 桌面 App、Codex CLI 和 IDE 扩展都提供模型与 Reasoning 选择。CLI 交互会话中可以使用：

```text
/model
```

启动时使用 `--model` 或 `-m`：

```bash
codex -m gpt-5.6-sol
codex -m gpt-5.6-terra
codex -m gpt-5.6-luna
```

非交互任务同样支持：

```bash
codex exec -m gpt-5.6-terra "Review the current changes"
```

本地默认模型可以写入共享的 `config.toml`。官方当前示例使用 GPT-5.6 家族别名：

```toml
model = "gpt-5.6"
```

如果没有设置，客户端会使用推荐模型。Codex cloud chat 当前由服务自动选择模型，不能从本机 `config.toml` 推断云端实际模型。

## 重要日期：GPT-5.4 将在 2026-08-31 从 ChatGPT 登录的 Codex 退休

OpenAI 当前公告：2026 年 8 月 31 日后，使用 ChatGPT 账号登录 Codex 的用户不能再选择 GPT-5.4 和 GPT-5.4 mini。

官方建议替换关系是：

| 旧模型 | 推荐替换 |
| --- | --- |
| `gpt-5.4` | `gpt-5.6-terra` |
| `gpt-5.4-mini` | `gpt-5.6-luna` |

这次退休不影响 OpenAI API，也不影响使用自己 API Key 认证的 Codex 会话。不要把 ChatGPT 登录的 Codex 模型退休，写成“API 同名模型也在同一天下线”。

## 8 月 31 日前应检查哪些地方

不要只改当前聊天的模型选择。旧模型可能同时存在于：

- 用户级与项目级 `config.toml`；
- CLI 启动脚本中的 `--model` 或 `-m`；
- `codex exec` 自动化；
- 自定义 Agents / Subagents；
- Scheduled tasks；
- 工作区默认值和托管配置；
- 团队文档、模板与复制出来的示例。

在仓库中可以先做只读搜索：

```bash
rg -n 'gpt-5\.4(-mini)?|--model|-m ' .
```

找到旧模型的引用后，按任务用途逐项调整，不必全部换成 Sol。OpenAI 针对 Codex 的明确退休映射是 GPT-5.4 → Terra、GPT-5.4 mini → Luna；改完还应重跑代表性任务、测试和用量对比。

## 用同一组真实任务做模型基准

准备 3～5 个日常任务，每次只改一个变量，记录：

1. 模型与 Reasoning；
2. 首次可用结果和总耗时；
3. 修改文件数与 diff 规模；
4. 测试是否一次通过；
5. 人工修正与返工次数；
6. Usage 页面显示的用量变化。

例如，对同一个格式迁移任务分别运行 Terra + Medium 与 Luna + Medium。如果两者都一次通过，而 Luna 更快、没有增加返工，可以把这类明确任务固定到 Luna。开放式重构若在 Terra 上持续遗漏影响面，则回到 Sol，而不是给所有任务统一增加 Reasoning。

## Model not found / 404 怎么分流

出现 `Model not found`、`unsupported model` 或 404 时，先确认：

1. 当前使用 ChatGPT 登录还是 API Key；
2. `/model` 中账号实际可见什么；
3. CLI、App 或 IDE 是否已更新；
4. `config.toml`、profile、启动参数和组织策略谁覆盖了模型；
5. 自定义 provider 是否支持当前 Responses API 和目标模型。

不要连续随机猜模型名。完整步骤见 [Codex Model not found / 404 排查](codex-model-not-found-404.md) 与 [config.toml 配置排查](codex-config-toml-troubleshooting.md)。

## 官方参考

- [OpenAI：Codex Models](https://learn.chatgpt.com/docs/models)
- [OpenAI：ChatGPT & Codex Changelog](https://learn.chatgpt.com/docs/changelog#codex-2026-07-31)
- [OpenAI：Codex Config basics](https://learn.chatgpt.com/docs/config-file/config-basic)
- [OpenAI：Codex Subagents](https://learn.chatgpt.com/docs/agent-configuration/subagents)

模型、默认值、推理档位和账号可见范围会继续变化。使用前应同时检查官方 Models 页面、当前客户端和 `/model`，不要把本文的核对日期当成永久保证。

## 利益披露

本文由 Hi Codex 服务团队维护。维护者提供独立 ChatGPT / Codex 套餐相关人工充值协助，因此与套餐主题存在商业利益关系；与 OpenAI 不存在隶属、代理或官方授权关系。模型选择应依据真实任务、质量、延迟和 Usage，不应只为显示某个模型名称而购买套餐。
