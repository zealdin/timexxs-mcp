# timexxs-mcp

[![M8ven Score](https://m8ven.ai/badge/mcp/zealdin-timexxs-mcp-1lyxtq?v=627009f765b2b6e39b86ffe6ce36da50)](https://m8ven.ai/mcp/zealdin-timexxs-mcp-1lyxtq?s=readme)

省点时间 MCP 开放平台的公开文档仓库（仅文档，不含服务端代码）。 · Public docs for the Timexxs (省点时间) remote MCP servers. Docs only, no server code.

[中文](#中文) · [English](#english)

---

## 中文

### 简介
`mcp.timexxs.com` 是一个托管的远程 MCP 网关（Streamable HTTP，JSON-RPC 2.0），按产品线分为三个端点：

| 端点 | 产品 | 用途 |
|---|---|---|
| `https://mcp.timexxs.com/kgc` | 快工场 | AI 内容创作：多平台文案改写、AI 生图、配音、文生视频、一键成片、违禁词质检等 |
| `https://mcp.timexxs.com/ks` | 快视 | 视频号矩阵管理：多账号管理、发布与定时排程、私信/评论自动回复、数据看板 |
| `https://mcp.timexxs.com/kf` | 快发 | 多平台（抖音/快手/小红书等）运营数据与自动回复任务管理（数据通道；发布在快发桌面端完成） |

用户使用自己的省点时间统一账号授权、使用自己账号下的额度，接入方无需持有任何密钥。

### 认证
- OAuth 2.1 授权码 + PKCE；支持 RFC 8414 / RFC 9728 元数据发现与 RFC 7591 动态客户端注册，主流客户端填入 URL 即可自动完成授权。
- 授权服务器元数据：`https://mcp.timexxs.com/.well-known/oauth-authorization-server`（issuer `https://id.timexxs.com`）
- 资源元数据：`https://mcp.timexxs.com/.well-known/oauth-protected-resource/{kgc|ks|kf}`
- 不支持浏览器授权的客户端可使用个人访问令牌（在 https://id.timexxs.com/consents 自助创建），放在 `Authorization: Bearer` 头中。

### 权限范围（scopes）
| Scope | 说明 |
|---|---|
| `kgc.read` | 快工场只读工具 |
| `kgc.generate` | 快工场生成类工具（消耗账号算力点） |
| `ks.read` | 快视只读工具 |
| `ks.write.material` | 导入/删除快视素材 |
| `ks.publish` | 发布视频、取消定时任务 |
| `ks.write.settings` | 自动回复规则、分组、通知等设置 |
| `ks.write.content` | 管理已发布作品（可见范围、置顶、删除等） |
| `kf.read` / `kf.write` | 快发数据读取 / 设置修改 |
| `offline_access` | 需要刷新令牌时显式申请 |

### 客户端配置
直接使用远程 URL，首次调用时客户端会自动打开浏览器完成授权。示例见下方 [Client configuration](#client-configuration)（配置在中英文下相同）。

### 工具列表
工具清单见下方 [Tools](#tools)。部分工具需要对应产品的会员或消耗算力点，具体以产品内说明为准。

### 链接
- 接入指南：https://id.timexxs.com/connect
- 文档：https://id.timexxs.com/docs
- 机器可读说明：https://mcp.timexxs.com/llms.txt

---

## English

### Overview
`mcp.timexxs.com` is a hosted remote MCP gateway (Streamable HTTP, JSON-RPC 2.0) with one endpoint per product line:

| Endpoint | Product | What it does |
|---|---|---|
| `https://mcp.timexxs.com/kgc` | Kuaigongchang (快工场) | AI content creation: multi-platform copy rewriting, image generation, TTS, text-to-video, one-click video assembly, risk-word checks |
| `https://mcp.timexxs.com/ks` | Kuaishi (快视) | Short-video account matrix management: multi-account management, publishing and scheduling, DM/comment auto-reply, analytics |
| `https://mcp.timexxs.com/kf` | Kuaifa (快发) | Multi-platform (Douyin / Kuaishou / Xiaohongshu, etc.) analytics and auto-reply task management. Data lane only; publishing happens in the Kuaifa desktop app |

End users sign in with their own Timexxs account and use their own quota; integrators never hold keys.

### Authentication
- OAuth 2.1 authorization code + PKCE, with RFC 8414 / RFC 9728 discovery and RFC 7591 open dynamic client registration. Unauthenticated requests return `401` with a `WWW-Authenticate: Bearer resource_metadata=...` header, so compliant clients complete sign-in automatically.
- Authorization server metadata: `https://mcp.timexxs.com/.well-known/oauth-authorization-server` (issuer `https://id.timexxs.com`)
- Protected resource metadata: `https://mcp.timexxs.com/.well-known/oauth-protected-resource/{kgc|ks|kf}`
- For clients that cannot run the browser flow, use a Personal Access Token (self-issued at https://id.timexxs.com/consents) in the `Authorization: Bearer` header. Same scopes as OAuth.

### Client configuration

**Cursor** (`~/.cursor/mcp.json`)
```json
{
  "mcpServers": {
    "timexxs-kgc": { "url": "https://mcp.timexxs.com/kgc" },
    "timexxs-ks":  { "url": "https://mcp.timexxs.com/ks" },
    "timexxs-kf":  { "url": "https://mcp.timexxs.com/kf" }
  }
}
```

**Claude Desktop / Claude.ai** — Settings → Connectors → Add custom connector → URL `https://mcp.timexxs.com/kgc` (repeat for `/ks`, `/kf`). For older builds without remote connectors, use the `mcp-remote` bridge:
```json
{
  "mcpServers": {
    "timexxs-kgc": { "command": "npx", "args": ["-y", "mcp-remote", "https://mcp.timexxs.com/kgc"] }
  }
}
```

**Cherry Studio** — Settings → MCP Servers → Add → Type `Streamable HTTP (streamableHttp)` → URL `https://mcp.timexxs.com/kgc`.

**VS Code (GitHub Copilot)** (`.vscode/mcp.json`)
```json
{
  "servers": {
    "timexxs-kgc": { "type": "http", "url": "https://mcp.timexxs.com/kgc" }
  }
}
```

**Clients without browser OAuth (e.g. Trae, WorkBuddy)** — create a Personal Access Token at https://id.timexxs.com/consents and send it as a header:
```json
{
  "mcpServers": {
    "timexxs-kgc": {
      "url": "https://mcp.timexxs.com/kgc",
      "headers": { "Authorization": "Bearer <YOUR_PERSONAL_ACCESS_TOKEN>" }
    }
  }
}
```

### Tools
Tool descriptions are in Chinese, as returned by the server. Some tools require a paid membership of the related product or consume account credits.

#### 快工场 / kgc
| Tool | 说明 |
|---|---|
| `list_accounts` | 已绑定的各平台发布账号列表 |
| `list_my_works` | 我的成片/作品列表(发布的素材源) |
| `list_materials` | 素材库(图片/视频) |
| `list_copywriting` | 文案库列表 |
| `get_data_overview` | 发布数据概览(播放/点赞/评论等) |
| `get_point_balance` | 算力点余额查询 |
| `list_image_models` | 可用的 AI 生图模型 |
| `task_status` | 异步生成任务的状态与结果 |
| `publish_status` | 发布任务进度 |
| `check_risk_words` | 文案违禁词/风险词质检 |
| `text_rewrite` | 一稿多平台改写(抖音/小红书/B站等原生风格) |
| `ai_generate_image` | AI 生图 |
| `text_to_speech` | 文本配音(TTS,多音色) |
| `video_remake` | 爆款视频复刻(链接→同款成片) |
| `video_oneclick` | 一键成片(素材+文案→成片) |
| `video_from_text` | 文生视频(一句话直出视频) |
| `video_download` | 视频链接提取(去水印下载) |
| `extract_video_text` | 提取视频文案(ASR) |
| `video_split` | 视频拆分(长视频切段) |
| `save_copywriting` | 保存文案到文案库(生成流收尾,免费) |

#### 快视 / ks
| Tool | 说明 |
|---|---|
| `ks_whoami` | 当前令牌代表哪个快视账号(用户码/手机号尾号/视频号数) |
| `ks_list_accounts` | 已绑定的视频号列表(含在线状态、分组) |
| `ks_get_account_health` | 视频号健康度(处罚状态/限流/被罚作品) |
| `ks_list_publish_files` | 发布文件库分页列表(发布链路消费的文件) |
| `ks_list_materials` | 素材库(网页端「素材库」菜单同源,含分组/标签) |
| `ks_import_material_by_url` | 视频直链导入素材库,返回 materialId 与 coverUrl |
| `ks_begin_material_upload` | 本地视频导入第 1 步:领预签名上传地址 |
| `ks_finish_material_upload` | 本地视频导入第 2 步:验收注册,返回 materialId 与 coverUrl |
| `ks_delete_material` | 删除素材 |
| `ks_precheck_content` | 发布前文案合规预检(每次消耗一次 AI 调用) |
| `ks_publish_video` | 发布视频到视频号(支持定时),返回 taskId |
| `ks_batch_publish_video` | 批量发布(文件夹排程):整批一个任务,逐条独立文案与排期 |
| `ks_get_publish_task` | 查询发布任务结果 |
| `ks_list_scheduled` | 即将发布的排程(跨任务摊平,按时间升序) |
| `ks_list_publish_tasks` | 发布任务列表 |
| `ks_cancel_publish_task` | 取消尚未发出的定时任务 |
| `ks_get_dashboard` | 账号互动数据看板 |
| `ks_get_video_stats` | 单条作品数据 |
| `ks_get_weekly_report` | 账号周报 |
| `ks_chat_auto_reply` | 私信自动回复总开关(按视频号,get/set) |
| `ks_reply_rules` | 私信自动回复规则(list/save/delete/copy) |
| `ks_message_reply_settings` | 含昵称回复设置(旗舰/企业版) |
| `ks_comment_rules` | 评论自动回复规则(list/save/delete) |
| `ks_comment_block` | 评论屏蔽词与拉黑开关 |
| `ks_quick_replies` | 快捷回复库 |
| `ks_notification_settings` | 通知项、webhook 与三个总开关 |
| `ks_monitor_list` | 热度提醒名单 |
| `ks_hidden_list` | 隐藏设置名单(含定时隐藏) |
| `ks_account_settings` | 账号别名 / 排序 / 启用停用 |
| `ks_manage_groups` | 客户分组(list/create/update/delete/assign/sort) |
| `ks_fingerprint` | 登录指纹/城市(只读) |
| `ks_reply_stats` | 自动回复报表 / 私信消息统计(含逐小时明细) |
| `ks_outreach_stats` | 主动私信 / 潜客激活任务与日志 |
| `ks_hook_funnel` | 钩子转化漏斗 |
| `ks_fans_trend` | 粉丝趋势(旗舰版+) |
| `ks_accounts_daily_data` | 矩阵日数据(企业版,逐号实时查) |
| `ks_finder_metrics` | 单号互动指标与粉丝曲线 |
| `ks_list_posts` | 作品列表(含 objectId/exportId) |
| `ks_account_insight` | 账号洞察大报告(快工场 AI) |
| `ks_comment_cluster` | 评论聚类(快工场 AI) |
| `ks_ai_lead_scan` | 线索扫描(快工场 AI) |
| `ks_notifications` | 视频号通知中心(list/read) |
| `ks_shop_orders` | 带货订单 |
| `ks_team_members` | 企业成员(只读) |
| `ks_set_post_visibility` | 作品可见范围 |
| `ks_post_actions` | 作品置顶 / 取消置顶 / 批量声明 |
| `ks_delete_posts` | 删除作品(不可恢复) |

#### 快发 / kf
| Tool | 说明 |
|---|---|
| `kf_list_accounts` | 快发绑定的多平台账号(抖音/快手/小红书等,含在线状态) |
| `kf_data_overview` | 多平台运营数据总览 |
| `kf_list_works` | 作品数据(按日期/平台/账号筛选,分页) |
| `kf_account_trend` | 账号数据趋势 |
| `kf_interact_tasks` | 评论/私信自动回复任务(list/save/delete/start/stop/history) |
| `kf_account_overview` | 账号数据(按平台/关键词/时间) |
| `kf_dashboard_trend` | 总览趋势 |
| `kf_monitors` | 数据监控:作品/账号/播放流速 |
| `kf_leads` | 客资线索(list/update-status/delete/empty/config) |
| `kf_settings` | 用户设置与 webhook |
| `kf_sph_diag_settings` | 视频号诊断设置 |
| `kf_manage_groups` | 账号分组 |
| `kf_update_account` | 账号别名 / 分组 / 启用停用 |
| `kf_interact_quota` | 互动配额 |
| `kf_notifications` | 通知(list/read/read-all/delete) |
| `kf_devices` | 设备(list/kick/offline) |
| `kf_sync_data` | 触发账号/作品数据同步 |
| `kf_xhs` | 小红书:@提及 / 自动关注配置 / 关注日志 |
| `kf_tags` | 话题标签 |
| `kf_publish_defaults` | 发布标签与默认发布参数 |
| `kf_fingerprint` | 指纹(只读) |
| `kf_audit_log` | 团队操作审计(仅管理员) |
| `kf_vip_info` | 会员信息与套餐 |
| `kf_team_members` | 团队成员(只读) |

### Rate limits & safety
- Per-token rate limits per bucket (e.g. `ks:read` 60/min, `ks:publish` 10/min, `kf:read` 60/min); exceeding returns `RATE_LIMITED`.
- kgc: balance precheck, per-client daily spend cap, two-step confirmation (`CONFIRM_REQUIRED` + `confirm_token`) for expensive calls, optional `idempotency_key`.
- ks: always pass `idempotencyKey` to `ks_publish_video` to avoid duplicate posts on retry.

### Links
- Connect guide: https://id.timexxs.com/connect
- Docs: https://id.timexxs.com/docs
- Machine-readable: https://mcp.timexxs.com/llms.txt

## License
MIT — applies to the documentation in this repository.
