# Codex 已并入 ChatGPT 桌面 App：Chat、Work、Codex 与多仓库项目怎么选

> 核对日期：2026-08-08。2026 年 7 月 9 日，原 Codex App 合并到 macOS 与 Windows 的 ChatGPT 桌面 App，但 Codex 的独立开发者视图和历史仍然保留。

“Codex 并入 ChatGPT”不等于 Codex 被取消，也不代表所有对话、工作模式和代码任务混成一个入口。现在的桌面 App 同时提供 Chat、ChatGPT Work 和 Codex：它们共享部分能力，但界面、历史、技术细节和适合的任务不同。

原 Codex App 用户可以按原方式更新。新版桌面 App 面向所有 ChatGPT 套餐提供，包括 Free；具体模型、Work、Plugins、Computer Use、Remote 和用量仍会受套餐、地区、平台、灰度和工作区策略影响。

## 三个入口的快速选择

| 入口 | 适合 | 你会看到什么 |
| --- | --- | --- |
| Chat | 问答、搜索、头脑风暴、短文案、快速比较 | 对话为主，技术执行细节较少 |
| ChatGPT Work | 从目标出发，完成报告、表格、演示、研究或其他可审查成品 | 进度、工具和最终产物，偏非技术表达 |
| Codex | 调试代码、运行测试、检查 diff、Review PR、实现功能 | Git、Shell、diff、Review 和开发者细节 |

如果工作主要围绕仓库、分支、测试和 PR，直接使用 Codex。任务是研究、文档或成品文件时，可以用 Work；偏好 Codex 的用户也可以继续在 Codex 中完成知识工作。

## 合并以后哪些东西还保留

Codex 在 ChatGPT 桌面 App 中仍有独立入口，并保留：

- 面向开发项目的独立历史；
- diff 中的内联编辑；
- 侧边栏 Pull Request Review；
- Git、Worktree、终端和测试相关视图；
- 多仓库项目；
- Computer Use、Browser、Skills、Plugins 和 Subagents 等可用能力。

你可以把 Codex 设为默认视图，也可以把 App 图标切换成 Codex 标识。桌面 Codex 项目还可以从 ChatGPT 手机端访问，但远程连接、可操作的本地文件和客户端能力仍需要满足对应条件。

## Chat、Work 和 Codex 的历史为什么看起来不一样

ChatGPT 桌面端把 Chat 与 Work 会话放在 ChatGPT 视图中。Cloud Work 会在 Web、移动端和桌面端同步；Local Work 会话留在本机。

Codex 有单独的开发者视图和项目历史。这样，普通问答和仓库任务不会混在一起，查找代码任务时，也能同时看到相关的 Git、diff 和审查记录。

如果你在一个入口找不到旧任务，先确认它最初是 Chat、Work 还是 Codex，再确认它是 Cloud 还是 Local；不要直接判断聊天已经丢失。

## 一个本地项目可以添加多个文件夹

现在本地项目可以同时附加多个相关文件夹，例如：

- Web 前端与 API 后端；
- 应用代码与独立文档仓库；
- 主仓库与部署配置；
- 产品网站与内容仓库。

在项目菜单选择 **Edit project → Add folder** 添加目录，并为其中一个目录设置 **Make primary**。

主目录决定：

- 新聊天的默认工作目录；
- 默认 Git 操作目标；
- 自动发现的 `AGENTS.md`、Skills 和 `config.toml`；
- Pull Request 与 Worktree 操作的主要仓库。

辅助目录仍可被搜索、读取和修改，但 Codex 不会自动从辅助目录发现其中的 `AGENTS.md`、Skills 和 `config.toml`。因此，影响整个任务的核心规则应放在主目录，或在 Prompt 中明确说明辅助仓库的约定。

远端项目当前仍只支持一个文件夹。多文件夹本地项目可见，不代表同样的结构会自动出现在 Cloud 环境。

## 多仓库 Review 怎么用

当同一个本地项目附加多个 Git 仓库时，Review 面板可以显示每个仓库及其改动行数，让你在一个审查入口查看跨仓库变更。

一个实用流程是：

