# Codex Chrome 插件、内置 Browser 与 Computer Use：安装和不可用排查

更新日期：2026-07-21

搜索“Codex 浏览器插件”时，经常会看到 Chrome Extension、Browser、Computer Use、IDE Extension 和 Plugins 五个名字。它们不是同一个组件。先选错入口，再执行重装、删缓存或换网络，通常只会增加新的变量。

这份指南先给出最短分流，再按官方支持链路处理安装、插件不显示、未连接、`missing native host`、`Computer Use plugins unavailable` 和 Chrome Web Store“此商品无法购买或下载”。

## 一分钟选择正确入口

| 你的任务 | 应选入口 | 关键区别 |
| --- | --- | --- |
| 操作已经登录账号的现有 Chrome 标签页 | `@Chrome` | 使用安装扩展的 Google Chrome Profile |
| 调试 localhost、预览网页、与日常 Chrome 隔离 | `@Browser` | 使用 ChatGPT 桌面 App 内的独立浏览器资料 |
| 点击 macOS / Windows 桌面 App、跨窗口工作 | `@Computer` 或 `@AppName` | 控制桌面 GUI，需要系统和 App 权限 |
| 在 VS Code、Cursor、Windsurf 内使用 Codex | IDE Extension | 不负责控制 Chrome |
| 安装 Skills、连接器、MCP、Hooks 的能力包 | Codex Plugins | 是分发机制，不是浏览器本身 |

两个最容易混淆的结论：

1. 内置 Browser 默认不会自动共享你日常 Chrome 的登录状态、标签页或浏览资料。
2. Computer Use 可以操作浏览器窗口，但它不是 Chrome Extension；Chrome 正常不代表桌面 GUI 权限正常，反过来也一样。

## 安装 Codex / ChatGPT Chrome Extension

当前官方流程从 ChatGPT 桌面 App 开始：

1. 在桌面 App 中选择 ChatGPT 并切换 Work mode，或选择 Codex。
2. 打开 Plugins Directory，找到并安装 **Chrome**。
3. 跟随设置流程打开 Chrome Web Store。
4. 核对官方扩展 ID：`hehggadaopoacecdllhhajmbjkdcmajg`。
5. 审核 Chrome 权限并完成安装。
6. 在 Chrome 工具栏或 Extensions 菜单中打开 ChatGPT，确认 side chat 能加载。
7. 回到桌面 App，新建 Work mode / Codex 聊天，用一个无敏感写操作的小任务调用 `@Chrome`。

例如：

```text
@Chrome 打开 example.com，只读取页面标题，然后停止。
```

当前公开文档明确支持 Google Chrome。Edge、Brave 等 Chromium 浏览器即使能手动加载扩展，也不属于官方承诺的支持路径，不能据此判断 native host、自动更新和 Codex 集成一定正常。

## 权限怎么给才稳妥

Chrome Extension 需要足够权限才能操作网页。Chrome 的安装提示可能包含网站数据、调试页面、浏览历史、下载、书签、通知、标签组以及与本机应用通信等能力。

建议按最小范围授权：

- 首次访问新站点先用 **Allow once**。
- 只有长期可信且确实反复使用的域名，再选 **Allow for this site**。
- 不要为了省确认直接选择所有网站长期允许。
- 浏览历史是单独的敏感权限；官方说明历史记录访问没有 always-allow 选项。
- 付款、提交、删除、权限调整、账号安全和 MFA 操作应保持人在场。
- 不需要任务读取的敏感网站、邮箱和后台标签页先关闭。

如果需要上传本机文件，在 `chrome://extensions/` 中打开该扩展详情，按官方说明开启 **Allow access to file URLs**，然后重新开始任务。

## Chrome Plugin 不显示或找不到

按下面顺序排查，不要先删系统目录：

1. 确认你在 ChatGPT 桌面 App，而不是 Codex CLI 或 VS Code Marketplace。
2. 更新桌面 App；安装过多个 ChatGPT / Codex App 时，更新每一份或移除不用的旧副本。
3. 使用 ChatGPT Work mode 或 Codex，并打开新聊天。
4. 在 Plugins 中确认 Chrome 已安装且开关开启。
5. 确认当前使用 Google Chrome。
6. 如果界面明确提示“组织禁用”或“地区不可用”，本地重装无法覆盖工作区或服务端策略。
7. 仍不显示时运行 `/feedback`，提供 App 版本、系统、入口截图、聊天 ID 和原始错误；不要上传密码、Cookie、Token 或 Session。

## 未连接、missing native host 怎么办

`missing native host` 指浏览器扩展与桌面 App 的本机通信链路没有正常建立。官方排查顺序可以压缩成六步：

1. 在 Settings → Computer Use → Google Chrome → Manage 中确认目标网站没有被 blocklist 阻止。
2. 关闭 Chrome side panel，重启 Chrome，再从工具栏重新打开扩展。
3. 如果 side chat 本身无法加载或提示 native host 缺失，在桌面 App 的 Plugins 中移除 Chrome，再按官方流程重新添加。
4. 确认当前 Chrome Profile 就是安装并启用扩展的 Profile。
5. 新建 Work mode / Codex 聊天，再调用 `@Chrome`。
6. 重启桌面 App；最后才卸载扩展、移除 Plugin，并从官方入口完整重装。

多 Profile 是高频原因：扩展装在 Profile A，却在 Profile B 打开任务，看起来像“已经安装”，实际当前资料里没有可用扩展。

## “此商品无法购买或下载”怎么办

