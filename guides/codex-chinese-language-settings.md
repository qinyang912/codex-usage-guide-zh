# 2026 Codex 中文设置：App、IDE、CLI 与不生效排查

> 更新于 2026-07-21。先说结论：Codex 的中文界面、中文回答和中文代码不是同一个开关。先确认当前使用 App、IDE 扩展还是 CLI，再调整对应设置。

## 三个入口怎么处理

| 入口 | 中文界面 | 中文回答 |
| --- | --- | --- |
| Codex App | 当前版本显示 Language 时，在 Settings → General 中选择中文 | 当前 Prompt 或 AGENTS.md |
| VS Code / Cursor / Windsurf | 编辑器设置中搜索 `chatgpt.localeOverride` | 当前 Prompt 或 AGENTS.md |
| Codex CLI | 当前官方 CLI 文档没有与 IDE 相同的中文 UI 开关 | 直接中文交互或 AGENTS.md |

菜单仍为英文，但回答已经是中文，属于 UI 本地化问题；菜单是中文、回答偶尔变成英文，更接近 Prompt 或指令作用域。不要把两种现象混在一起。

## Codex App 切换中文

1. 打开 Codex App 的 Settings。
2. 在 General 中寻找 Language 或 “Language for the app UI”。
3. 选择简体中文，重新打开设置确认已经保存。
4. 完全退出 App 后重新启动，不要只关闭当前窗口。
5. 分别检查菜单、侧边栏、设置页和聊天内容。

如果当前版本没有 Language，先确认安装的是官方 App 并完成更新。不同平台和发布阶段可能看到不同入口，不要因此改用网盘“中文绿色版”。

OpenAI Codex 官方仓库 issue #19239 记录过一种症状：`localeOverride=zh-CN` 已保存，Settings 也显示中文，但部分 UI 仍为英文。相同证据更接近客户端本地化没有应用，而不是账号、套餐或操作步骤错误。

## IDE 扩展的 localeOverride

OpenAI 当前 IDE 设置文档把 `chatgpt.localeOverride` 定义为 Codex UI 的首选语言；留空时自动检测。

操作顺序：

1. 打开编辑器 Settings。
2. 搜索 `@ext:openai.chatgpt`、`Codex` 或 `chatgpt.localeOverride`。
3. 在设置 UI 中选择简体中文。
4. 运行 Developer: Reload Window 或重启编辑器。

`chatgpt.*` 属于编辑器设置，不写进 `~/.codex/config.toml`。模型、Sandbox、MCP 等共享 Agent 行为才通过 Codex 设置或 `config.toml` 管理。

## CLI 一直用中文回答

CLI 可以直接理解中文。一次性任务可这样写：

```text
请默认使用简体中文解释。
命令、路径、代码标识符、日志和错误原文保持原样。
引用关键英文错误后，用中文说明原因、证据和下一步。
完成后用中文总结 Diff、测试结果与未验证项。
```

如果多个任务都需要，可以写入 `~/.codex/AGENTS.md`；如果只对一个仓库生效，则写入仓库根目录的 `AGENTS.md`。源码注释、用户文档和提交信息应继续遵循项目已有规范，不要因为“中文回答”就机械翻译标识符和错误原文。

## 中文设置不生效的检查顺序

1. 确认当前是 App、IDE 还是 CLI。
2. 区分菜单语言和回答语言。
3. 重新打开设置，确认值仍然保存。
4. 完全退出 App，或对 IDE 执行 Reload Window。
5. 记录客户端、扩展和 CLI 版本，再从官方渠道更新。
6. 检查 IDE User / Workspace 设置和 AGENTS.md 作用域。
7. 新建空任务做最小中文回答验证。
8. 设置已保存但 UI 仍英文时，保留版本、系统和未翻译区域，对照官方 issue。

## 为什么不把汉化补丁作为第一选择

Google 前排的一个第三方 Codex CLI 汉化工具明确写着为 CLI `0.40.0` 设计，并通过脚本注入翻译文件。本指南核对时，本机官方 CLI 已是 `0.144.1`。版本差距不代表项目一定恶意，却足以说明旧版专用工具不能被描述成当前通用方案。

第三方可执行文件或注入脚本可能修改安装文件、在更新后失效，并扩大本地供应链风险。优先顺序应是：官方 Language / localeOverride → Prompt / AGENTS.md → 官方更新与 issue → 最后才评估经过审计、版本匹配且可恢复的工具。

完整的三入口步骤、手机端表格、设置失效证据和安全清单见：[Codex 中文界面、中文回答与设置失效完整指南](https://hi-codex.com/guides/codex-chinese-language-ui-not-working/?utm_source=github&utm_medium=guide&utm_campaign=codex_chinese)。

## 来源与利益关系

本文依据 OpenAI Codex IDE settings、AGENTS.md 与 openai/codex issue #19239 核对。单个公开 issue 只证明该症状存在，不代表所有语言问题都有同一根因。

作者维护 Hi Codex，并提供用户自己 ChatGPT 账号的 Plus / Pro 独立人工充值协助，对会员主题存在商业利益；与 OpenAI、GitHub、知乎或第三方汉化项目不存在隶属、代理或官方授权关系。语言设置、版本和客户端 Bug 不能靠充值修复。