1. 把承担主要 Git / PR 操作的仓库设为 Primary；
2. 在任务开始前让 Codex 列出每个仓库的分支与工作树状态；
3. 明确哪些仓库允许修改、哪些只读；
4. 完成后按仓库分别列出 diff、测试和未提交文件；
5. 在 Review 面板统一检查跨仓库影响。

把多个文件夹加入项目，不代表 Codex 就获得了更大的权限。它能读写哪些文件、能否访问网络，仍取决于 Sandbox、Approval、操作系统权限和工作区策略。

## 新版桌面端还值得注意什么

2026 年 7 月后，桌面端连续加入了几项与开发流程直接相关的能力：

- Browser 可以从地址栏搜索 Google 或浏览历史，并在获得批准后为任务找回早先访问的上下文；
- Chrome 扩展可以提及已打开标签页、把选中文本带入侧边聊天，并处理 YouTube 页面问题；
- Activity 视图集中显示最近互动和需要处理的任务；
- 生成图片可以在展开视图中评论、选择版本和继续定向修改；
- ChatGPT Voice 可以在 Chat、Work 和 Codex 中启动、检查或调整其他任务。

这些能力的可见性可能不同。浏览器历史、屏幕、桌面操作和外部连接都应按最小权限开启，并在关键动作前查看审批内容。

## 从旧 Codex App 迁移的检查表

1. 正常更新到当前 ChatGPT 桌面 App，不要先删除个人配置目录。
2. 打开 Codex 视图，确认原项目和历史是否出现。
3. 检查默认项目目录、Git 分支和未提交修改。
4. 确认 `config.toml`、`AGENTS.md`、Skills、Plugins、MCP 和 Hooks 仍来自预期位置。
5. 使用多文件夹项目时明确 Primary，并检查辅助目录的规则不会自动加载。
6. 重新核对 Browser、Computer Use、Screen Recording、Accessibility 和网络权限。
7. 用一个低风险任务依次检查：能否读取文件、修改代码、运行测试，以及在 Review 中查看改动。

## 常见误解

### “Codex App 没了，所以只能用 Chat”

不对。Codex 已成为 ChatGPT 桌面 App 内的独立开发者入口，保留专门的项目、历史和技术视图。

### “Free 能下载桌面 App，所以所有功能都免费且无限”

不对。桌面 App 面向所有套餐提供，不等于每个模型、Work、Plugin、Computer Use 或用量档位完全相同。以账号内可见功能和 Usage 为准。

### “添加辅助目录后，它的 AGENTS.md 会自动生效”

不对。自动发现项目级 `AGENTS.md`、Skills 和 `config.toml` 使用 Primary；辅助目录主要用于搜索、读取和编辑。

### “多个仓库放进一个项目，就能一次 Push 所有仓库”

不对。Review 可以统一查看，但 PR 和 Worktree 操作以 Primary 为主要目标。跨仓库发布仍应分别确认远端、分支、权限、测试和提交范围。

## 官方参考

- [OpenAI：Use Codex in the ChatGPT desktop app](https://learn.chatgpt.com/docs/whats-new#use-codex-in-the-chatgpt-desktop-app)
- [OpenAI：Use ChatGPT](https://learn.chatgpt.com/docs/use-chatgpt)
- [OpenAI：Projects and chats](https://learn.chatgpt.com/docs/projects#use-local-projects-for-folders-and-codebases)
- [OpenAI：ChatGPT & Codex Changelog](https://learn.chatgpt.com/docs/changelog)
- [OpenAI：ChatGPT desktop app](https://learn.chatgpt.com/docs/app)

桌面 App 变化很快。本文固定记录 2026-08-08 的入口与项目行为；界面名称、套餐资格和灰度范围请以当前官方文档和账号为准。

## 利益披露

本文由 Hi Codex 服务团队维护。维护者提供独立 ChatGPT / Codex 套餐相关人工充值协助，因此与套餐主题存在商业利益关系；与 OpenAI 不存在隶属、代理或官方授权关系。桌面端故障应先按版本、项目、权限和配置排查，不应把升级套餐当作通用修复。
