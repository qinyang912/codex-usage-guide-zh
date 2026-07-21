# 2026 Codex AGENTS.md 配置指南：全局文件、目录覆盖与验证

`AGENTS.md` 是 Codex 的持久项目说明文件。它适合记录仓库结构、安装与测试命令、代码约定、禁止修改的范围和交付检查。Codex 开始一次运行时会自动发现这些文件，把个人全局说明与当前目录对应的项目说明组合起来。

它与其他 Codex 配置不是同一层：`config.toml` 管模型、Sandbox、MCP 等客户端设置；Skill 封装按任务选择的工作流；Rules 控制哪些命令可以在 Sandbox 外运行。代码规范和验证命令写进 `AGENTS.md`，通常比塞进另外三种机制更清楚。

## 一、推荐的三层结构

```text
~/.codex/AGENTS.md                  # 个人跨项目习惯
my-project/AGENTS.md                # 整个仓库共同规则
my-project/apps/web/AGENTS.md       # Web 子项目补充规则
```

- **全局文件**：写跨项目都成立的个人约定，例如先保留未提交修改、完成后运行相关测试、不公开密钥。
- **仓库根文件**：写项目结构、统一包管理器、构建命令、CI 要求和公共边界。
- **子目录文件**：写某个应用或包的局部命令、公开 API 和例外，不重复整份根规则。

不要在全局文件强制所有仓库使用同一个包管理器或测试命令。只适用于一个项目的事实应跟着项目提交，只适用于一个子树的要求应尽量靠近那个目录。

## 二、Codex 的发现与覆盖顺序

Codex 当前按以下顺序构建指令链：

1. 在 `CODEX_HOME` 中寻找全局说明；默认通常是 `~/.codex`。
2. 全局层优先读取非空 `AGENTS.override.md`，否则读取 `AGENTS.md`。
3. 项目层通常从 Git 根目录走到当前工作目录。
4. 每一级目录依次检查 `AGENTS.override.md`、`AGENTS.md`，然后检查配置的 fallback 文件名。
5. 每个目录最多选择一个非空文件，再从根到当前目录依次合并。

更靠近当前工作目录的内容加入得更晚，所以冲突时更具体的规则占优；父目录没有冲突的内容仍会保留。相邻目录不在发现路径上，例如从 `apps/web` 工作时不会自动读取 `apps/api/AGENTS.md`。

如果找不到项目根，Codex 只检查当前目录的项目说明。Windows、WSL、SSH、容器和本机也可能使用不同的 home 与 `CODEX_HOME`，排查时要先确认真正运行 Codex 的环境。

## 三、override、fallback 与大小限制

`AGENTS.override.md` 会替换同一目录的普通 `AGENTS.md` 候选。它适合迁移期、冻结期或个人临时覆盖，但应说明清理条件，避免长期忘记移除。

仓库已有其他文件名时，可以在用户配置中添加 fallback：

```toml
# ~/.codex/config.toml
project_doc_fallback_filenames = ["TEAM_GUIDE.md", ".agents.md"]
```

fallback 只在同一目录前面的 override 和标准文件没有被选中时使用，不会让一个目录同时拼接多份说明。

当前官方文档列出的合并上限是 32 KiB，由 `project_doc_max_bytes` 控制。内容接近上限时，应先删除重复、把专用规则下沉到子目录、把长背景放进独立文档；确实需要时再调整：

```toml
project_doc_max_bytes = 65536
```

## 四、一个最小项目模板

```md
# Project instructions

## Project map
- apps/web：Web 应用。
- apps/api：API 与后台任务。
- packages/ui：共享组件，修改公开接口要同步更新使用方。

## Commands
- 使用仓库现有包管理器和锁文件。
- 相关测试：pnpm test
- 类型检查：pnpm typecheck
- 生产构建：pnpm build

## Change rules
- 修改前检查工作树，不覆盖任务外的未提交内容。
- 不手改生成文件，使用仓库脚本重新生成。
- 不输出或提交 API Key、Cookie、Session 和真实用户数据。

## Definition of done
- 运行与改动范围匹配的测试。
- 用户可见行为变化要更新对应文档。
- 提交只包含当前任务相关文件。
```

Codex CLI 当前目录中使用 `/init` 可以生成一份起始模板。生成结果仍需对照真实的 package scripts、Makefile、CI 和目录结构逐条修订，不能把示例命令直接当成事实。

## 五、怎样把说明写得可执行

| 模糊要求 | 更可验证的写法 |
| --- | --- |
| 保持高质量 | 修改 TypeScript 后运行类型检查与相关单测 |
| 不要破坏旧代码 | 修改前检查工作树，不覆盖无关未提交改动 |
| 注意安全 | 不输出密钥、Cookie、Session；日志示例必须脱敏 |
| 遵守项目风格 | 复用指定组件目录，命名跟随相邻文件 |

优先写真实命令、路径、禁止范围和完成标准。高风险或反直觉规则可以补一句原因，但不要把设计文档全文复制进来。指令过长不仅消耗上下文，也会让真正重要的边界被淹没。

## 六、验证与排查

1. 记录当前 host、工作目录与 `CODEX_HOME`。
2. 从项目根到当前目录列出 override、标准文件与 fallback 候选。
3. 确认文件非空，名称没有被保存成 `AGENTS.md.txt`。
4. 开启一次新运行，让 Codex 复述当前构建命令与禁止修改范围。
5. 从仓库根和子目录分别启动，确认局部规则只在目标子树生效。
6. 规则缺失时检查合并内容是否接近默认 32 KiB。

Codex 在每次运行开始时构建指令链。刚修改文件但当前会话没有变化时，开启新运行再验证，不需要删除整个 `.codex` 目录。

团队仓库应像评审 CI 配置一样评审 `AGENTS.md`。第三方 Pull Request 如果新增下载脚本、扩大目录权限、要求跳过 Sandbox 或自动批准工具，应单独检查来源和作用域。密钥只应通过受控环境变量或凭证机制提供，不能写入指令文件。

## 官方资料与完整中文教程

- [OpenAI：Custom instructions with AGENTS.md](https://learn.chatgpt.com/docs/agent-configuration/agents-md)
- [OpenAI：Codex Rules](https://learn.chatgpt.com/docs/agent-configuration/rules)
- [AGENTS.md 开放格式说明](https://agents.md/)

需要发现顺序对照表、全局与子目录示例、完整模板、安全清单和常见问题，可继续阅读：[Hi Codex：2026 Codex AGENTS.md 教程](https://hi-codex.com/guides/codex-agents-md-config-best-practices/?utm_source=github&utm_medium=guide&utm_campaign=codex_agents_md)。

本文核对日期为 2026-07-21。Codex 的文件发现与配置字段可能更新，应以当前官方文档和实际客户端行为为准。

## 利益披露

本文由 Hi Codex 服务团队维护；维护者提供独立人工充值协助，因此与 ChatGPT / Codex 套餐主题存在商业利益关系。Hi Codex 与 OpenAI 不存在隶属、代理或官方授权关系。本文不要求共享账号、Token、Cookie、Session 或私有代码，也不提供绕过 Sandbox 与审批的方法。
