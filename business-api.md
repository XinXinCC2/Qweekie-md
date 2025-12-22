# Qweekle 验票业务接口（精简版）

## 全局配置
- Base URL:
  - https://nextapi.qweekle.at/api
  - https://api.qweekle-beta.com/api
- 认证与必填 Header:
  - Authorization: Bearer <token>
  - tenant: <tenant_id>
  - venture: <venture_id>
  - pos: <pos_id>（部分验票场景需要）
- 说明:
  - OpenAPI 未提供专用健康检查接口，下文以“轻量业务接口”承担健康检查职责。

## 1. 健康检查（在线/离线切换依据）

### GET /ventures/{venture_id}
- 使用时机: App 启动、前台心跳、验票前检查；用于确认网络连通、token 有效且 venture 可访问。
- 前后端协作:
  - 前端定时发起请求（例如 30-60 秒一次），后端返回 200 视为在线；非 200 视为离线降级。
- 请求:
  - Path: venture_id
- 响应:
  - 200: 返回 Venture 数据（data: Venture）
  - 404: 资源不存在或权限不足

### GET /pos/{pos_id}
- 使用时机: 设备绑定后、验票前快速确认 POS 配置是否仍有效。
- 前后端协作:
  - 前端在健康检查中可追加该接口作为“更贴近业务”的在线判断条件。
- 请求:
  - Path: pos_id
- 响应:
  - 200: 返回 POS 数据
  - 401/404: 无权限或不存在，视为离线降级

## 2. 配置与绑定（手动输入 / QR 扫码）

### 配置入口说明
- 配置二维码（Onboarding）: 用于初始化环境（token/tenant/venture/pos 等）。这是“配置页扫码”的二维码，不是验票二维码。
- 验票二维码（票据/凭证）: 用于验票流程，只包含票号/凭证码，走验票接口（`POST /access/check-code`）。

### POST /auth/impersonate
- 使用时机: 配置二维码中包含 `slug + token` 时，用于换取可用的 Bearer Token（仅配置阶段）。
- 前后端协作:
  - 前端扫码解析出 slug/token -> 调用接口换取 token -> 本地保存 token + tenant/venture/pos 配置。
- 请求:
  - Body (application/json): { slug, token }
- 响应:
  - 200: 返回 user_id、token、qweekler_id
  - 401: token 无效或过期

### GET /ventures
- 使用时机: 在配置页输入 API Key + Tenant ID 后，加载“Choisir un établissement”数据；或扫码后校验 venture 是否可用。
- 前后端协作:
  - 前端展示列表/搜索 -> 用户选择 -> 保存 venture_id 到本地配置。
- 请求:
  - Query: filter[name] / filter[short_name] / filter[slug]（可选）
- 响应:
  - 200: data: Venture[]
  - 404: 无匹配
- 备注:
  - OpenAPI 全局安全策略要求携带 venture，但该接口实际用于获取 venture 列表；如服务端强校验 venture，请使用配置二维码直接下发 venture_id，或让后端放通该接口。

### GET /pos
- 使用时机: 手动配置时选择 POS；或扫码后校验 POS 可用性。
- 前后端协作:
  - 前端按 venture_id 拉取 pos 列表 -> 用户选择 -> 保存 pos_id。
- 请求:
  - Query: filter[venture_id], filter[label], filter[type]（可选）
- 响应:
  - 200: POS 列表
  - 401: 无权限

### POST /pos/{pos_id}/assign
- 使用时机: 设备绑定（将硬件设备与 POS 关联）。如果你的设备是“固定硬件扫码机/一体机”，建议绑定；如果只是手机/平板相机或外接扫码枪，一般可不绑。
- 前后端协作:
  - 前端获取硬件唯一标识（hardware_id）-> 调用绑定接口 -> 绑定成功后保存配置。
- 请求:
  - Path: pos_id
  - Body (application/json): { hardware_id }
- 响应:
  - 200: 绑定成功
  - 401: 无权限

## 3. 验票（在线模式）

### POST /access/check-code
- 使用时机: 在线验票核心接口；无论是硬件扫描、相机扫码、手动输入，最终都调用此接口。
- 前后端协作:
  - 前端获取 code -> 调用接口 -> 依据 200/403 更新验票结果与列表状态。
- 请求:
  - Body (application/json): { code, pos_id, order_id, force }
- 响应:
  - 200: { success: true, message: "Access granted" }
  - 403: { success: false, message: "Access denied" }
  - 500: { success: false, message: "Internal server error" }

### POST /scan（可选）
- 使用时机: 需要先识别扫码内容类型（票/凭证/其他）时使用。
- 前后端协作:
  - 前端传入扫码内容 -> 后端返回匹配资源类型 -> 决定是否继续走 /access/check-code。
- 请求:
  - Body: ScanRequest（包含 scanned code 与可选 object type）
- 响应:
  - 200: 返回按类型分组的匹配资源

## 4. 验票（离线模式）与回传
- 离线验票流程（不依赖接口）:
  - 本地保存每次验票记录（code、时间、来源、结果）到本地队列。
- 回传流程:
  - 健康检查恢复在线 -> 逐条/批量调用 `POST /access/check-code` 回放离线记录 -> 根据响应更新本地状态。
- 前后端协作:
  - 前端负责本地队列与状态流转；后端仅提供在线校验接口。

## 5. 验票列表

### GET /access/logs
- 使用时机: 在线模式下的“验票记录列表页”。
- 前后端协作:
  - 前端按筛选条件拉取访问日志 -> 展示最近/指定条件的验票记录。
- 请求:
  - Query: filter[code] / filter[credential_id] / filter[access_point_id] / filter[rule_id]
- 响应:
  - 200: data: AccessLog[] + metadata
  - 404: 无匹配

### GET /tickets（可选）
- 使用时机: 在线模式下展示“完整票据列表”或根据票号/使用状态查询。
- 前后端协作:
  - 前端查询票据数据用于列表详情展示或统计。
- 请求:
  - Query: filter[number] / filter[used] / filter[used_between] / filter[used_at] 等
- 响应:
  - 200: data: Ticket[] + metadata
  - 404: 无匹配

### 离线列表（本地状态）
- 离线模式下不调用接口，前端依据本地数据分为 4 类:
  1) 全部票（本地票 + 已回传票）
  2) 本地验证票（待回传）
  3) 在线验证失败票（回放时 403）
  4) 在线验证成功票（回放时 200）

## 6. 统计（验票数量 + 日期筛选）

### GET /tickets
- 使用时机: 统计验票数量（以已使用/使用时间为口径）。
- 前后端协作:
  - 前端按日期区间发起请求 -> 使用 metadata/total 或列表长度统计展示。
- 请求示例:
  - filter[is_used]=true
  - filter[used_between]=2024-01-01,2024-01-31
  - filter[used_at]=2024-01-01
- 响应:
  - 200: data: Ticket[] + metadata（用于统计）
