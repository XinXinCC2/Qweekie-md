# Config 页面接口分析文档

## 概述

本文档分析 Config 配置页面所需的 API 接口及数据存储方案。

---

## 一、重要说明

| 序号 | 说明 |
|------|------|
| 1 | API 文档中**没有**心跳检测接口，需要与后端确认或使用业务接口替代 |
| 2 | API 文档中**没有**二维码识别接口，需要前端自行实现扫码解析功能 |
| 3 | API 文档中**没有**配置提交接口，配置数据可能需要需存储在本地 |
| 4 | 验票列表页 对于每个验票记录**没有**验证状态 |
| 5 | API 文档中**没有**验票统计数据接口（如：通过数、拒绝数、降级模式拒绝数） |
| 6 | API 文档中**没有**按时间范围查询验票统计的接口 |

---

## 二、基础配置信息

| 配置项 | 值 |
|--------|-----|
| Base URL | `https://nextapi.qweekle.at/api` |
| API Key | `888\|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e` |
| Tenant ID | `019a248b-66e1-7214-875d-41e05984a099` |
| Pos ID | `92989df0-333d-11ed-ba8f-8ff40d49a83f` |

---

## 三、必需的 HTTP Headers

| Header | 说明 | 示例值 |
|--------|------|--------|
| Authorization | Bearer Token 认证 | `Bearer 888\|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e` |
| tenant | 租户 ID | `019a248b-66e1-7214-875d-41e05984a099` |
| venture | 机构 ID（选择后必填） | `<从 /ventures 返回的 id>` |
| Content-Type | 内容类型 | `application/json` |

---

## 四、接口列表

### 4.1 获取机构列表（Choisir un établissement）

| 项目 | 内容 |
|------|------|
| 接口 | `GET /ventures` |
| 作用 | 获取机构/场馆列表，用于填充"Etablissement"下拉框 |
| 可选筛选 | `filter[name]`、`filter[short_name]`、`filter[slug]` |

**请求示例：**
```bash
curl -X GET "https://nextapi.qweekle.at/api/ventures" \
  -H "Authorization: Bearer 888|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e" \
  -H "tenant: 019a248b-66e1-7214-875d-41e05984a099" \
  -H "Content-Type: application/json"
```

**响应格式：**
```json
{
  "data": [
    {
      "id": "venture_id",
      "name": "机构名称",
      "short_name": "简称",
      "slug": "slug标识"
    }
  ],
  "metadata": {}
}
```

---

### 4.2 获取访问控制点组列表（Choisir le Contrôle d'accès）

| 项目 | 内容 |
|------|------|
| 接口 | `GET /access/point-groups` |
| 作用 | 获取访问控制点组列表，用于填充"Contrôle d'accès"下拉框 |
| 前置条件 | 需要先选择 venture |
| 可选筛选 | `filter[venture_id]`、`filter[label]` |

**请求示例：**
```bash
curl -X GET "https://nextapi.qweekle.at/api/access/point-groups" \
  -H "Authorization: Bearer 888|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e" \
  -H "tenant: 019a248b-66e1-7214-875d-41e05984a099" \
  -H "venture: <选择的venture_id>" \
  -H "Content-Type: application/json"
```

**响应格式：**
```json
{
  "data": [
    {
      "id": "access_point_group_id",
      "venture_id": "venture_id",
      "label": "控制点组名称",
      "created_at": "2023-01-01T00:00:00",
      "updated_at": "2023-01-01T00:00:00"
    }
  ],
  "metadata": {}
}
```

---

### 4.3 获取访问控制点列表（备选接口）

| 项目 | 内容 |
|------|------|
| 接口 | `GET /access/points` |
| 作用 | 获取具体的访问控制点列表 |
| 前置条件 | 需要先选择 venture |
| 可选筛选 | `filter[venture_id]`、`filter[label]`、`filter[access_point_group_id]` |

**请求示例：**
```bash
curl -X GET "https://nextapi.qweekle.at/api/access/points" \
  -H "Authorization: Bearer 888|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e" \
  -H "tenant: 019a248b-66e1-7214-875d-41e05984a099" \
  -H "venture: <选择的venture_id>" \
  -H "Content-Type: application/json"
```

---

## 五、Confirmer 按钮处理逻辑

