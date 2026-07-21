# OpenAI Codex 是什么？App、CLI、IDE 与 Cloud 怎么选

> 核对日期：2026-07-21。Codex 的入口、模型和套餐会持续变化，最终以 OpenAI 当前页面和账号内显示为准。

Codex 是面向软件开发与复杂工作流的智能代理能力。它不仅回答代码问题，还可以在获得相应权限后读取和修改文件、运行命令、检查差异、执行测试并形成可审查的结果。

“Codex App”“Codex CLI”“IDE 扩展”和“Codex Cloud”不是四种不同模型，而是不同的工作入口。选择时应先看代码在哪里、工具运行在哪里、权限如何控制，以及任务是否需要远端环境。

## 四种入口对比

| 入口 | 适合场景 | 需要重点确认 |
| --- | --- | --- |
| ChatGPT 桌面 App 中的 Codex | 多项目、并行任务、文件产物、代码审查和图形化管理 | 项目目录、系统权限、终端与 Agent 环境 |
| Codex CLI | 在终端围绕本地 Git 仓库工作 | 当前路径、PATH、Sandbox、Approval、登录方式 |
| Codex IDE 扩展 | 在 VS Code、Cursor、Windsurf 等编辑器中工作 | Extension Host、Local/Remote、共享配置和认证 |
| Codex Cloud | 远端仓库、并行任务和隔离环境 | 仓库授权、分支、环境依赖、变量和审查流程 |

## Codex CLI

CLI 适合已经习惯终端和 Git 的开发者。进入项目目录运行 `codex` 后，应先确认：

```bash
codex --version
codex login status
git status --short
```

Codex 能否写文件、运行命令或访问网络，取决于 Sandbox、Approval、工作区和操作系统权限。`approval_policy = never` 不会自动扩大 Sandbox；Full Access 也不应成为生产仓库的默认设置。

## IDE 扩展

IDE 扩展把 Codex 放进编辑器上下文，但 Remote WSL、SSH、Codespaces 与本地窗口可能运行在不同 host。CLI 在本机正常，不代表远端 Extension Host 的 PATH、配置、认证和网络也正常。

图标不显示、面板一直转圈或 `failed to start codex app-server` 时，应保存完整错误、客户端版本和运行环境，再分别检查扩展、认证、配置与 app-server，而不是直接删除整个配置目录。

## Codex Cloud

云端任务依赖已连接的仓库和环境。本机未提交文件、私有依赖、凭证和本地命令不会自动出现在云端。开始任务前应确认：

1. 使用哪个仓库、分支或工作树；
2. 是否包含未推送修改；
3. 构建需要哪些依赖、变量、网络资源和组织权限；
4. 完成后如何审查 diff、测试、日志和安全影响。

## ChatGPT 套餐和 API 账单

使用 ChatGPT 登录时，通常查看套餐侧的 Codex 用量与 Credits；使用 API Key 时，查看 OpenAI Platform API 账单。两者分开，不能因为拥有 Plus / Pro 就推断 API Key 调用免费。

## 安全边界

- 先建立 Git 检查点，再让 Agent 修改重要仓库；
- 不把生产凭证、API Key、Cookie 或 Session 写入提示词和仓库；
- 生产环境遵守最小权限、审批、审计和回滚要求；
- 技术错误按完整错误分流，不把升级套餐当作万能修复。

官方参考：

- [Codex](https://developers.openai.com/codex/)
- [Codex CLI](https://developers.openai.com/codex/cli)
- [Codex IDE extension](https://developers.openai.com/codex/ide)
- [Codex Cloud](https://developers.openai.com/codex/cloud)

更完整的入口对比、故障分流和套餐说明：[Hi Codex：Codex 是什么](https://hi-codex.com/guides/codex-what-is-it-app-cli-ide-cloud/?utm_source=github&utm_medium=guide&utm_campaign=codex_foundations)

利益关系披露：维护者参与 Hi Codex 人工充值服务，与 ChatGPT / Codex 套餐主题存在商业利益关系；与 OpenAI 不存在隶属、代理或官方授权关系。
