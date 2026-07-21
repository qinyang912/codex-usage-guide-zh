# Codex Plugins：创建、安装、plugin.json 与 Marketplace 实用指南

> 最后核对：2026-07-21。Codex Plugins 仍在快速更新，界面、命令和清单字段请同时对照 OpenAI 当前文档。

## 先分清：这里说的 Plugin 不是 VS Code 扩展

Codex Plugin 是一个可安装的能力包。它可以组合：

- 一个或多个 Skills；
- 连接器或 MCP Server；
- 生命周期 Hooks；
- 浏览器能力或定时任务模板；
- 图标、截图和安装页说明等展示资源。

VS Code Marketplace 里的 OpenAI Codex 扩展，是把 Codex 放进编辑器的客户端入口。两者都常被中文称为“插件”，但安装位置、用途和故障排查完全不同。

当前支持浏览和安装 Plugins 的入口包括：

1. ChatGPT 网页的 Work mode；
2. ChatGPT 桌面 App 的 Work mode 或 Codex；
3. Codex CLI 的 `/plugins` 浏览器。

IDE Extension、Chat mode 和移动端当前没有 Plugin 浏览器。安装以后应开启新聊天或新的 CLI 会话，让新能力进入会话上下文。

## 什么时候用 Skill，什么时候做 Plugin

| 需求 | 更合适的机制 |
| --- | --- |
| 临时要求，只在当前对话生效 | Prompt |
| 一个仓库长期遵守的构建、测试和目录约定 | `AGENTS.md` |
| 可复用的任务流程、说明、脚本和参考资料 | Skill |
| 连接外部工具、数据或 SaaS | MCP Server / Connector |
| 在确定生命周期节点执行脚本 | Hook |
| 把以上能力组合成可安装、可分享、可版本化的包 | Plugin |

如果工作流只给自己或一个仓库使用，先做 Skill，验证触发、步骤和结果。需要跨团队安装、组合多个 Skills、接入外部服务、加入 Hooks 或维护版本时，再包装成 Plugin。

## 必需目录：`.codex-plugin/plugin.json`

每个 Plugin 都必须在根目录包含 `.codex-plugin/plugin.json`。只有这个 manifest 放在 `.codex-plugin/`；其他组件保留在 Plugin 根目录：

```text
team-review/
├── .codex-plugin/
│   └── plugin.json
├── skills/
│   └── review-pr/
│       └── SKILL.md
├── hooks/
│   └── hooks.json
├── assets/
│   ├── icon.png
│   └── logo.png
├── .mcp.json
└── .app.json
```

最小 manifest 可以只有标识、版本、描述和组件路径：

```json
{
  "name": "team-review",
  "version": "0.1.0",
  "description": "Repeatable code review workflows",
  "skills": "./skills/"
}
```

`name` 应使用稳定的 kebab-case。不要为了显得完整而声明不存在的 `mcpServers`、`apps`、`hooks` 或权限。

准备给其他人安装时，可以补充作者、仓库、许可证和安装界面元数据：

```json
{
  "name": "team-review",
  "version": "0.1.0",
  "description": "Bundle repeatable code review workflows.",
  "author": {
    "name": "Example Team",
    "url": "https://example.com"
  },
  "homepage": "https://example.com/team-review",
  "repository": "https://github.com/example/team-review",
  "license": "MIT",
  "keywords": ["review", "quality"],
  "skills": "./skills/",
  "mcpServers": "./.mcp.json",
  "apps": "./.app.json",
  "hooks": "./hooks/hooks.json",
  "interface": {
    "displayName": "Team Review",
    "shortDescription": "Repeatable review workflows",
    "developerName": "Example Team",
    "category": "Developer Tools",
    "capabilities": ["Read", "Write"],
    "brandColor": "#10A37F",
    "composerIcon": "./assets/icon.png",
    "logo": "./assets/logo.png"
  }
}
```

每个相对路径都应从 Plugin 根目录解析并指向真实文件。发布前至少用 JSON parser 检查两个 JSON 文件，并逐项确认 manifest 声明和仓库内容一致。