### 5.1 结论

**API 文档中没有专门的"配置提交"接口，点击 Confirmer 后数据可能是存储在本地。这边需要和Cue确认**

### 5.2 可选的校验请求

在保存前可调用以下接口验证配置是否有效：

```bash
# 验证 venture 是否有效
GET /ventures/{venture_id}

# 验证 access point 是否有效  
GET /access/points/{access_point_id}
```

### 5.3 如果需要本地存储 可能需要的字段

| 字段 | 说明 | 来源 | 是否必填 |
|------|------|------|----------|
| `api_key` | API 密钥 | 用户输入/扫码 | ✅ 是 |
| `tenant_id` | 租户 ID | 用户输入/扫码 | ✅ 是 |
| `venture_id` | 机构 ID | 下拉选择 | ✅ 是 |
| `access_point_id` | 控制点 ID | 下拉选择 | ✅ 是 |
| `scan_interval` | 扫描间隔（毫秒） | 用户输入 | 否（默认500） |
| `degraded_mode` | 降级模式行为 | 下拉选择 | 否（默认"Demander"） |
| `sound_alert` | 声音提醒开关 | 开关 | 否（默认关闭） |
| `vibration_alert` | 振动提醒开关 | 开关 | 否（默认关闭） |

---

## 六、完整调用流程

```
┌─────────────────────────────────────────────────────────────┐
│  1. 用户输入 API Key + Tenant ID（或扫码获取）                │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  2. 调用 GET /ventures                                       │
│     Header: Authorization + tenant                           │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  3. 用户选择 Venture（Etablissement）                        │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  4. 调用 GET /access/point-groups 或 GET /access/points      │
│     Header: Authorization + tenant + venture                 │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  5. 用户选择 Contrôle d'accès                                │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  6. 用户填写其他配置项（扫描间隔、降级模式、提醒开关等）       │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  7. 点击 Confirmer                                           │
│     - 可选：调用校验接口验证配置有效性                        │
│     - 必须：将配置数据保存到本地存储                          │
└─────────────────────────────────────────────────────────────┘
```

---

## 七、心跳检测与网络状态判断

### 7.1 心跳检测方案

**使用 `GET /ventures` 作为心跳检测接口**，可同时验证网络和 API 可用性。

**请求示例：**
```bash
curl -X GET "https://nextapi.qweekle.at/api/ventures" \
  -H "Authorization: Bearer 888|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e" \
  -H "tenant: 019a248b-66e1-7214-875d-41e05984a099" \
  -H "Content-Type: application/json"
```

### 7.2 响应状态码判断

| 响应情况 | 网络状态 | 接口状态 | 处理方式 |
|----------|----------|----------|----------|
| `200 OK` | ✅ 正常 | ✅ 可用 | 正常使用 |
| `401 Unauthorized` | ✅ 正常 | ⚠️ Token 失效 | 提示重新配置 |
| `403 Forbidden` | ✅ 正常 | ⚠️ 无权限 | 检查 Tenant ID |
| `500/502/503` | ✅ 正常 | ❌ 服务异常 | 切换离线模式 |
| `超时/无响应` | ❌ 断网 | ❓ 未知 | 切换离线模式 |

### 7.3 下次启动检查逻辑（配置config后）

| 场景 | 处理方式 |
|------|----------|
| 本地配置完整 + 网络正常 | 调用 `GET /ventures/{venture_id}` 验证，成功则进入主页 |
| 本地配置完整 + 网络异常 | 进入离线模式，允许使用本地缓存数据 |
| 本地配置不完整 | 停留在 Config 页面，要求用户完成配置 |

---

## 八、验票流程与校验策略

### 8.1 核心原则

**不需要每次验票前都校验 venture 和 access point**，那样太慢会影响用户体验。

### 8.2 推荐校验策略

| 时机 | 操作 | 说明 |
|------|------|------|
| **App 启动时** | 校验一次 venture 和 access point | 确保配置有效 |
| **定时心跳** | 每 30-60 秒调用 `GET /ventures` | 检测网络和 API 状态 |
| **验票时** | 直接调用 `POST /access/check-code` | 不做额外校验 |
| **验票失败时** | 根据错误码判断 | 401/403 才需要重新校验配置 |

### 8.3 验票流程图

