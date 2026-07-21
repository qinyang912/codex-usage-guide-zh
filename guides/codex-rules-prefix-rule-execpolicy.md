# 2026 Codex Rules 配置指南：prefix_rule、.rules 与 execpolicy

Codex Rules 用来分类请求在 Sandbox 外运行的命令。它不是普通提示词，也不负责项目编码规范：`AGENTS.md` 说明仓库如何构建、测试和交付；Sandbox 定义文件、进程和网络边界；`approval_policy` 决定边界动作怎样审批；Rules 再按命令参数前缀返回 `allow`、`prompt` 或 `forbidden`。

OpenAI 当前把 Rules 标记为实验功能。字段和加载行为可能变化，修改前应核对当前官方文档，团队规则应像 CI 与权限配置一样接受评审。

## 一、文件位置与加载边界

用户层最常见的文件是：

```text
~/.codex/rules/default.rules
```

Codex 启动时扫描活动配置层旁边的 `rules/` 目录。受信任项目还可以提交：

```text
my-project/.codex/rules/project.rules
```

项目被标记为 untrusted 时，项目级 `.codex/` 配置、Hooks 和 Rules 会被跳过，用户与系统层仍然独立加载。修改规则后要重启 Codex，因为规则在启动时读取。

在 TUI 中把命令加入允许列表时，Codex 会把用户规则写入 `~/.codex/rules/default.rules`。Smart approvals 也可能建议一条 `prefix_rule`；建议不是安全证明，接受前必须检查前缀覆盖范围。

## 二、一条最小 prefix_rule

下面规则只匹配参数数组以 `gh pr view` 开头的命令，并在越过 Sandbox 前询问：

```python
prefix_rule(
    pattern = ["gh", "pr", "view"],
    decision = "prompt",
    justification = "查看 Pull Request 前需要确认",
    match = [
        "gh pr view 7888",
        "gh pr view --repo openai/codex",
    ],
    not_match = [
        "gh pr --repo openai/codex view 7888",
    ],
)
```

`pattern` 不是正则表达式，也不是整行字符串包含。Codex 把命令视为类似 `execvp` 接收的参数数组，参数位置或顺序变化会影响匹配。

## 三、字段与最严格决策

| 字段 | 作用 | 检查重点 |
| --- | --- | --- |
| `pattern` | 必填的非空参数前缀；单个位置可写字面值或候选集合 | 不要短到覆盖整类解释器、Shell 或网络客户端 |
| `decision` | `allow`、`prompt` 或 `forbidden`；默认 allow | 建议显式填写，避免忘记后意外允许 |
| `justification` | 审批或拒绝时解释原因 | forbidden 最好同时给出安全替代方案 |
| `match` | 必须命中的加载时测试样例 | 覆盖常见尾随参数和参数顺序 |
| `not_match` | 必须不命中的加载时测试样例 | 覆盖相似但危险的命令 |

多条规则同时匹配时，Codex 使用最严格结果：

```text
forbidden > prompt > allow
```

因此一条宽泛 allow 不能覆盖更具体的 prompt 或 forbidden。

## 四、尾随参数与候选子命令

`pattern = ["git", "status"]` 会匹配 `git status` 以及继续追加的参数，但不会匹配 `git -C repo status`。同一参数位置需要多个只读子命令时可以写：

```python
prefix_rule(
    pattern = ["git", ["status", "diff", "show"]],
    decision = "allow",
    justification = "允许只读仓库检查",
    match = ["git status --short", "git diff --stat"],
    not_match = ["git push", "git reset --hard"],
)
```

不要把 `git`、`python`、`curl`、`bash -lc` 整类设为 allow。它们能承载写入、网络、凭证读取或任意脚本，过宽规则会移除原本需要的审批边界。

## 五、Shell wrapper 与复合命令

Codex 会特别处理 `bash -lc`、`bash -c` 以及对应的 `zsh`、`sh` 调用。

当脚本只包含普通字词，并以 `&&`、`||`、`;` 或 `|` 线性连接时，Codex 可以用 tree-sitter 拆成单条命令，再分别应用 Rules。任何一条命中更严格决策，整个调用就采用该结果。

脚本包含重定向、命令替换、变量、赋值、通配符或控制流时，Codex 不尝试深入解释，而把它保守地视为整个 Shell wrapper 调用。不要为了减少一次提示就允许所有 `bash -lc`。

## 六、用 execpolicy check 验证

不要通过真实部署、删除或强制推送试错。使用官方当前提供的实验性检查命令：

```shell
codex execpolicy check --pretty \
  --rules ~/.codex/rules/default.rules \
  -- gh pr view 7888 --json title,body,comments
```

输出 JSON 会显示最严格决策、命中的规则和 justification。多个规则文件可重复添加 `--rules`。

最低测试矩阵应包括：最短合法命令、常见尾随参数、参数顺序变化、相似危险命令、Shell 复合命令和多规则组合。`match` 与 `not_match` 只是规则加载时的内联测试，不会执行其中的命令。

## 七、不生效时按顺序排查

1. 确认问题确实是 Sandbox 外命令审批，而不是 OS permission denied、只读挂载或网络配置。
2. 确认实际 host、`CODEX_HOME` 和 `.rules` 文件位置。
3. 编辑后重启 Codex。
4. 项目级规则要确认项目处于 trusted 状态。
5. 检查真实参数数组、包装器、`-C`、参数顺序和尾随参数。
6. 检查是否有更严格的用户、团队或管理员规则同时匹配。
7. 同时传入实际加载的规则文件运行 `codex execpolicy check`。

Rules 不能扩大 Sandbox、打开所有网络或绕过管理员 requirements。团队审查时要重点关注：decision 从 prompt 改为 allow、pattern 被缩短、not_match 被删除、Shell/解释器/发布工具被整类允许，以及没有负责人和回滚说明的项目规则。

## 官方资料与完整中文教程

- [OpenAI：Rules](https://learn.chatgpt.com/docs/agent-configuration/rules)
- [OpenAI：Agent approvals & security](https://developers.openai.com/codex/agent-approvals-security)
- [OpenAI：Custom instructions with AGENTS.md](https://learn.chatgpt.com/docs/agent-configuration/agents-md)

需要完整字段表、allow/prompt/forbidden 示例、Shell 拆分矩阵、execpolicy 测试清单、团队评审和 FAQ，可继续阅读：[Hi Codex：2026 Codex Rules 配置教程](https://hi-codex.com/guides/codex-rules-prefix-rule-execpolicy/?utm_source=github&utm_medium=guide&utm_campaign=codex_rules)。

本文核对日期为 2026-07-21。Rules 当前仍为实验功能，应以最新官方文档和实际客户端行为为准。

## 利益披露

本文由 Hi Codex 服务团队维护；维护者提供独立人工充值协助，因此与 ChatGPT / Codex 套餐主题存在商业利益关系。Hi Codex 与 OpenAI 不存在隶属、代理或官方授权关系。本文不要求共享账号、API Key、Cookie、Session、私有代码或审批凭证，也不提供绕过 Sandbox、管理员要求和系统安全策略的方法。
