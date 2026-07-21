# Codex CLI 登录失败：401、403、OAuth 与 API Key 排查

> 核对日期：2026-07-21。本文根据 OpenAI Codex Authentication、CLI 与 developer commands 官方文档整理。

Codex CLI 登录失败时，最重要的不是立刻重装或更换账号，而是先确认失败发生在哪一步：终端是否启动登录、浏览器是否完成授权、凭证是否返回 CLI、账号是否有工作区权限，还是登录成功后的首次请求被拒绝。

## 先确认使用哪种认证

本地 Codex CLI 支持两种主要方式：

- **Sign in with ChatGPT**：使用 ChatGPT 账号和套餐包含的 Codex 权益，浏览器完成 OAuth 授权。
- **API Key**：使用 OpenAI Platform API Key，按 API 标准费率单独计费。

Codex cloud 要求使用 ChatGPT 登录。CLI 和 IDE 扩展会共享缓存的登录信息，因此从其中一个退出后，另一个在下次启动时也可能要求重新登录。

先执行：

```bash
codex login status
codex --version
codex doctor
```

`codex login status` 用于确认当前认证状态；`codex doctor` 会检查安装、配置、认证、运行环境、Git 和终端等信息。分享诊断结果前仍应人工隐藏用户名、路径、仓库信息和其他敏感资料。

## 浏览器成功，终端仍等待

ChatGPT 登录会打开浏览器，完成授权后还需要把结果返回 CLI。WSL、SSH、容器、远程终端、回环地址或浏览器回调受阻时，网页可能提示成功，终端却没有继续。

可以停止当前登录，再使用官方设备授权方式：

```bash
codex login --device-auth
```

按官方页面显示的步骤完成授权。设备代码也是临时敏感凭证，不要发送给他人。

## 401、403 和 429 的区别

| 提示 | 优先检查 | 不建议的做法 |
| --- | --- | --- |
| 401 Unauthorized | 当前认证方式、凭证是否过期、CLI 与浏览器账号是否一致 | 复制别人电脑的 auth.json |
| 403 Forbidden | 工作区权限、组织策略、强制登录方式、地区支持、账号通知 | 把 403 一律认定为封号 |
| 429 Too Many Requests | ChatGPT Codex Usage、恢复时间、Credits 或 API 限额 | 反复 logout/login |

403 不自动等于账号被封。管理员可以通过配置强制登录方式或 ChatGPT 工作区；策略不匹配时，Codex 可能退出或拒绝继续。只有账号收到明确 Deactivated 通知时，才应进入账号停用申诉分支。

## 做一次受控重登

确认 OpenAI 状态页没有大面积异常后，可以保存错误信息，再做一次完整重登：

```bash
codex logout
codex login
codex login status
```

不要在短时间内连续重复授权。若同一错误在最新客户端、普通 OAuth 和 device auth 后仍稳定复现，应保留发生时间、操作系统、Codex 版本、认证方式和已经脱敏的完整错误，再联系官方支持或工作区管理员。

## API Key 登录

官方 CLI 支持从标准输入接收 API Key：

```bash
printenv OPENAI_API_KEY | codex login --with-api-key
```

不要把真实 Key 写进教程、命令历史、截图或仓库。API Key 登录使用 Platform API 独立账单，不能用来判断 Plus 或 Pro 套餐是否生效。

## auth.json 安全

Codex 凭证可能保存在操作系统凭证存储或 `~/.codex/auth.json`。官方文档明确要求把 auth.json 当作密码保护，因为文件可能包含访问令牌：

- 不提交到 Git；
- 不上传网盘或工单；
- 不粘贴到群聊、论坛或远程排障页面；
- 不从陌生来源下载“现成登录文件”。

如果怀疑凭证已经泄露，应停止共享、退出当前凭证并按官方流程重新登录，同时检查账号和 API 用量是否异常。

## 官方来源

- [Codex Authentication](https://developers.openai.com/codex/auth/)
- [Codex CLI](https://developers.openai.com/codex/cli/)
- [Codex developer commands](https://developers.openai.com/codex/developer-commands?surface=cli)

更完整的中文错误阶段表、FAQ 和相关账号分支见：[Codex CLI 登录失败怎么办？](https://hi-codex.com/guides/codex-cli-login-failed-401-403/?utm_source=github&utm_medium=guide&utm_campaign=codex_login_errors)

## 利益披露

本文由 Hi Codex 服务团队维护。维护者提供独立人工充值协助，因此与 ChatGPT 套餐和第三方充值主题存在商业利益关系；Hi Codex 与 OpenAI 不存在隶属、代理或官方授权关系。本文不收集也不要求用户提供密码、API Key、Session、auth.json、设备代码或验证码。