```
┌─────────────────────────────────────────────────────────────┐
│  扫码获取票据码                                              │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  直接调用 POST /access/check-code                            │
│  （不做额外校验，保证验票速度）                               │
└─────────────────────────────────────────────────────────────┘
                              ↓
                    ┌─────────┴─────────┐
                    ↓                   ↓
               成功 (200)           失败
                    ↓                   ↓
               显示通过            判断错误码
                                        ↓
                    ┌───────────────────┼───────────────────┐
                    ↓                   ↓                   ↓
                  403                 401                 其他
               票据无效           Token 失效           网络问题
                    ↓                   ↓                   ↓
               显示拒绝           提示重新配置         切换离线模式
```

### 8.4 验票接口

| 项目 | 内容 |
|------|------|
| 接口 | `POST /access/check-code` |
| 作用 | 核心验票接口，校验票据码是否有效 |

**请求示例：**
```bash
curl -X POST "https://nextapi.qweekle.at/api/access/check-code" \
  -H "Authorization: Bearer 888|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e" \
  -H "tenant: 019a248b-66e1-7214-875d-41e05984a099" \
  -H "venture: <venture_id>" \
  -H "Content-Type: application/json" \
  -d '{
    "code": "<扫描到的票据码>",
    "pos_id": "<pos_id>必传 但是目前没有获取的途径"
  }'
```

**响应格式：**
```json
// 成功
{
  "success": true,
  "message": "Access granted"
}

// 失败
{
  "success": false,
  "message": "Access denied"
}
```
### 8.5 重要说明
**API 文档中没有批量验票接口**，`POST /access/check-code` 只支持单个票据码验证。

---

## 九、验票记录列表页

### 9.1 接口信息

| 项目 | 内容 |
|------|------|
| 接口 | `GET /access/logs` |
| 作用 | 获取验票记录列表 |
| 可选筛选 | `filter[code]`、`filter[credential_id]`、`filter[access_point_id]`、`filter[rule_id]` |

### 9.2 请求示例

```bash
curl -X GET "https://nextapi.qweekle.at/api/access/logs" \
  -H "Authorization: Bearer 888|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e" \
  -H "tenant: 019a248b-66e1-7214-875d-41e05984a099" \
  -H "venture: <venture_id>" \
  -H "Content-Type: application/json"
```

### 9.3 响应格式

```json
{
  "data": [
    {
      "id": 12345,
      "credential_id": 67890,
      "credential_code": "TX09180DF5C-1",
      "access_point_id": 111,
      "access_point_label": "Tout le Parc",
      "direction": "in",
      "access_rule_id": 222,
      "access_rule_text": "Entrée adulte (OBLIGATOIOBLIGAT...)",
      "is_forced": false,
      "created_at": "2025-09-21T12:00:00",
      "updated_at": "2025-09-21T12:00:00",
      "deleted_at": null
    }
  ],
  "metadata": {}
}
```

### 9.4 响应字段说明

| 字段 | 类型 | 说明 | UI 对应 |
|------|------|------|---------|
| `id` | integer | 日志 ID | - |
| `credential_id` | integer | 凭证 ID | - |
| `credential_code` | string | 票据码 | TX09180DF5C-1 |
| `access_point_id` | integer | 控制点 ID | - |
| `access_point_label` | string | 控制点名称 | Tout le Parc |
| `direction` | string | 方向（in/out） | - |
| `access_rule_id` | integer | 规则 ID | - |
| `access_rule_text` | string | 规则/票种描述 | Entrée adulte... |
| `is_forced` | boolean | 是否强制通过 | - |
| `created_at` | datetime | 验票时间 | 21/09/2025 12:00 |

### 9.5 ⚠️ 重要说明：成功/失败状态

**API 返回的 AccessLog 中没有成功/失败状态字段！**

`GET /access/logs` 只记录**成功通过**的验票记录，失败的验票不会出现在这个列表中。

### 9.6 列表页数据来源方案

由于 API 限制，列表页需要**前端自己维护验票状态**：

---

## 十、验票记录统计

### 10.1 统计页面数据需求

