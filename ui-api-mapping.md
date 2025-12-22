# 业务页面与接口映射（基于 OpenAPI + 需求分析）

## 前置说明
- OpenAPI 未提供“专用健康检查接口”与“配置提交接口”。配置提交属于客户端本地持久化。
- 健康检查建议用轻量业务接口替代（见下文）。
- 文中“扫码配置二维码”与“验票二维码”是两类不同的二维码：
  - 配置二维码（onboarding）用于初始化 token/tenant/venture/pos。
  - 验票二维码用于票据校验，只包含票号/凭证码。

---

## CONFIG 页面

### 1. 获取下拉框数据的接口
- Choisir un établissement（机构/场馆）:
  - `GET /ventures`
  - 说明: 用于加载 venture 列表（支持 filter[name]/filter[short_name]/filter[slug]）
- POS 选择:
  - `GET /pos`（使用 filter[venture_id] 过滤）

### 2. 扫描获取数据方式的逻辑、入口、扫码 UI
- 入口:
  - Config 页的“扫码配置”按钮 -> 打开“配置扫码页面”（使用设备相机扫描二维码）。
- 扫描逻辑:
  - 解析二维码 payload:
    - 若包含 `slug + token` -> 调用 `POST /auth/impersonate` 换取 Bearer Token。
    - 若包含 `tenant/venture/pos` -> 直接写入本地配置（必要时校验）。
  - 校验（可选）:
    - `GET /ventures/{venture_id}` / `GET /pos/{pos_id}` 用于确认数据可用。
- UI 页面:
  - 使用独立“配置扫码页面”，与验票扫码页面分离，避免误扫票据码。

### 3. 扫描获取的数据哪些不能修改
- 来自配置二维码且具有强绑定语义的数据建议锁定不可编辑:
  - `tenant`、`venture_id`、`pos_id`（如果二维码明确指定）
  - 若二维码提供 `slug + token`，则 token 本身不应被手工覆盖
- 可编辑项:
  - 用户手动模式下可调整 `venture_id` / `pos_id`

### 4. 数据关联关系与 Confirm 按钮条件
- 关联关系:
  - `pos` 必须属于 `venture`，因此 POS 下拉必须依赖已选 venture。
- Confirm 必填条件（建议）:
  - `Authorization (Bearer Token)`
  - `tenant`
  - `venture_id`
  - `pos_id`（验票接口常依赖 pos，虽然 OpenAPI 标注为可选，建议配置阶段强制选择）
- Confirm 可点击条件:
  - 必填字段齐全且通过一次健康检查（见“下一次启动检查逻辑”）。

### 5. 提交数据要调用的接口
- 无“配置提交接口”。
- 提交时仅执行“可用性校验”（可选）:
  - `GET /ventures/{venture_id}`
  - `GET /pos/{pos_id}`

### 6. 本地存储逻辑（提交后）
- 必存字段:
  - `base_url`（选用环境）
  - `authorization_token`
  - `tenant`
  - `venture_id`
  - `pos_id`
- 可选字段:
  - `hardware_id`（若走设备绑定）
  - 最近一次健康检查结果/时间戳

### 7. 下次启动的配置检查逻辑（离线/在线）
- 在线判定:
  - 依次调用 `GET /ventures/{venture_id}` / `GET /pos/{pos_id}`。
  - 2xx -> 在线；非 2xx 或超时 -> 进入离线模式。
- 离线允许逻辑（建议）:
  - 若本地配置完整，允许进入首页但标记离线并启用离线验票。
  - 若本地配置不完整，停留在 Config 页。

---

## 首页（在线模式）

### 1. 底部按钮状态
- 在线状态下:
  - “扫码验票”可用
  - “同步/队列”显示 0 或隐藏
  - “离线模式”标识不显示

### 2. 拉取在线列表数据的接口
- 若列表展示“验票记录”:
  - `GET /access/logs`
- 若列表展示“票据列表”:
  - `GET /tickets`

### 3. 根据输入字段查询接口
- 验票记录搜索:
  - `GET /access/logs` + `filter[code]` / `filter[credential_id]`
- 票据搜索:
  - `GET /tickets` + `filter[number]` / `filter[used]` / `filter[used_at]` / `filter[used_between]`

### 4. 列表是否仅展示
- 默认仅展示。
- 若需要详情页:
  - 票据详情: `GET /tickets/{ticket_id}`
  - 凭证详情: `GET /access/credentials/{crendential_id}`

### 5. 降级模式判断
- 建议:
  - 健康检查连续失败 N 次（如 3 次）或请求超时 -> 切换离线。
- 不切换的风险:
  - 扫码验票持续失败、体验卡顿、无法缓存离线记录。

### 6. 扫描验票接口
- 核心验票:
  - `POST /access/check-code`
- 可选预识别:
  - `POST /scan`（识别扫码内容类型后再决定是否验票）

---

## 数据统计页面

### 1. 拉取统计数据接口
- 推荐使用票据接口统计（有使用时间字段）:
  - `GET /tickets` + `filter[is_used]=true`

### 2. 时间过滤接口
- `GET /tickets` +
  - `filter[used_between]=YYYY-MM-DD,YYYY-MM-DD`
  - `filter[used_at]=YYYY-MM-DD`

---

## 扫描页面

### 1. 扫描后校验接口
- `POST /access/check-code`
- 可选: `POST /scan`（需要先识别内容类型时）

---

## 需要提供的接口 API（对应你列出的 8 项）

1. Config 页面拉取用户数据
- `POST /auth/impersonate`（扫码时换 token，并返回 user_id）
- 如需用户详情: `GET /users/{user_id}`（可选）

2. Config 页面拉取下拉框数据
- `GET /ventures`
- `GET /pos`（带 filter[venture_id]）

3. Config 页面提交数据
- 无专用接口；仅做校验:
  - `GET /ventures/{venture_id}`
  - `GET /pos/{pos_id}`

4. 在线页面拉取数据
- `GET /access/logs` 或 `GET /tickets`

5. 在线页面通过字段拉取数据
- `GET /access/logs` + filter[code]/filter[credential_id]
- `GET /tickets` + filter[number]/filter[used]/filter[used_between]

6. 在线检查票据
- `POST /access/check-code`

7. 数据统计拉取接口
- `GET /tickets` + filter[is_used]=true

8. 数据统计通过时间拉取数据接口
- `GET /tickets` + filter[used_between] / filter[used_at]

