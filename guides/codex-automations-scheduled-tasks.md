# 2026 Codex Automations 定时任务教程：Scheduled、Worktree、Cron 与不执行排查

> 核对日期：2026-07-21。本文依据 OpenAI 当前 Scheduled tasks、Worktrees、Sandboxing 和 Codex CLI 文档整理。

中文搜索里常把 `Codex Automations`、`Scheduled tasks`、定时任务和 cron 当成同一个功能。实际至少有四条不同路线；入口选错，会出现找不到按钮、无法访问本机文件、每次像新对话，或到了时间没有运行。

## 先用一张表选入口

| 需求 | 推荐路线 | 关键限制 |
| --- | --- | --- |
| 周期检查上传文件或连接服务 | ChatGPT 网页 standalone Scheduled | 不能直接访问本机文件夹 |
| 周期检查或修改本机项目 | ChatGPT 桌面 App Scheduled | 电脑开机、App 运行、路径可用 |
| 回到同一个调查或 PR 聊天继续 | 已有聊天定时续跑 | 必须写清停止和静默条件 |
| 服务器、CI 或操作系统计划任务 | 外部 cron / GitHub Actions + `codex exec` | 不是 App 的 Scheduled 管理界面 |

Codex CLI 和 IDE 扩展当前不提供 Scheduled 管理界面。它们可以用于测试 Prompt、Skill 或脚本；创建、暂停和查看 Scheduled 运行记录应使用 ChatGPT 网页或桌面 App。

## 创建顺序：不要先定时一个还没跑通的 Prompt

1. 在普通聊天用真实但非敏感样例手动运行一次。
2. 确认模型、工具、权限、输入范围和输出格式稳定。
3. 选择网页或桌面：需要本机目录只能使用桌面项目任务。
4. 选择 standalone 或当前聊天：独立周期报告用前者，持续轮询用后者。
5. 写清工作、周期、时区、无变化时的行为和停止条件。
6. 桌面 Git 项目优先选择 Worktree 隔离修改。
7. 创建后进入 Scheduled 核对 Active、下一次运行和前几次输出。

可以这样描述一个低风险任务：

```text
每周一 09:30（Asia/Shanghai）检查这个项目上周合并的 PR。
只报告失败测试、没有负责人的事项和可以从提交证明的文档漂移。
没有异常时返回“本周无新增异常”。
先只生成报告，不修改文件、不合并 PR、不发送外部消息。
每次生成独立运行，并在连续两次因同一原因失败时停止重试。
```

## Standalone 和已有聊天怎么选

Standalone Scheduled 每次从保存的 Prompt 开始，结果作为独立运行进入 Scheduled 收件箱。它适合日报、周报、代码库巡检和文档漂移。

已有聊天定时续跑会回到当前聊天，沿用已有上下文。它适合等待部署、轮询 CI、跟进 PR 或继续一个尚未结束的研究。即使上下文会保留，也要在定时说明里写清：

- 每次检查什么；
- 什么变化值得回复；
- 没变化时是否静默；
- 什么状态意味着任务完成；
- 何时必须停下来请求用户决定。

## Local、Worktree 和非 Git 项目

桌面 App 对 Git 项目可以选择 Local 或 Worktree：

- **Worktree**：后台任务在隔离工作树中运行，不干扰正在编辑的主 checkout。可能产生修改时优先选它。
- **Local**：直接操作主工作区，可以看到即时本地状态，也可能和人工未提交改动冲突。
- **非 Git 项目**：没有 Worktree 隔离，任务直接在项目目录工作；第一版应只读并先备份。

需要本机项目的任务运行时，电脑必须开机、ChatGPT 桌面 App 必须运行，项目路径和外接磁盘也必须可访问。频繁任务会积累运行记录和 Worktree，应归档不再需要的运行。

## 时间、时区和 RRULE

创建后不要只看 Prompt 里的“明天上午”，要核对界面显示的下一次运行和时区。跨地区协作时显式写 `Asia/Shanghai` 等 IANA 时区。

高级周期可以使用 RFC 5545 RRULE，例如：

```text
RRULE:FREQ=MONTHLY;BYMONTHDAY=1;BYHOUR=9;BYMINUTE=0
```