| 统计项 | 法语名称 | 说明 |
|--------|----------|------|
| 在线通过数 | Approuvé en ligne | 在线模式下验票通过的次数 |
| 在线拒绝数 | Refus en ligne | 在线模式下验票被拒绝的次数 |
| 降级模式拒绝数 | Refus en dégradé | 降级/离线模式下验票被拒绝的次数 |

### 10.2 ⚠️ API 现状

**在当前 OpenAPI 规范中，没有找到对应的统计接口。**

已排查的相关接口：
- `GET /agendas/daily-stats/{year}` - 日程/预约统计，非验票统计
- `GET /sales/metrics` - 销售指标，非验票统计

### 10.3 可能的数据来源

| 方案 | 说明 | 可行性 |
|------|------|--------|
| **前端本地统计** | App 在本地存储每次验票结果，自行计算统计数据 | ✅ 推荐 |
| 未公开的内部接口 | 可能存在未在 OpenAPI 文档中公开的统计接口 | ⚠️ 需确认 |
| 后端聚合查询 | 通过 GraphQL 或其他内部 API 获取 | ⚠️ 需确认 |

### 10.4 前端本地统计方案（推荐）

如果采用前端本地统计，需要存储以下数据：

| 字段 | 类型 | 说明 |
|------|------|------|
| `approved_online` | integer | 在线模式通过次数 |
| `rejected_online` | integer | 在线模式拒绝次数 |
| `rejected_degraded` | integer | 降级模式拒绝次数 |
| `last_reset_at` | datetime | 上次重置时间 |

**统计更新时机：**
- 每次调用 `POST /access/check-code` 后，根据响应结果更新对应计数
- 降级模式下的验票结果单独统计

### 10.5 待确认事项

| 序号 | 问题 | 负责人 |
|------|------|--------|
| 1 | 是否有未公开的统计接口？ | 后端 |
| 2 | 统计数据是否需要同步到服务器？ | 产品/后端 |
| 3 | 统计数据的重置周期（每日/每周/手动）？ | 产品 |

---

## 十一、POS 终端接口

### 11.1 POS 概念说明

| 概念 | 说明 |
|------|------|
| **POS** | Point of Sale，销售点/验票终端设备 |
| **POS Session** | POS 工作会话，代表一个班次/工作周期 |
| **pos_id** | POS 终端的唯一标识，**验票接口必填参数** |

### 11.2 获取 POS 列表

| 项目 | 内容 |
|------|------|
| 接口 | `GET /pos` |
| 作用 | 获取 POS 终端列表，用于 Config 页面选择 |
| 前置条件 | 需要先选择 venture |
| 所需权限 | `pos.show` |

> ⚠️ **重要提示：权限限制**
> 
> **当前用户 Token 没有访问该接口的权限！**
> 
> 调用此接口会返回 `403 FORBIDDEN` 错误：
> ```json
> {
>   "error": true,
>   "code": "FORBIDDEN",
>   "message": "User does not have the right permissions. Necessary permissions are pos.show"
> }
> ```
> 
> **解决方案：** 直接使用客户提供的 Pos ID，在 **「二、基础配置信息」** 中已配置：
> - Pos ID: `92989df0-333d-11ed-ba8f-8ff40d49a83f`

**请求参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `filter[venture_id]` | string | 否 | 按机构 ID 筛选 |
| `filter[label]` | string | 否 | 按名称模糊筛选 |
| `filter[type]` | string | 否 | 按类型筛选（如 `access_ctrl`） |
| `include` | string | 否 | 包含子资源（如 `latestPosSession`） |

**请求示例：**
```bash
curl -X GET "https://nextapi.qweekle.at/api/pos?filter[venture_id]=<venture_id>" \
  -H "Authorization: Bearer 888|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e" \
  -H "tenant: 019a248b-66e1-7214-875d-41e05984a099" \
  -H "venture: <venture_id>" \
  -H "Content-Type: application/json"
```

**响应格式：**
```json
{
  "data": [
    {
      "id": "92989df0-333d-11ed-ba8f-8ff40d49a83f",
      "venture_id": "VXXX9ebc3ccae0234d01b1130cf58bea39dd",
      "label": "CTRL ERIC",
      "type": "access_ctrl",
      "hardware_id": null
    }
  ]
}
```

---

### 11.3 获取单个 POS 详情

