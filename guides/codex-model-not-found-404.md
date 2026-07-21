# Codex 404 Model not found / unsupported model 排查

> 核对日期：2026-07-21。本文根据 OpenAI Codex Models、配置文档和 openai/codex 官方仓库公开 issue 整理。

`unexpected status 404 Not Found: Model not found`、`model not supported` 和“模型不存在”不是一个固定原因。它们可能来自模型名或旧配置、账号模型可用性、官方后端故障，也可能来自 API 或自定义 provider 的地址和权限。

## 一分钟分流

先运行：

```bash
codex --version
codex login status
```

然后保留完整错误里的模型名、URL 域名、request ID、发生时间和所用界面：

| 线索 | 优先检查 |
| --- | --- |
| ChatGPT 登录，URL 指向 `chatgpt.com/.../codex/responses` | `/model` 当前列表、旧配置、账号可用性、OpenAI Status |
| OpenAI API Key | API 模型 ID、项目权限、Billing、API endpoint |
| 自定义 `base_url` 或第三方域名 | provider 模型名、wire API、权限、路径与服务状态 |
| 401 / 403 | 登录、工作区、组织策略和账号状态 |
| 429 | Usage、恢复时间、Credits 或 API 限额 |

## ChatGPT 登录：先用 /model 做对照

在交互会话中运行：

```text
/model
```

从当前账号实际显示的推荐模型中选择。启动 CLI 时可临时指定：

```bash
codex --model <模型名>
codex exec -m <模型名> "Reply with exactly OK."
```

不要根据旧教程或社交平台截图猜新模型名。OpenAI 官方文档区分 ChatGPT 登录推荐模型和 API 可用模型；某个模型出现在 API 文档中，不代表当前 ChatGPT 账号与界面一定可用。

如果同一账号、同一客户端中模型 A 正常而模型 B 404，这不像通用网络或整体认证失败。记录两次结果后继续检查模型配置和服务端状态。

## 检查 config.toml 和旧脚本

OpenAI 文档说明，ChatGPT 桌面应用、Codex CLI 与 IDE 扩展在同一环境中使用 `config.toml`。这些位置都可能固定旧模型：

- 顶层 `model = "..."`；
- profile 或项目级 `.codex/config.toml`；
- 自定义 agent；
- `--model` / `-m` 启动参数；
- 脚本、快捷方式、CI 或 IDE 工作区设置。

先备份配置，再临时移除自己添加的模型覆盖，让客户端使用推荐默认值。更新 Codex 并重启 App、CLI 或 IDE 扩展。不要删除或分享 `auth.json`。

## 模型列表里有，真实请求却 404

这可能是模型发现信息、账号灰度或实际响应后端暂时不同步。openai/codex issue #26892 记录了本地模型列表仍显示可用、Desktop 与 CLI 的实际请求却返回 404 的个案；该 issue 已关闭，但公开页面不足以证明所有类似问题的根因。

处理顺序：

1. 用当前 `/model` 中另一个推荐模型做对照；
2. 查看 OpenAI Status；
3. 搜索 openai/codex 官方仓库是否有同模型、同日期的大量报告；
4. 其他模型可用时先切换工作，不持续重装；
5. 持续失败时提供新的 request ID、时间、版本和最小复现。

不要把一次后端事件写成永久套餐规则，也不要凭个别评论断言某模型永远只向特定套餐开放。

## API Key 或自定义 provider

这时才重点核对 endpoint：

- 模型 ID 是否是该 provider 实际使用的名称；
- 当前 API project、组织或云部署是否有权限；
- `base_url` 是否符合对应服务商文档；
- wire API 是 Responses 还是 Chat Completions；
- 环境变量是否属于当前 provider。

是否需要 `/v1` 取决于 provider 的协议与 Codex 配置，不存在适用于所有 404 的统一后缀。错误 URL 不是 OpenAI 官方域名时，也不要把第三方服务状态当成 OpenAI 故障。

## 提交报告的最小信息

- 日期、时间和时区；
- 操作系统与 Codex 版本；
- App、CLI 或 IDE 扩展；
- ChatGPT、OpenAI API Key 或自定义 provider；
- 失败模型和一个成功的对照模型；
- 脱敏后的完整错误、request ID 与 cf-ray（如有）；
- 最小复现命令。

不要公开 `auth.json`、API Key、Session、设备代码、验证码、恢复码、完整环境变量或私有仓库日志。

更完整的中文分流、8 项 FAQ 与相关错误入口见：[Codex Model not found、unsupported model、404 排查](https://hi-codex.com/guides/codex-model-not-found-404/?utm_source=github&utm_medium=guide&utm_campaign=codex_model_404)。

## 官方资料

- https://developers.openai.com/codex/models
- https://developers.openai.com/codex/config-basic
- https://developers.openai.com/codex/config-advanced#custom-model-providers
- https://github.com/openai/codex/issues/26892

## 利益披露

本文由 Hi Codex 服务团队维护。维护者提供独立人工充值协助，因此与 ChatGPT / Codex 套餐主题存在商业利益关系。Hi Codex 与 OpenAI 不存在隶属、代理或官方授权关系；本文不提供第三方 API 中转、地区绕过、共享凭证或来源不明客户端。