修改 RRULE 后再次查看下一次运行。先用简单的近未来一次性测试证明入口正常，再增加复杂周期。不要假设电脑关机期间错过的本机任务一定会自动补跑；用数据时间窗口去重，并查看 Scheduled 是否真的生成运行记录。

## 一个耐久 Prompt 应包含什么

1. **范围**：项目、目录、仓库、连接器或数据时间窗。
2. **动作**：每次只做稳定、可重复的检查或修改。
3. **证据**：文件、提交、测试、工单或可验证来源。
4. **输出**：固定的结论、证据、风险和建议结构。
5. **无变化**：返回固定短句或保持静默。
6. **停止条件**：完成、连续失败、缺少权限或需要用户决定。
7. **安全边界**：不泄露凭证、不删除数据、不擅自扩大权限。

需要复用复杂流程时，把它做成 Skill，并在桌面定时 Prompt 中显式写 `$skill-name`。不要依赖模糊的自动匹配承载关键步骤。

## 定时任务不执行：八层排查

1. **状态**：Scheduled 中是否为 Active，而不是 Paused 或 Completed。
2. **时间**：下一次运行、时区和 RRULE 是否解析正确。
3. **入口**：任务是否创建在当前账号和工作区的网页或桌面 App。
4. **本机条件**：电脑、App、项目路径、磁盘和当前用户权限。
5. **项目方式**：Local / Worktree、基础分支、Git 状态和 Worktree 创建。
6. **权限**：Sandbox、网络、工作区外路径、Plugins、连接器和 Skills。
7. **上下文**：Standalone 是否缺少只存在于手动聊天里的资料；网页任务是否错误引用本机目录。
8. **最小复现**：新建两分钟后只输出固定文字和当前时间的测试，再逐层加回项目与工具。

| 现象 | 先看哪里 | 不要先做 |
| --- | --- | --- |
| 完全没有运行记录 | Active、下一次时间、账号工作区、App | 直接开 Full Access |
| 运行了但找不到文件 | 网页/桌面、路径、磁盘、Local/Worktree | 把整个用户目录加入工作区 |
| `permission denied` | Sandbox、工作区范围、网络和组织策略 | 永久改成 unrestricted |
| 每次重复或“失忆” | standalone/聊天续跑、Prompt、时间窗口 | 无筛选复制全部历史 |
| Worktree 越来越多 | 频率、归档、是否真的需要隔离写入 | 删除仍被运行引用的目录 |

## 无人值守的权限边界

OpenAI 当前文档说明，Scheduled tasks 使用默认 Sandbox。组织策略允许时，任务会使用 `approval_policy = "never"`；这表示运行中不会等待临时确认，并不表示自动获得所有权限。

- `read-only` 适合第一版巡检；写文件、网络和桌面 App 操作会失败。
- `workspace-write` 可以修改工作区文件，工作区外路径和网络仍可能被阻止。
- `full access` 对后台任务风险最高，不应作为排错开关。

先只读、再 Worktree、最后才增加小范围修改。外部发送、合并、部署、删除和权限调整应保留明确目标、证据和人工审查点。

## 官方参考

- [Scheduled tasks](https://learn.chatgpt.com/docs/automations)
- [Git worktrees](https://learn.chatgpt.com/docs/environments/git-worktrees)
- [Sandboxing](https://learn.chatgpt.com/docs/sandboxing)
- [Permissions](https://learn.chatgpt.com/docs/permissions)
- [Codex non-interactive mode](https://developers.openai.com/codex/noninteractive)

更完整的入口对照、RRULE、三个可复制模板、手机端故障表和 FAQ 见：[2026 Codex Automations 定时任务创建与不执行排查](https://hi-codex.com/guides/codex-automations-scheduled-tasks/?utm_source=github&utm_medium=guide&utm_campaign=codex_automations)。

## 利益关系说明

本文为独立中文技术整理。作者维护的 Hi Codex 提供 ChatGPT / Codex 套餐相关人工充值协助，因此与套餐主题存在商业利益关系；与 OpenAI 不存在隶属、代理或官方授权关系。升级套餐不能修复任务状态、时区、App、项目路径、Worktree、Sandbox 或 Prompt 问题。
