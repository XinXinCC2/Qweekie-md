# Config 页面接口分析文档

## 概述

本文档分析 Config 配置页面所需的 API 接口及数据存储方案。

---

## 一、重要说明

| 序号 | 说明 |
|------|------|
| 1 | API 文档中**没有**心跳检测接口，需要与后端确认或使用业务接口替代 |
| 2 | API 文档中**没有**二维码识别接口，需要前端自行实现扫码解析功能 |
| 3 | API 文档中**没有**配置提交接口，配置数据需存储在本地 |

---

## 二、基础配置信息

| 配置项 | 值 |
|--------|-----|
| Base URL | `https://nextapi.qweekle.at/api` |
| API Key | `888\|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e` |
| Tenant ID | `019a248b-66e1-7214-875d-41e05984a099` |

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

**API 文档中没有专门的"配置提交"接口，点击 Confirmer 后数据需存储在本地。**

### 5.2 可选的校验请求

在保存前可调用以下接口验证配置是否有效：

```bash
# 验证 venture 是否有效
GET /ventures/{venture_id}

# 验证 access point 是否有效  
GET /access/points/{access_point_id}
```

### 5.3 本地存储字段

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

### 7.3 心跳检测伪代码

```
心跳检测() {
    try {
        response = GET /ventures (超时: 5秒)
        
        if (response.status == 200) {
            return { network: true, api: true }
        } 
        else if (response.status == 401 || 403) {
            return { network: true, api: false, reason: "认证失败" }
        }
        else if (response.status >= 500) {
            return { network: true, api: false, reason: "服务异常" }
        }
    } catch (超时/网络错误) {
        return { network: false, api: false }
    }
}
```

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
    "pos_id": "<pos_id>"
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

---

## 九、下次启动检查逻辑

| 场景 | 处理方式 |
|------|----------|
| 本地配置完整 + 网络正常 | 调用 `GET /ventures/{venture_id}` 验证，成功则进入主页 |
| 本地配置完整 + 网络异常 | 进入离线模式，允许使用本地缓存数据 |
| 本地配置不完整 | 停留在 Config 页面，要求用户完成配置 |


---

## 十、验票记录列表页

### 10.1 接口信息

| 项目 | 内容 |
|------|------|
| 接口 | `GET /access/logs` |
| 作用 | 获取验票记录列表 |
| 可选筛选 | `filter[code]`、`filter[credential_id]`、`filter[access_point_id]`、`filter[rule_id]` |

### 10.2 请求示例

```bash
curl -X GET "https://nextapi.qweekle.at/api/access/logs" \
  -H "Authorization: Bearer 888|aCs8qcl1fhyToJsBdsNwtcgeKwYi0Qc4FIhJnHLUf649579e" \
  -H "tenant: 019a248b-66e1-7214-875d-41e05984a099" \
  -H "venture: <venture_id>" \
  -H "Content-Type: application/json"
```

### 10.3 响应格式

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

### 10.4 响应字段说明

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

### 10.5 ⚠️ 重要说明：成功/失败状态

**API 返回的 AccessLog 中没有成功/失败状态字段！**

`GET /access/logs` 只记录**成功通过**的验票记录，失败的验票不会出现在这个列表中。

### 10.6 列表页数据来源方案

由于 API 限制，列表页需要**前端自己维护验票状态**：

```javascript
// 验票时保存结果到本地
async function checkAndSave(code) {
    try {
        const response = await fetch('/access/check-code', {
            method: 'POST',
            body: JSON.stringify({ code: code })
        })
        
        const result = await response.json()
        
        // 保存到本地列表
        const record = {
            code: code,
            success: result.success,
            message: result.message,
            status: result.success ? 'approved' : 'refused',
            checked_at: new Date().toISOString()
        }
        
        saveToLocalList(record)
        return record
        
    } catch (error) {
        // 网络错误，进入离线模式
        const record = {
            code: code,
            status: 'pending',
            checked_at: new Date().toISOString()
        }
        saveToOfflineQueue(record)
        return record
    }
}
```

### 10.7 列表页状态对应

| 状态 | HTTP 状态码 | success | UI 显示 | 颜色 |
|------|-------------|---------|---------|------|
| 处理中 | - | - | En cours de traitement | 灰色 |
| 通过 | 200 | true | Approuvé | 绿色 |
| 拒绝 | 403 | false | Accès refusé | 红色 |
| 待同步 | - | - | En attente | 蓝色 |

---

## 十一、离线模式与批量同步

### 11.1 重要说明

**API 文档中没有批量验票接口**，`POST /access/check-code` 只支持单个票据码验证。

### 11.2 离线同步方案

```
┌─────────────────────────────────────────────────────────────┐
│  离线模式：扫码 → 本地缓存（队列）                           │
│  状态标记为 "pending"                                        │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  网络恢复：逐个调用 POST /access/check-code                  │
│  （循环处理队列中的每个票据码）                              │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│  更新本地记录状态为 "approved" 或 "refused"                  │
└─────────────────────────────────────────────────────────────┘
```

### 11.3 离线同步伪代码

```javascript
// 离线队列
let offlineQueue = []

// 离线时：加入队列
function offlineScan(code) {
    offlineQueue.push({
        code: code,
        scanned_at: new Date().toISOString(),
        status: 'pending'
    })
    saveToLocalStorage(offlineQueue)
}

// 网络恢复时：批量同步
async function syncOfflineQueue() {
    for (let item of offlineQueue) {
        if (item.status === 'pending') {
            try {
                const result = await checkCode(item.code)
                item.status = result.success ? 'approved' : 'refused'
                item.message = result.message
                item.synced_at = new Date().toISOString()
            } catch (error) {
                // 网络又断了，停止同步
                break
            }
        }
    }
    saveToLocalStorage(offlineQueue)
}
```
