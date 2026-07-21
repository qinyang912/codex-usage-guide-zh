# 2026 Codex 教程：从安装、登录到第一个真实任务

> 更新于 2026-07-21。本教程面向第一次使用 Codex 的用户，目标不是罗列所有功能，而是在约 30 分钟内安全完成一个能验证、能回滚的小任务。

## 先选入口：App、CLI、IDE 还是 Cloud

四个入口使用的是同一类 Codex 能力，但工作位置不同：

| 入口 | 更适合 | 第一次使用建议 |
| --- | --- | --- |
| Codex App | 想看任务、Diff 和审批界面 | 桌面用户最容易入门 |
| Codex CLI | 经常在终端和本地仓库工作 | 熟悉命令行后效率高 |
| IDE 扩展 | 希望一边看代码一边协作 | 已使用 VS Code、Cursor 或 Windsurf |
| Codex Cloud | 让任务在远端环境运行 | 先理解仓库、环境和权限再用 |

不要同时安装四种入口来验证同一个问题。第一次只选一个入口，完成闭环后再扩展。

## 第一步：安装并确认登录

CLI 的 npm 安装方式是：

```bash
npm install --global @openai/codex
codex --version
codex login
codex login status
```

如果提示 `command not found`，先检查 npm 全局目录是否在 `PATH`，以及机器上是否残留多份旧版本。登录也分两条路线：个人交互式使用通常选择 ChatGPT 登录；脚本或 CI 只有在明确需要按 Platform API 计费时才使用 API Key。Plus / Pro 套餐与 OpenAI Platform API Billing 是两套账单。

## 第二步：选一个低风险的真实任务

首次任务最好同时满足四个条件：

1. 修改范围小，例如文案、一个组件或一条失败测试。
2. 仓库已有测试或至少有明确的人工验收步骤。
3. 不需要生产密钥、付款、删库或大规模迁移。
4. 即使修改不满意，也能从 Git Diff 中撤销。

例如：“修复设置页空状态文案，并运行这个组件的测试”比“重构整个前端”更适合作为第一次任务。

## 第三步：用四段式 Prompt 说清验收标准

一个可执行的 Prompt 应至少包含：

```text
Goal：修复设置页在没有数据时的空状态文案。
Context：页面组件在 src/settings/EmptyState.tsx，已有相邻测试。
Constraints：不要修改接口；保持现有设计系统；不要新增依赖。
Done when：文案正确，相关测试通过，并总结修改文件和潜在风险。
```

这四段分别对应目标、上下文、约束和完成条件。对复杂任务，先让 Codex 给出 Plan，确认范围后再修改。缺少验收标准时，模型可能完成了“看起来合理”的改动，却不是你真正需要的结果。

## 第四步：让仓库规则可见

如果项目有固定命令、目录边界或代码规范，可以在仓库根目录写一个简短的 `AGENTS.md`：

```md
# AGENTS.md
- 安装：npm ci
- 检查：npm run lint && npm test
- 只修改当前任务涉及的文件
- 不提交 .env、Token、Cookie 或生产密钥
- 完成后说明测试结果与未验证项
```

规则应短、明确、可以执行。不要把互相冲突的长篇背景资料全部塞进去；子目录有不同要求时，再在对应目录添加更具体的说明。

## 第五步：从默认权限开始

Sandbox 决定命令能访问什么，Approval 决定哪些动作需要你确认；它们不是同一个设置。第一次使用保持默认权限，看到审批时阅读具体命令、目标路径和网络访问原因。

不要为了少点几次确认就直接放开所有权限。涉及删除、覆盖、生产部署、对外发帖、购买或凭证时，必须明确目标和影响。任何时候都不要把密码、Session、Cookie、恢复码或 API Key 直接粘贴进普通 Prompt。

## 第六步：验证，而不是只看“已完成”

任务结束后至少完成这四项：

1. 查看 Git Diff，确认没有无关文件和意外删除。
2. 运行与修改最接近的测试、Lint 或构建。
3. 对页面任务检查桌面和手机宽度；对接口任务检查失败分支。
4. 让 Codex Review 当前改动，并自己核对高风险结论。

CLI 中可以使用 `/status` 查看会话状态，用 `/review` 请求审查；环境异常时可运行 `codex doctor`。需要继续之前的交互任务时，使用 `codex resume`。命令可能随版本更新，执行前以当前官方文档和本机 `--help` 为准。

## 常见失败怎样分流

| 现象 | 先检查什么 |
| --- | --- |
| `codex: command not found` | 安装结果、PATH、Shell 重载和旧版本 |
| 登录 401 / 403 | 登录方式、账号/工作区、Token 状态和官方服务状态 |
| Usage limit | ChatGPT / Codex Usage、恢复时间、套餐和 Credits |
| `insufficient_quota` | 是否走 API Key、Platform 组织和 Billing |
| 修改范围失控 | 停止任务，缩小 Goal、文件范围和 Done when |
| 测试失败 | 先保存原始错误，区分代码回归与环境问题 |

不要把所有错误都归因于“需要充值”。安装、认证、网络、权限、测试和套餐分别有不同证据。先完成技术诊断，只有账号页面明确显示 Usage limit 或套餐不足时，再讨论额度。

## 一次完整闭环

第一次任务的合格结果应是：你能解释 Codex 修改了什么、为什么这样改、运行了哪些验证、还有哪些未验证项，并能从 Diff 中接受或撤销改动。能做到这一点，再尝试多文件重构、Cloud、Automations、Skills、MCP 或更高权限任务。

更完整的入口对比、安装命令、手机端阅读版与专题错误分流见：[2026 Codex 从安装登录到第一个任务](https://hi-codex.com/guides/codex-tutorial-getting-started-first-task/?utm_source=github&utm_medium=guide&utm_campaign=codex_tutorial)。

## 来源与利益关系

本文依据 OpenAI Codex 官方的入口选择、Authentication、Best practices、CLI、AGENTS.md 与 Agent approvals 文档整理。功能和命令会变化，应以当前官方页面和本机版本为准。

作者维护 Hi Codex，并提供用户自己 ChatGPT 账号的 Plus / Pro 独立人工充值协助，对会员主题存在商业利益；与 OpenAI、GitHub、知乎或闲鱼不存在隶属、代理或官方授权关系。技术错误未排除前，不建议把充值当作通用解决方案。