| 项目 | 内容 |
|------|------|
| 接口 | `GET /pos/{pos_id}` |
| 作用 | 获取指定 POS 的详细信息 |

**请求参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `pos_id` | string | ✅ 是 | POS ID（路径参数） |
| `withCurrentSession` | boolean | 否 | 是否包含当前会话 |
| `withAttributes` | boolean | 否 | 是否包含属性 |

**请求示例：**
```bash
curl -X GET "https://nextapi.qweekle.at/api/pos/92989df0-333d-11ed-ba8f-8ff40d49a83f?withCurrentSession=true" \
  -H "Authorization: Bearer 888|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e" \
  -H "tenant: 019a248b-66e1-7214-875d-41e05984a099" \
  -H "venture: <venture_id>" \
  -H "Content-Type: application/json"
```

---

### 11.4 获取 POS 当前活跃会话

| 项目 | 内容 |
|------|------|
| 接口 | `GET /pos/{pos_id}/current-pos-session` |
| 作用 | 检查 POS 是否有活跃的工作会话 |

**请求示例：**
```bash
curl -X GET "https://nextapi.qweekle.at/api/pos/92989df0-333d-11ed-ba8f-8ff40d49a83f/current-pos-session" \
  -H "Authorization: Bearer 888|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e" \
  -H "tenant: 019a248b-66e1-7214-875d-41e05984a099" \
  -H "venture: <venture_id>" \
  -H "Content-Type: application/json"
```

**响应格式：**
```json
{
  "data": {
    "id": "PSXXa0a893f18a86483e834395b6bc470977",
    "pos_id": "92989df0-333d-11ed-ba8f-8ff40d49a83f",
    "opened_at": "2025-12-23T03:02:50.000000Z",
    "closed_at": null
  }
}
```

**判断逻辑：**
- `closed_at == null` → 会话活跃，可以验票
- `closed_at != null` → 会话已关闭，需要重新开班
- 返回 `204` 或 `404` → 无活跃会话，需要创建

---

### 11.5 创建 POS Session（开班）

| 项目 | 内容 |
|------|------|
| 接口 | `POST /pos/{pos_id}/pos-sessions` |
| 作用 | 为指定 POS 创建新的工作会话（开班） |
| ⚠️ 注意 | 一个 POS 同时只能有一个活跃会话 |

**请求参数（Body）：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `pos_id` | string | ✅ 是 | POS ID（需要在 body 中再传一次） |
| `total` | integer | ✅ 是 | 初始金额（验票 App 设为 `0`） |
| `cash` | array | ✅ 是 | 现金明细（验票 App 传 `[]`） |
| `open_comment` | string | ✅ 是 | 开班备注（可传空字符串 `""`） |

**请求示例：**
```bash
curl -X POST "https://nextapi.qweekle.at/api/pos/92989df0-333d-11ed-ba8f-8ff40d49a83f/pos-sessions" \
  -H "Authorization: Bearer 888|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e" \
  -H "tenant: 019a248b-66e1-7214-875d-41e05984a099" \
  -H "venture: VXXX9ebc3ccae0234d01b1130cf58bea39dd" \
  -H "Content-Type: application/json" \
  -d '{
    "pos_id": "92989df0-333d-11ed-ba8f-8ff40d49a83f",
    "total": 0,
    "cash": [],
    "open_comment": ""
  }'
```

**响应格式：**
```json
{
  "data": {
    "id": "PSXXa0a893f18a86483e834395b6bc470977",
    "venture_id": "VXXX9ebc3ccae0234d01b1130cf58bea39dd",
    "pos_id": "92989df0-333d-11ed-ba8f-8ff40d49a83f",
    "opened_at": "2025-12-23T03:02:50.000000Z",
    "closed_at": null
  }
}
```

**常见错误：**

| 错误信息 | 原因 | 解决方案 |
|----------|------|----------|
| `Pos already have opened session` | POS 已有活跃会话 | 无需再创建，直接验票 |
| `The pos id field is required` | 缺少 pos_id 参数 | 在 body 中添加 pos_id |
| `The total field is required` | 缺少 total 参数 | 在 body 中添加 total |

---

### 11.6 关闭 POS Session（结班）

| 项目 | 内容 |
|------|------|
| 接口 | `POST /pos-sessions/{pos_session_id}/close` |
| 作用 | 关闭指定的工作会话（结班） |
| ⚠️ 注意 | 必须使用 Session ID，不能用 POS ID |

