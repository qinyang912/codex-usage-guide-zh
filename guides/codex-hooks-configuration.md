# 2026 Codex Hooks 配置指南：hooks.json、PreToolUse 与 PostToolUse

Codex Hooks 是生命周期扩展：它让一个确定性的命令处理器在会话启动、工具调用前后、上下文压缩、子代理启动或停止、回合结束等节点运行。它适合做检查、验证和补充上下文，但不是 Sandbox、审批或 Rules 的替代品。

本文核对日期为 2026-07-21。Hook 事件、工具覆盖和处理器能力仍可能随 Codex 更新，应同时核对当前官方文档与本机 `/hooks` 显示。

## 先判断是否应该使用 Hook

| 需求 | 更合适的机制 |
| --- | --- |
| 告诉 Codex 项目怎么构建、测试和交付 | `AGENTS.md` |
| 按任务选择一套可复用流程 | Skill |
| 在工具调用或回合结束时确定执行脚本 | Hook |
| 决定 Sandbox 外命令允许、询问还是禁止 | Rules |
| 限制文件、进程或网络资源 | Sandbox 与审批 |

一句话原则：自然语言工作约定放 `AGENTS.md`，按需流程放 Skill，机械执行关口放 Hook，资源和越界命令分别交给 Sandbox、审批与 Rules。

## Hooks 从哪里加载

最常用的四个位置是：

```text
~/.codex/hooks.json
~/.codex/config.toml
<repo>/.codex/hooks.json
<repo>/.codex/config.toml
```

用户级 Hooks 独立于当前项目的 trust；项目级 `.codex/` 只有在项目 trusted 时才加载。插件还能打包 `hooks/hooks.json`，管理员可以通过 `requirements.toml` 定义托管 Hooks。

多个活动来源不会互相覆盖：所有匹配 Hook 都会加载。一个配置层同时存在 `hooks.json` 与内联 `[hooks]` 时，Codex 会合并并在启动时警告。为了避免重复运行和排查混乱，通常每一层只选择一种写法。

## 一条最小 hooks.json

下面配置只观察 Shell 与统一执行工具在调用前的事件：

```json
{
  "description": "Project lifecycle checks",
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "^Bash$",
        "hooks": [
          {
            "type": "command",
            "command": "/usr/bin/python3 \"$(git rev-parse --show-toplevel)/.codex/hooks/pre_tool_use_policy.py\"",
            "timeout": 30,
            "statusMessage": "Checking Bash command"
          }
        ]
      }
    ]
  }
}
```

当前只有 `type: "command"` 处理器实际运行。`prompt`、`agent` 与异步命令 Hook 虽能被解析，但会被跳过。`timeout` 单位是秒，省略时当前默认值为 600。

命令从 Codex 会话的 `cwd` 启动。项目 Hook 不要假设当前目录一定是仓库根目录；使用明确解释器，并从 Git 根目录或绝对路径定位脚本。

## 等价的 config.toml 写法

```toml
[[hooks.PreToolUse]]
matcher = "^Bash$"

[[hooks.PreToolUse.hooks]]
type = "command"
command = '/usr/bin/python3 "$(git rev-parse --show-toplevel)/.codex/hooks/pre_tool_use_policy.py"'
timeout = 30
statusMessage = "Checking Bash command"

[[hooks.PostToolUse]]
matcher = "^Bash$"

[[hooks.PostToolUse.hooks]]
type = "command"
command = '/usr/bin/python3 "$(git rev-parse --show-toplevel)/.codex/hooks/post_tool_use_review.py"'
timeout = 30
```

Hooks 默认启用。如需关闭：

```toml
[features]
hooks = false
```

`hooks` 是当前规范 feature key；`codex_hooks` 是仍兼容但已弃用的别名。

## 事件选择速查

| 事件 | 时机 | 常见用途 | matcher 过滤对象 |
| --- | --- | --- | --- |
| `SessionStart` | 线程启动、恢复、清理或压缩后 | 加载目录级上下文 | 启动来源 |
| `SubagentStart` | 子代理启动 | 为子代理补充上下文 | 子代理类型 |
| `PreToolUse` | 支持的本地工具调用前 | 检查、阻止或改写调用 | 工具名 |
| `PermissionRequest` | 工具请求权限时 | 审核权限请求 | 工具名 |
| `PostToolUse` | 支持的本地工具结束后 | 验证结果、给出反馈 | 工具名 |
| `PreCompact` / `PostCompact` | 上下文压缩前后 | 保存或恢复必要状态 | `manual` / `auto` |
| `UserPromptSubmit` | 用户提示提交时 | 输入检查或追加上下文 | 当前不支持 matcher |
| `SubagentStop` | 子代理停止 | 检查子任务交付 | 子代理类型 |
| `Stop` | 当前回合准备停止 | 最终验证或继续提示 | 当前不支持 matcher |