## 创建方法一：使用内置 plugin-creator

最快的创建方式是使用内置 `@plugin-creator`；在 Codex 中也可以显式调用 `$plugin-creator`。给它可验证的范围，不要只说“帮我做个厉害的插件”：

```text
$plugin-creator

创建一个名为 team-review 的 Codex Plugin：
- 包含 review-pr Skill
- 只读取当前仓库，不连接外部服务
- 生成 .codex-plugin/plugin.json
- 添加 repo-scoped marketplace 条目
- 不添加 Hook 和 MCP Server
- 完成后验证目录、JSON 和相对路径
```

创建后逐项复查：

1. Plugin 名称和版本是否稳定；
2. Skill 的 `description` 是否准确描述触发场景；
3. manifest 是否声明了不需要的组件；
4. MCP 域名、工具和认证范围是否最小；
5. Hook 命令是否可审计、是否会读取敏感数据；
6. Marketplace 是 repo 还是 personal 作用域；
7. 测试是否在低权限、非生产仓库完成。

## 创建方法二：手动做一个最小 Plugin

```bash
mkdir -p my-first-plugin/.codex-plugin
mkdir -p my-first-plugin/skills/hello
```

`my-first-plugin/.codex-plugin/plugin.json`：

```json
{
  "name": "my-first-plugin",
  "version": "1.0.0",
  "description": "Reusable greeting workflow",
  "skills": "./skills/"
}
```

`my-first-plugin/skills/hello/SKILL.md`：

```md
---
name: hello
description: Greet the user and ask how you can help.
---

Greet the user warmly and ask what outcome they want.
```

到这里它只是一个 Plugin 目录。要让 App 或 CLI 发现，还要把它加入 Marketplace。

## Marketplace 是目录机制，不自动代表官方审核

Marketplace 是 JSON catalog，可以来自官方、工作区、Git 仓库或本地文件。项目默认位置：

```text
$REPO_ROOT/.agents/plugins/marketplace.json
```

个人默认位置：

```text
~/.agents/plugins/marketplace.json
```

一个 repo Marketplace 示例：

```json
{
  "name": "team-plugins",
  "interface": {
    "displayName": "Team Plugins"
  },
  "plugins": [
    {
      "name": "team-review",
      "source": {
        "source": "local",
        "path": "./plugins/team-review"
      },
      "policy": {
        "installation": "AVAILABLE",
        "authentication": "ON_INSTALL"
      },
      "category": "Developer Tools"
    }
  ]
}
```

注意：`source.path` 相对 Marketplace 根目录解析，不是相对 `.agents/plugins/` 所在目录。它应以 `./` 开头并留在 Marketplace 根内。

一个 Marketplace 可以列出多个 Plugins。Git 来源应固定 `ref` 或 `sha`；团队生产环境不要永远追随未知的 `main`。npm 来源即使不运行 lifecycle scripts，也仍需核对包作用域、版本、registry 和依赖。

## 用 CLI 管理 Marketplace

添加来源：

```bash
codex plugin marketplace add owner/repo
codex plugin marketplace add owner/repo --ref main
codex plugin marketplace add https://github.com/example/plugins.git --sparse .agents/plugins
codex plugin marketplace add ./local-marketplace-root
```

查看、更新和移除：

```bash
codex plugin marketplace list
codex plugin marketplace upgrade
codex plugin marketplace upgrade marketplace-name
codex plugin marketplace remove marketplace-name
```

`--sparse` 只用于 Git Marketplace。排查“目录写了但 Plugin 不显示”时，先用 `list` 看 Codex 实际解析了哪些来源和根路径。

## 安装、启停和卸载

在 ChatGPT 网页或桌面 App：

1. 进入支持的 Work mode 或桌面 Codex；
2. 打开 Plugins，选择 OpenAI、工作区或 Personal 来源；
3. 打开详情，检查发布者、组件、权限和外部服务；
4. 安装，需要连接器时核对 OAuth 域名和 scopes；
5. 开启新聊天或新 Codex 会话；
6. 用 `@` 显式选择 Plugin / Skill，或直接描述目标。