**请求参数：**

| 参数 | 类型 | 必填 | 说明 |
|------|------|------|------|
| `pos_session_id` | string | ✅ 是 | POS Session ID（路径参数） |

**请求示例：**
```bash
curl -X POST "https://nextapi.qweekle.at/api/pos-sessions/PSXXa0a893f18a86483e834395b6bc470977/close" \
  -H "Authorization: Bearer 888|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e" \
  -H "tenant: 019a248b-66e1-7214-875d-41e05984a099" \
  -H "venture: VXXX9ebc3ccae0234d01b1130cf58bea39dd" \
  -H "Content-Type: application/json" \
  -d '{}'
```

**响应格式：**
```json
{
  "data": {
    "id": "PSXXa0a893f18a86483e834395b6bc470977",
    "closed_at": "2025-12-23T03:12:10.000000Z",
    "closed_by": "UXXXa091743962d24f989ba274069619d500"
  }
}
```

---

### 11.7 POS 接口汇总

| 接口 | 方法 | 作用 | 验票 App 必需 |
|------|------|------|---------------|
| `/pos` | GET | 获取 POS 列表 | ✅ Config 页面选择 |
| `/pos/{pos_id}` | GET | 获取 POS 详情 | ⚠️ 可选 |
| `/pos/{pos_id}/current-pos-session` | GET | 检查活跃会话 | ✅ 验票前检查 |
| `/pos/{pos_id}/pos-sessions` | POST | 创建会话（开班） | ✅ 无会话时创建 |
| `/pos-sessions/{session_id}/close` | POST | 关闭会话（结班） | ✅ 结束验票时 |

---

## 十二、完整验票流程图

```
┌─────────────────────────────────────────────────────────────┐
│  1. Config 配置完成                                          │
│     - 选择 venture（机构）                                    │
│     - 选择 access_point（控制点）                             │
│     - 选择 pos（验票终端）                                    │
│     - 保存配置到本地                                          │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  2. 检查 POS 是否有活跃会话                                   │
│     接口: GET /pos/{pos_id}/current-pos-session              │
└─────────────────────────────────────────────────────────────┘
                              ↓
                    ┌─────────┴─────────┐
                    ↓                   ↓
          有活跃会话 (200)        无活跃会话 (204/404)
          closed_at == null              ↓
                    ↓           ┌───────────────────────────┐
                    │           │  3. 创建新会话（开班）      │
                    │           │  接口: POST /pos/{pos_id}/ │
                    │           │        pos-sessions        │
                    │           │  Body: {                   │
                    │           │    "pos_id": "<pos_id>",   │
                    │           │    "total": 0,             │
                    │           │    "cash": [],             │
                    │           │    "open_comment": ""      │
                    │           │  }                         │
                    │           └───────────────────────────┘
                    │                       ↓
                    │              保存返回的 session_id
                    │                       ↓
                    └───────────┬───────────┘
                                ↓
┌─────────────────────────────────────────────────────────────┐
│  4. 开始验票                                                  │
│     接口: POST /access/check-code                            │
│     Body: {                                                  │
│       "code": "<扫描到的票据码>",                              │
│       "pos_id": "<pos_id>"                                   │
│     }                                                        │
└─────────────────────────────────────────────────────────────┘
                              ↓
                    ┌─────────┴─────────┐
                    ↓                   ↓
               成功 (200)           失败
                    ↓                   ↓
               显示通过            判断错误码
               更新统计                 ↓
                    │     ┌─────────────┼─────────────┐
                    │     ↓             ↓             ↓
                    │   403           401          其他
                    │  票据无效    Token 失效     网络问题
                    │     ↓             ↓             ↓
                    │  显示拒绝    提示重新配置   切换离线模式
                    │  更新统计                   更新统计
                    │     │             │             │
                    └─────┴─────────────┴─────────────┘
                                ↓
                         继续扫码验票
                                ↓
┌─────────────────────────────────────────────────────────────┐
│  5. 结束验票（结班）                                          │
│     接口: POST /pos-sessions/{session_id}/close             │
│     Body: {}                                                │
└─────────────────────────────────────────────────────────────┘
```