# Codex CLI 登录失败：401、403、OAuth 与 device auth 排查

更新日期：2026-07-21。

`401 Unauthorized`、`403 Forbidden`、`Token exchange failed` 和“浏览器显示成功但终端仍在等待”不一定是同一个故障。先保留完整错误、发生时间、Codex 版本和运行环境，再判断失败发生在浏览器验证、凭证回调、令牌交换，还是首次模型请求。

## 先确认当前认证方式

```bash
codex login status
codex --version
```

本地 Codex CLI 可以使用 ChatGPT 登录，也可以使用 API Key。ChatGPT 登录使用套餐访问；API Key 由 OpenAI Platform 按标准 API 费率单独计费。两套登录方式和账单不能混为一谈。

需要生成本地诊断报告时可以运行：

```bash
codex doctor
```

报告可能包含本地环境信息，公开前仍要人工检查并脱敏。

## 受控重登顺序

确认 OpenAI 状态页没有大面积异常后，只做一次受控重登：

```bash
codex logout
codex login
codex login status
```

Codex CLI 与 IDE 扩展会复用缓存的登录信息；从其中一个退出后，另一个下次启动也需要重新登录。不要通过下载或交换他人的 `auth.json` 来“修复”登录。

## 浏览器成功，终端为什么还在等待

标准登录会打开浏览器，并通过本地回调把凭证返回 Codex。WSL、SSH、容器、远程终端或本地回环回调不可达时，浏览器可能显示成功，终端却没有完成。

官方当前建议在这类环境优先尝试 device code 登录：

```bash
codex login --device-auth
```

设备代码是临时敏感信息，不要发到公开聊天、工单截图或第三方网页。

## 401、403 和 429 分流

| 现象 | 优先检查 | 不应直接推断 |
| --- | --- | --- |
| 401 Unauthorized | 当前认证方式、缓存凭证、受控重登结果 | 账号一定被封 |
| 403 Forbidden | 工作区权限、管理员限制、账号通知、支持地区和服务状态 | 只要换账号或改地区就能解决 |
| 429 / Usage limit | ChatGPT Usage、恢复时间、Credits 或 API Billing | 登录凭证已经失效 |
| 浏览器成功、终端等待 | 回调环境、WSL / SSH / 容器、device auth | 必须复制别人的认证文件 |

企业工作区还可能强制 ChatGPT 或 API 登录，或限制指定工作区。当前凭证不符合管理员策略时，Codex 会退出；这类问题应由工作区管理员核对席位、SSO、角色和允许的认证方式。

## 凭证安全

Codex 登录信息可能存放在系统凭证库，也可能保存在 `~/.codex/auth.json`。官方文档明确要求把该文件当作密码处理，因为它包含访问令牌：

- 不提交到 Git；
- 不粘贴到工单、群聊或远程排障网站；
- 不把整个 `~/.codex` 目录复制给陌生人；
- 怀疑泄露时退出登录、重新认证并检查账号安全。

更完整的中文阶段表、支持材料清单与 FAQ： [Codex CLI 登录失败、401、403 与 OAuth 排查](https://hi-codex.com/guides/codex-cli-login-failed-401-403/?utm_source=github&utm_medium=guide&utm_campaign=codex_cli_login)。

## 官方资料

- [OpenAI Codex Authentication](https://developers.openai.com/codex/auth)
- [OpenAI Codex developer commands](https://developers.openai.com/codex/developer-commands?surface=cli)
- [OpenAI Status](https://status.openai.com/)

## 利益披露

本文由 Hi Codex 服务团队维护；维护者提供独立人工充值协助，因此与 ChatGPT / Codex 套餐主题存在商业利益关系。Hi Codex 与 OpenAI 不存在隶属、代理或官方授权关系。本文不提供地区绕过、账号买卖、共享凭证或验证码代收方法。