在 Codex CLI：

```text
codex
/plugins
```

CLI 浏览器会按 Marketplace 分组。可以安装或卸载；在已安装 Plugin 上按 Space 切换启用状态。安装完成后开启新会话。

桌面 App 会把安装内容复制到类似以下缓存路径：

```text
~/.codex/plugins/cache/$MARKETPLACE_NAME/$PLUGIN_NAME/$VERSION/
```

本地 Plugin 的版本通常是 `local`。App 读取缓存副本，不会自动读取你刚修改的源目录；更新本地内容后需要刷新目录、重新安装或重启，再开新会话验证。

## Plugin 不显示或安装后不生效：10 步排查

1. **确认入口支持。** 网页必须是 Work mode；桌面使用 Work mode 或 Codex；IDE 和移动端当前没有 Plugin 浏览器。
2. **查看 Marketplace 列表。** 运行 `codex plugin marketplace list`，记录名称、来源和根路径。
3. **解析 marketplace.json。** 确认 JSON 有效、`plugins[]` 存在、source 类型和相对路径正确。
4. **检查必需入口。** Plugin 源目录必须包含 `.codex-plugin/plugin.json`。
5. **核对 manifest。** `name`、`version`、skills、hooks、apps、mcpServers 和资源路径都应有效。
6. **更新 Git 快照。** Git Marketplace 需要时运行 `marketplace upgrade`。
7. **刷新或重启 App。** 新增 repo / personal Marketplace 后重启桌面端。
8. **处理缓存副本。** 更新本地 Plugin 后重新安装，不要只改源目录。
9. **开启新会话。** 旧会话未加载新 Skills 或工具，不等于安装失败。
10. **分离组件故障。** Skill 不触发看 description；MCP 失败看传输和认证；Hook 被跳过看信任；连接器失败看 OAuth 与权限。

不要把删除整个 `~/.codex` 当作第一步。它可能同时清除登录、配置、规则、MCP 和其他状态。先保存版本、Marketplace 列表、路径、manifest 和第一条错误，只处理已证明出问题的来源或缓存。

## 安装前安全清单

- 核对发布者、官网、仓库和 Marketplace 来源；
- 阅读 manifest 和全部 `SKILL.md`；
- 审查 Hook 命令与脚本，因为它们是可执行代码；
- 审查 `.mcp.json` 的本地命令、远程域名、工具和认证；
- 连接器 OAuth 只授予最小账号、工作区和 scopes；
- Git 来源固定可追溯 ref 或 sha；
- 测试时使用非生产仓库和低权限账号；
- 不提供 ChatGPT Session、Cookie、密码、私钥、恢复码或验证码；
- 卸载后单独撤销 OAuth、Token 和第三方服务权限。

Sandbox 与 Approval 会约束 Codex host 中的能力，但不能证明第三方服务或 Plugin 本身可信。出现在任何 Marketplace 也不自动等同 OpenAI 官方背书。

## 官方资料

- [Plugins](https://learn.chatgpt.com/docs/plugins)
- [Build plugins](https://learn.chatgpt.com/docs/build-plugins)
- [Build skills](https://learn.chatgpt.com/docs/build-skills)
- [OpenAI Plugins examples](https://github.com/openai/plugins)

## 延伸中文检查表

如果需要更细的 manifest 字段、repo/personal Marketplace 对比、Workspace 分享和缓存故障矩阵，可查看 [Codex Plugins 创建、安装与 Marketplace 中文专题](https://hi-codex.com/guides/codex-plugins-create-install-marketplace/?utm_source=github&utm_medium=guide&utm_campaign=codex_plugins)。

## 利益关系与边界说明

本文作者维护 Hi Codex，并提供独立的 ChatGPT / Codex 人工充值协助，因此与相关套餐主题存在商业利益关系。Hi Codex 与 OpenAI、任何 Marketplace、Plugin 作者或连接器服务不存在隶属、代理或官方授权关系。

本文只提供配置与安全检查，不提供共享账号、Session/Cookie、来源不明安装包、绕过审批、默认信任 Hook 或关闭安全边界的方案。
