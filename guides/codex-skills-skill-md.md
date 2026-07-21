# Codex Skills 与 SKILL.md：创建、安装和调用

> 核对日期：2026-07-21。安装第三方 Skill 前，应审查它的说明、脚本、依赖、网络访问和数据范围。

Codex Skill 是围绕一个聚焦任务组织的可复用工作流。核心文件是 `SKILL.md`，也可以附带脚本、参考资料、模板和资产。

Skill 不应被写成“什么都能做”的万能提示词。它应明确回答：什么时候使用、需要什么输入、按什么顺序执行、完成结果长什么样，以及什么情况必须停下来。

## 最小目录结构

```text
my-skill/
├── SKILL.md
├── scripts/       # 可选：稳定机械步骤
├── references/    # 可选：按需读取的参考资料
└── assets/        # 可选：模板、图片或其他资源
```

`SKILL.md` 的元数据至少应包含名称和清晰的 description。description 不只写功能名称，还应包含真实用户会说的触发词，以及不适用的边界。

示例：

```markdown
---
name: release-checker
description: 检查准备发布的 Web 项目；当用户要求发布前检查、上线审计或部署验收时使用。
---

# Release checker

1. 确认目标环境与允许操作的范围。
2. 检查工作树、构建、测试和配置。
3. 只在用户授权后执行外部发布。
4. 输出已验证项、未验证项和回滚信息。
```

## Skill 怎么调用

在支持的 Codex CLI / IDE 界面中，可以通过 `/skills` 或输入 `$` 显式选择 Skill。请求与 description 匹配时，Codex 也可能自动选择。

需要确定使用时，应显式调用；需要自动触发时，应优化 description，而不是在正文中堆很多关键词。

## SKILL.md 放在哪里

项目或仓库技能应放在 Codex 当前可扫描的项目/仓库 Skill 目录中；个人技能放在用户级 Skill 目录。具体目录和作用域会随 Codex 表面与版本变化，应以当前官方 Skills 文档和本机可见技能目录为准。

仓库 Skill 适合团队共同审查和版本控制；个人 Skill 适合跨项目复用。不要把密码、Token、Cookie、API Key 或固定内部凭证写进 Skill 文件。

## Skill、Plugin、MCP 和 AGENTS.md 的区别

| 概念 | 主要作用 |
| --- | --- |
| Skill | 封装一个聚焦任务的说明和资源 |
| Plugin | 用于安装和分发 Skills、连接器或 MCP 配置等能力 |
| MCP | 连接外部工具与上下文的协议 |
| AGENTS.md | 为某个代码范围提供持续适用的项目指令 |

它们可以组合，但不能互相简单替代。Skill 可以告诉 Codex 如何使用某个 MCP 工具；Plugin 可以分发多个 Skills；AGENTS.md 可以定义仓库级规则。

## 什么时候加入脚本

只有当步骤需要稳定、机械、可重复执行时再增加脚本，例如格式校验、批量生成或固定检查。脚本应：

- 输入和输出明确；
- 默认不执行破坏性或外部写入操作；
- 不读取超出任务范围的数据；
- 失败时给出可审查错误；
- 不把凭证硬编码进仓库。

## 为什么没有自动触发

依次检查：

1. Skill 是否位于当前环境可扫描的作用域；
2. 元数据是否有效；
3. description 是否包含真实请求意图；
4. 任务是否被另一个更精确的 Skill 覆盖；
5. 显式选择后是否可以正常执行。

官方参考：

- [Codex Skills](https://developers.openai.com/codex/skills)
- [Build skills](https://learn.chatgpt.com/docs/build-skills)
- [Codex Plugins](https://developers.openai.com/codex/plugins)

更完整的结构、作用域、安全边界和排查清单：[Hi Codex：Codex Skills 与 SKILL.md](https://hi-codex.com/guides/codex-skills-skill-md-create-use/?utm_source=github&utm_medium=guide&utm_campaign=codex_skills)

利益关系披露：维护者参与 Hi Codex 人工充值服务，与 ChatGPT / Codex 套餐主题存在商业利益关系；与 OpenAI 不存在隶属、代理或官方授权关系。