`matcher` 是正则字符串，但必须先弄清它匹配什么。Shell 命令和 `exec_command` 匹配为 `Bash`；`apply_patch` 可匹配 `apply_patch`、`Edit` 或 `Write`；MCP 使用完整工具名，例如 `mcp__filesystem__read_file`。

Hosted tools（例如 WebSearch）当前不走本地函数工具 Hook 路径；特殊工具也可能选择不走默认路径。因此 Hooks 是辅助护栏，不是覆盖全部工具的强制安全边界。

## 命令处理器收到什么

命令 Hook 从 stdin 接收 JSON。常见共享字段包括：

- `session_id`：当前 Codex 会话 ID；
- `turn_id`：turn scope 事件中的当前回合 ID；
- `cwd`：会话工作目录；
- `hook_event_name`：当前事件名；
- `model`：当前模型 slug；
- `transcript_path`：会话记录路径或 null；
- `permission_mode`：部分事件提供的当前权限模式。

不同事件支持不同输出字段。不要把 Stop 的控制 JSON 原样复制给 PreToolUse；当前 PreToolUse、PermissionRequest 与 PostToolUse 的支持范围并不相同。

每条模型可见 Hook 输出当前大约限制为 2,500 tokens。超长内容可能被写入本地文件，只把首尾预览提供给模型。脚本应主动删除密钥、Session、个人数据与不必要的源码，不要把 transcript 当作稳定接口或默认上传到外部系统。

## Hooks 不执行时的排查顺序

1. 在 CLI 打开 `/hooks`，确认 Hook 是否被发现、来源和启用状态。
2. 审核并信任当前定义。非托管命令 Hook 按定义哈希记录信任，修改后会重新待审核并被跳过。
3. 确认项目 trusted；macOS、WSL、SSH、容器与 IDE host 可能读取不同的 HOME 或 `CODEX_HOME`。
4. 同一层只保留 JSON 或 TOML 一套配置，排除合并后重复执行。
5. 核对事件名、大小写和 matcher。Shell 是 `Bash`，Stop 与 UserPromptSubmit 不支持 matcher。
6. 核对解释器、脚本路径、执行权限、工作目录、超时和 Windows 命令覆盖。
7. 确认处理器类型是 command，并且没有启用当前不支持的 async。
8. 用无副作用输入检查 stdin、退出码和最小输出，不要用部署、删除或凭证访问试错。
9. 检查其他用户、项目、插件与托管 Hook；多个匹配命令处理器会并发启动，不能依赖顺序。

`--dangerously-bypass-hook-trust` 只适合已经在 Codex 外部完成来源审计的一次性自动化，不应作为日常“修复不执行”的开关。

## 官方资料与完整中文排障表

- [OpenAI：Hooks](https://learn.chatgpt.com/docs/hooks)
- [OpenAI：Advanced configuration - Hooks](https://learn.chatgpt.com/docs/config-file/config-advanced#hooks)
- [OpenAI：Rules](https://learn.chatgpt.com/docs/agent-configuration/rules)

需要完整事件矩阵、JSON/TOML 对照、输入输出字段、9 步故障排查、团队与插件治理，可继续阅读：[Hi Codex：2026 Codex Hooks 配置教程](https://hi-codex.com/guides/codex-hooks-config-pretooluse-posttooluse/?utm_source=github&utm_medium=guide&utm_campaign=codex_hooks)。

## 利益披露

本文由 Hi Codex 服务团队维护；维护者提供独立人工充值协助，因此与 ChatGPT / Codex 套餐主题存在商业利益关系。Hi Codex 与 OpenAI 不存在隶属、代理或官方授权关系。本文不要求共享账号、API Key、Cookie、Session、私有代码或 Hook 审批凭证，也不提供绕过 Sandbox、管理员要求和系统安全策略的方法。