这类错误来自 Chrome Web Store 可用性，而不是普通的“扩展开关关闭”。截至更新日，OpenAI Codex 公共 Issue [#21700](https://github.com/openai/codex/issues/21700) 仍记录 Windows 和 macOS 用户遇到商店不可用、账号或地区差异。部分用户后来恢复，另一些仍不可见，因此不能把某个网络或账号上的成功经验当作通用方案。

安全处理方式：

1. 确认从桌面 App 的 Chrome Plugin 设置流程进入商店，并核对扩展 ID。
2. 记录原始错误是否写着组织禁用、地区不可用或商品不可用。
3. 检查 Chrome / Google 账号和组织是否禁止扩展安装。
4. 当前任务不依赖现有 Chrome 登录态时，用 `@Browser` 完成公共网页或 localhost 工作。
5. 通过 `/feedback` 或官方 Support 提交地区、账号类型、App 和 Chrome 版本，等待受支持分发恢复。

不要从 Issue 评论、网盘、对象存储、论坛或“CRX 下载站”安装离线包。评论者给出的 SHA-256 不等于 OpenAI 官方签名或发布链。浏览器扩展能读取网站、历史记录和调试页面时，来源不明的离线包风险尤其高。

## Computer Use plugins unavailable

Computer Use 是 macOS / Windows 桌面 GUI 控制能力。先确认它与 Chrome 连接问题是不是两件事：

1. 在支持地区的 ChatGPT 桌面 App 中选择 Work mode 或 Codex。
2. 打开 Plugins → Computer Use，按界面安装或启用 Plugin。
3. 打开 Computer Use server 和 skill 开关，再选择 Try now。
4. 在 Settings → Computer Use 中检查目标 App 权限和已连接浏览器。
5. macOS：在 Privacy & Security 中给 Codex Computer Use 开启 Screen Recording 和 Accessibility。
6. Windows：让目标 App 保持在当前活动桌面可见；Computer Use 会接管前台鼠标和键盘。
7. 受管设备：让管理员确认 `computer_use`、`browser_use`、`browser_use_external`、`in_app_browser` 与完整 CDP 是否被策略关闭。

macOS 上“看不见窗口”更接近 Screen Recording，“看得见但不能点”更接近 Accessibility。Windows 上目标窗口不在活动桌面，或用户同时抢占同一桌面的输入，也会让测试结果失真。

Computer Use 不能替你批准系统隐私权限、以管理员身份认证，也不能自动化终端 App 或 ChatGPT 本身。这些限制不是缓存损坏。

## 内置 Browser 不可用

内置 Browser 在 ChatGPT 网页和桌面 App 可用，但 Browser 控制不提供给 Codex CLI 或 IDE Extension。桌面 App 中：

1. 使用 Work mode 或 Codex。
2. 打开 Plugins Directory，安装 Browser。
3. 新建聊天并调用 `@Browser`。
4. 需要账号时在内置 Browser 里单独登录。

管理员可以禁用内置 Browser、外部 Browser Use 或完整 CDP。Developer mode 被禁用，只说明高级调试访问受限，不必然表示普通浏览也完全不可用。

## 不要采用的高风险修复

- 从第三方下载 CRX / ZIP，再以开发者模式加载。
- 修改 Chrome 注册表强制安装扩展。
- 删除 WindowsApps、bundled plugin cache 或 native messaging 目录。
- 复制别人的 native host 配置、Cookie、Session 或浏览器资料。
- 以“官方 Issue 里有人回复”为理由运行未知 PowerShell 脚本。
- 直接给所有站点永久权限。
- 用换网络、换账号或改地区的单次成功，推导稳定、合规、受支持。

如果官方文档没有要求修改系统目录，就先完成可逆的版本、模式、Plugin 开关、Profile、站点权限、新聊天、重启和官方反馈链路。

## 六步验收清单

1. Chrome 工具栏能打开 ChatGPT side chat。
2. 当前 Profile 的扩展页面显示扩展已启用。
3. 桌面 App 的 Chrome Plugin 已开启。
4. 新 Work mode / Codex 聊天能识别 `@Chrome`。
5. 对普通网页 Allow once 后能读取标题。
6. 提交、购买、删除或权限变更仍会请求确认。

1 失败，优先处理扩展或 native host；1 成功、3 失败，处理桌面 Plugin；1–4 成功但单个网站失败，检查站点权限；Chrome 正常而桌面 App 控制失败，转到 Computer Use 系统权限。

## 官方参考

- [Chrome extension](https://learn.chatgpt.com/docs/chrome-extension)
- [Built-in Browser](https://learn.chatgpt.com/docs/browser)
- [Computer Use](https://learn.chatgpt.com/docs/computer-use)
- [OpenAI Codex Issue #21700](https://github.com/openai/codex/issues/21700)

更完整的中文入口对照、错误原文表、权限说明和逐层诊断见：[Codex Chrome 插件安装与 Computer Use 不可用排查](https://hi-codex.com/guides/codex-chrome-extension-browser-computer-use/?utm_source=github&utm_medium=guide&utm_campaign=codex_chrome_browser)。

## 利益关系说明

本文为独立中文技术整理。作者维护的 Hi Codex 提供 ChatGPT / Codex 套餐相关人工充值协助，因此与套餐主题存在商业利益关系；与 OpenAI、Google、Chrome Web Store 或任何插件作者不存在隶属、代理或官方授权关系。充值不能解除地区、账号、工作区、浏览器扩展或管理员策略限制。
