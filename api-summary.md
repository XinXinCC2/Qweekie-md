# Qweekle Dev API

## 全局配置
- OpenAPI: 3.1.0
- 版本: 1.0.0
- 说明: Qweekle's public REST API, allows the user to query Qweekle's data model et get JSON responses. To operate in as RESTful of a manner as possible, the Qweekle API requires multiple headers to carry state of each request : - Authorization : Bearer <your_user_s_bearer_token> - tenant : <the_identifying_qweekle_account_id> - venture : <the_current_venture_id> - pos (optional) : <the_active_pos_id> (some actions require a pos for business logic and compliance reasons)
- Base URL:
  - https://nextapi.qweekle.at/api
  - https://api.qweekle-beta.com/api
- 认证与必填 Header:
  - Tenant: tenant (header)
  - Venture: venture (header)
  - Bearer_Token: Authorization (Bearer Token)
- 全局安全策略: Bearer_Token, Tenant, Venture

## Acceptances
> Operations related to Acceptances

### GET /acceptances
- 作用: 用于当前系统中 Acceptances 模块的接口，通过 `GET /acceptances` 获取 acceptances 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[clients.email] (query, string, email, 可选) - Filter acceptances with a partial email.
    - filter[accepted_at] (query, string, 可选) - Filter acceptances with a partial acceptation datetime.
    - filter[accepted_src] (query, string, 可选) - Filter acceptances with an EXACT source
    - filter[accepted_key] (query, string, 可选) - Filter acceptances with an EXACT key
    - filter[client_id] (query, string, 可选) - Filter acceptances with an EXACT client ID
    - filter[venture_id] (query, string, 可选) - Filter acceptances with an EXACT venture ID
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

## Access
> Operations related to Access

### POST /access/check-code
- 作用: 用于当前系统中 Access 模块的接口，通过 `POST /access/check-code` 校验访问码并返回授权结果。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{code, pos_id, order_id, force}
- 响应体:
  - 200: Access granted | application/json -> object{success, message}
  - 403: Access denied | application/json -> object{success, message}
  - 500: Internal server error | application/json -> object{success, message}

### GET /access/credentials
- 作用: 用于当前系统中 Access 模块的接口，通过 `GET /access/credentials` 获取 access 下 credentials 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[code] (query, string, 可选) - Filter access_credentials with a partial code
    - filter[sale_id] (query, string, 可选) - Filter access_credentials with an EXACT sale_id
    - filter[order_id] (query, string, 可选) - Scope access_credentials with an EXACT order_id
    - filter[venture_id] (query, string, 可选) - Filter access_credentials with an EXACT venture ID
    - filter[ref_id] (query, string, 可选) - Filter access_credentials with an EXACT ref ID
    - filter[ref_type] (query, string, 可选) - Filter access_credentials with an EXACT ref type
    - filter[client_buyer] (query, string, 可选) - Filter credentials by buyer client's name or society
    - filter[user_search] (query, string, 可选) - Filter credentials by user client's name or society
    - filter[number] (query, string, 可选) - Filter credentials with a partial number
    - filter[ticket_number] (query, string, 可选) - Scope credentials with an EXACT ticket number
    - filter[sale_number] (query, string, 可选) - Scope credentials with an EXACT sale number
    - filter[sold_between] (query, string, 可选) - Filter credentials sold between two dates
    - filter[used_at] (query, string, date, 可选) - Filter credentials used on a specific date
    - filter[expired_at] (query, string, date, 可选) - Filter credentials expiring on a specific date
    - filter[created_between] (query, string, 可选) - Filter credentials created between two dates (comma separated)
    - filter[expired_between] (query, string, 可选) - Filter credentials expired between two dates (comma separated)
    - filter[is_used] (query, string, 可选) - Filter credentials by usage state (true/false)
    - filter[usage_count_greater_than] (query, integer, 可选) - Filter credentials with at least the provided number of usages
    - filter[only_deleted] (query, boolean, 可选) - Return only deleted credentials
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /access/credentials/export
- 作用: 用于当前系统中 Access 模块的接口，通过 `POST /access/credentials/export` 导出 access 相关数据，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[code] (query, string, 可选) - Filter access_credentials with a partial code
    - filter[sale_id] (query, string, 可选) - Filter access_credentials with an EXACT sale_id
    - filter[order_id] (query, string, 可选) - Scope access_credentials with an EXACT order_id
    - filter[venture_id] (query, string, 可选) - Filter access_credentials with an EXACT venture ID
    - filter[ref_id] (query, string, 可选) - Filter access_credentials with an EXACT ref ID
    - filter[ref_type] (query, string, 可选) - Filter access_credentials with an EXACT ref type
    - filter[created_between] (query, string, 可选) - Filter credentials created between two dates (comma separated)
    - filter[expired_between] (query, string, 可选) - Filter credentials expired between two dates (comma separated)
    - filter[is_used] (query, string, 可选) - Filter credentials by usage state (true/false)
    - filter[usage_count_greater_than] (query, integer, 可选) - Filter credentials with at least the provided number of usages
    - filter[only_deleted] (query, boolean, 可选) - Return only deleted credentials
  - Body:
    - 无
- 响应体:
  - 200: Success | application/pdf -> object
  - 401: Not allowed

### PATCH /access/credentials/{credential_id}
- 作用: 用于当前系统中 Access 模块的接口，通过 `PATCH /access/credentials/{credential_id}` 更新 access 下 credentials 部分字段。
- 请求体:
  - 参数:
    - credential_id (path, string, uuid, 必填) - UUID of the access credential to update
  - Body:
    - Content-Type: application/json -> object{code}
- 响应体:
  - 200: Access credential successfully updated | application/json -> object{id, venture_id, external_id, sale_id, code, text, code_type, credentiable, ...}
  - 404: Credential not found
  - 422: Validation error

### GET /access/credentials/{credential_id}/pdf
- 作用: 用于当前系统中 Access 模块的接口，通过 `GET /access/credentials/{credential_id}/pdf` 生成或获取 access 的 PDF 文件。
- 请求体:
  - 参数:
    - credential_id (path, string, 必填) - The credential_id of the Access Credential we wish to get.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/pdf -> object
  - 202: Accepted | application/json -> object
  - 401: Not allowed

### GET /access/credentials/{crendential_id}
- 作用: 用于当前系统中 Access 模块的接口，通过 `GET /access/credentials/{crendential_id}` 获取 access 下 credentials 详情。
- 请求体:
  - 参数:
    - crendential_id (path, string, 必填) - Id of the credential we wish to review.
    - include (query, string, 可选) - Sub-resources to include
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{id, venture_id, external_id, sale_id, code, text, code_type, credentiable, ...}
  - 404: No Match for query

### GET /access/logs
- 作用: 用于当前系统中 Access 模块的接口，通过 `GET /access/logs` 获取 access 下 logs 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[code] (query, string, 可选) - Filter access_logs with a partial code
    - filter[credential_id] (query, string, 可选) - Filter access_logs with an EXACT credential ID
    - filter[access_point_id] (query, string, 可选) - Filter access_logs with an EXACT access_point ID
    - filter[rule_id] (query, string, 可选) - Filter access_logs with an EXACT rule ID
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### GET /access/point-groups
- 作用: 用于当前系统中 Access 模块的接口，通过 `GET /access/point-groups` 获取 access 下 point-groups 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter access_point_groups with a partial label
    - filter[venture_id] (query, string, 可选) - Filter access_point_groups with an EXACT venture ID
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 401: Not allowed
  - 404: No Match for query

### POST /access/point-groups
- 作用: 用于当前系统中 Access 模块的接口，通过 `POST /access/point-groups` 在 access 下创建 point-groups。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, label, location_id, direction}
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /access/point-groups/{access_point_group_id}
- 作用: 用于当前系统中 Access 模块的接口，通过 `GET /access/point-groups/{access_point_group_id}` 获取 access 下 point-groups 详情。
- 请求体:
  - 参数:
    - access_point_group_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### DELETE /access/point-groups/{access_point_group_id}
- 作用: 用于当前系统中 Access 模块的接口，通过 `DELETE /access/point-groups/{access_point_group_id}` 删除 access 下 point-groups。
- 请求体:
  - 参数:
    - access_point_group_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success
  - 401: Not allowed

### PATCH /access/point-groups/{access_point_group_id}
- 作用: 用于当前系统中 Access 模块的接口，通过 `PATCH /access/point-groups/{access_point_group_id}` 更新 access 下 point-groups 部分字段。
- 请求体:
  - 参数:
    - access_point_group_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, location_id, direction}
- 响应体:
  - 200: Success | application/json -> object{id, venture_id, label, created_at, updated_at, deleted_at}
  - 401: Not allowed

### GET /access/points
- 作用: 用于当前系统中 Access 模块的接口，通过 `GET /access/points` 获取 access 下 points 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter access_points with a partial label
    - filter[venture_id] (query, string, 可选) - Filter access_points with an EXACT venture ID
    - filter[access_point_group_id] (query, string, 可选) - Filter access_points with an EXACT group ID
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /access/points
- 作用: 用于当前系统中 Access 模块的接口，通过 `POST /access/points` 在 access 下创建 points。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, label, access_point_group_id, location_id, direction, hw_template_id, last_synced_at}
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /access/points/{access_point_id}
- 作用: 用于当前系统中 Access 模块的接口，通过 `GET /access/points/{access_point_id}` 获取 access 下 points 详情。
- 请求体:
  - 参数:
    - access_point_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### DELETE /access/points/{access_point_id}
- 作用: 用于当前系统中 Access 模块的接口，通过 `DELETE /access/points/{access_point_id}` 删除 access 下 points。
- 请求体:
  - 参数:
    - access_point_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success

### PATCH /access/points/{access_point_id}
- 作用: 用于当前系统中 Access 模块的接口，通过 `PATCH /access/points/{access_point_id}` 更新 access 下 points 部分字段。
- 请求体:
  - 参数:
    - access_point_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, access_point_group_id, location_id, direction, hw_template_id, last_synced_at}
- 响应体:
  - 200: Success | application/json -> object{id, venture_id, venture, location_id, location, label, direction, access_point_group_id, ...}
  - 401: Not allowed

### GET /access/rule-templates
- 作用: 用于当前系统中 Access 模块的接口，通过 `GET /access/rule-templates` 获取 access 下 rule-templates 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter access_rule_templates with a partial label
    - filter[venture_id] (query, string, 可选) - Filter access_rule_templates with an EXACT venture ID
    - include (query, string, 可选) - Sub-resources to include
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /access/rule-templates
- 作用: 用于当前系统中 Access 模块的接口，通过 `POST /access/rule-templates` 在 access 下创建 rule-templates。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, label}
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /access/rule-templates/{access_rule_template_id}
- 作用: 用于当前系统中 Access 模块的接口，通过 `GET /access/rule-templates/{access_rule_template_id}` 获取 access 下 rule-templates 详情。
- 请求体:
  - 参数:
    - access_rule_template_id (path, string, 必填)
    - withCount (query, boolean, 可选)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### DELETE /access/rule-templates/{access_rule_template_id}
- 作用: 用于当前系统中 Access 模块的接口，通过 `DELETE /access/rule-templates/{access_rule_template_id}` 删除 access 下 rule-templates。
- 请求体:
  - 参数:
    - access_rule_template_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success

### PATCH /access/rule-templates/{access_rule_template_id}
- 作用: 用于当前系统中 Access 模块的接口，通过 `PATCH /access/rule-templates/{access_rule_template_id}` 更新 access 下 rule-templates 部分字段。
- 请求体:
  - 参数:
    - access_rule_template_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, rules}
- 响应体:
  - 200: Success | application/json -> object{id, venture_id, venture, location_id, location, label, direction, access_point_group_id, ...}
  - 401: Not allowed

### POST /access/rule-templates/{access_rule_template_id}/mass-update
- 作用: 用于当前系统中 Access 模块的接口，通过 `POST /access/rule-templates/{access_rule_template_id}/mass-update` 更新过期时间。
- 请求体:
  - 参数:
    - access_rule_template_id (path, string, uuid, 必填) - ID of the access rule template group
  - Body:
    - 必填: 是
    - Content-Type: application/json -> object
- 响应体:
  - 200: Batch job created successfully | application/json -> object{data, metadata}

## Account Codes
> Operations related to Account Codes

### GET /account-codes
- 作用: 用于当前系统中 Account Codes 模块的接口，通过 `GET /account-codes` 获取 account-codes 列表。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### POST /account-codes
- 作用: 用于当前系统中 Account Codes 模块的接口，通过 `POST /account-codes` 创建 account-codes。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, account_code, label, vc_id, qweekler_id}
- 响应体:
  - 201: Success
  - 401: Not allowed

### GET /account-codes/{account_code_id}
- 作用: 用于当前系统中 Account Codes 模块的接口，通过 `GET /account-codes/{account_code_id}` 获取 account-codes 详情。
- 请求体:
  - 参数:
    - account_code_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### PATCH /account-codes/{account_code_label}
- 作用: 用于当前系统中 Account Codes 模块的接口，通过 `PATCH /account-codes/{account_code_label}` 更新 account-codes 部分字段。
- 请求体:
  - 参数:
    - account_code_label (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{account_code, label, vc_id, qweekler_id}
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /account-codes/{account_code_label}/usages
- 作用: 用于当前系统中 Account Codes 模块的接口，通过 `GET /account-codes/{account_code_label}/usages` 获取 account-codes 下 usages 列表。
- 请求体:
  - 参数:
    - account_code_label (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

## Agenda Templates
> Operations related to Agenda Templates

### GET /agenda-templates
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `GET /agenda-templates` 获取 agenda-templates 列表，并支持条件筛选、支持分页、支持排序。
- 请求体:
  - 参数:
    - filter[venture_id] (query, string, 可选) - Filter by exact venture ID
    - filter[label] (query, string, 可选) - Filter by partial match on the label
    - filter[archived] (query, string, 可选) - Filter by archive status: all (default), only, exclude
    - include (query, string, 可选) - Load sub-resources in the response
    - sort (query, string, 可选) - Sort by fields. Prefix with "-" for descending. Allowed: label, created_at
    - page (query, integer, 可选) - Page number for pagination
    - per_page (query, integer, 可选) - Number of items per page
  - Body:
    - 无
- 响应体:
  - 200: Paginated list of agenda templates | application/json -> object{data, metadata}

### POST /agenda-templates
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `POST /agenda-templates` 创建 agenda-templates。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, label, color}
- 响应体:
  - 201: Agenda template created successfully | application/json -> object{data, metadata}
  - 422: Validation error

### GET /agenda-templates/{template_id}
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `GET /agenda-templates/{template_id}` 获取 agenda-templates 详情。
- 请求体:
  - 参数:
    - template_id (path, string, uuid, 必填) - UUID of the agenda template
  - Body:
    - 无
- 响应体:
  - 200: Agenda template retrieved successfully | application/json -> object{data}
  - 404: Template not found

### DELETE /agenda-templates/{template_id}
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `DELETE /agenda-templates/{template_id}` 删除 agenda-templates。
- 请求体:
  - 参数:
    - template_id (path, string, uuid, 必填) - UUID of the agenda template
  - Body:
    - 必填: 否
    - Content-Type: application/json -> object
- 响应体:
  - 204: Agenda template successfully deleted
  - 403: Forbidden
  - 404: Template not found

### PATCH /agenda-templates/{template_id}
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `PATCH /agenda-templates/{template_id}` 更新 agenda-templates 部分字段。
- 请求体:
  - 参数:
    - template_id (path, string, uuid, 必填) - UUID of the agenda template to update
  - Body:
    - 必填: 是
    - 描述: Fields to update on an existing agenda template
    - Content-Type: application/json -> object{label, color}
- 响应体:
  - 200: Agenda template updated successfully | application/json -> object{data, metadata}
  - 404: Template not found
  - 422: Validation error

### GET /agenda-templates/{template_id}/agendas
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `GET /agenda-templates/{template_id}/agendas` 获取 agenda-templates 下 agendas 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - template_id (path, string, 必填)
    - filter[starts_between] (query, string, 可选) - Scope templated agendas which start between two datetimes
    - filter[suspended] (query, boolean, 可选) - Filter agendas which are suspended or not suspended
    - filter[activities.id] (query, string, 可选) - Filter agendas with an EXACT activity ID
    - filter[locations.label] (query, string, 可选) - Filter agendas with a partial location Label
    - filter[locations.id] (query, string, 可选) - Filter agendas with an EXACT location ID
    - filter[hour_types.label] (query, string, 可选) - Filter agendas with a partial hour_type Label
    - filter[hour_types.id] (query, string, 可选) - Filter agendas with an EXACT hour_type ID
    - filter[subcategory.label] (query, string, 可选) - Filter agendas with a partial subcategory Label
    - filter[subcategory_id] (query, string, 可选) - Filter agendas with and EXACT subcategory ID
    - filter[day_index] (query, string, 可选) - Filter agendas with and EXACT day_index
    - include (query, string, 可选) - which subresources to include
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /agenda-templates/{template_id}/agendas
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `POST /agenda-templates/{template_id}/agendas` 在 agenda-templates 下创建 agendas。
- 请求体:
  - 参数:
    - template_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{venture_id, subcategory_id, hour_type_id, location_id, day_index, start_at_time, end_at_time, convoc_minutes_before, ...}
- 响应体:
  - 201: Success

### DELETE /agenda-templates/{template_id}/agendas
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `DELETE /agenda-templates/{template_id}/agendas` 删除 agenda-templates 下 agendas。
- 请求体:
  - 参数:
    - template_id (path, string, uuid, 必填) - UUID of the agenda template
  - Body:
    - 必填: 是
    - 描述: Delete one or more templated agendas by ID or by planning axis
    - Content-Type: application/json -> object{agenda_ids, day_index, subcategory_id, location_id}
- 响应体:
  - 204: Templated agendas successfully deleted
  - 403: Forbidden
  - 404: Templated agendas not found

### PATCH /agenda-templates/{template_id}/agendas
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `PATCH /agenda-templates/{template_id}/agendas` 更新 agenda-templates 下 agendas 部分字段。
- 请求体:
  - 参数:
    - template_id (path, string, uuid, 必填) - UUID of the agenda template
  - Body:
    - 必填: 是
    - 描述: Update one or more templated agendas in batch. Update rules may vary depending on agenda grouping (same line, same location, etc).
    - Content-Type: application/json -> object{agenda_ids, data}
- 响应体:
  - 200: Batch update successful | application/json -> object{data, metadata}
  - 404: Agenda template not found
  - 422: Validation error

### POST /agenda-templates/{template_id}/agendas/duplicate
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `POST /agenda-templates/{template_id}/agendas/duplicate` 在 agenda-templates 下创建 duplicate。
- 请求体:
  - 参数:
    - template_id (path, string, 必填)
  - Body:
    - 必填: 是
    - Content-Type: application/json -> object{agenda_ids, direction, multiplier}
- 响应体:
  - 201: Templated agendas successfully duplicated | application/json -> array<ref: TemplatedAgenda>
  - 422: Validation error

### DELETE /agenda-templates/{template_id}/agendas/{agenda_id}
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `DELETE /agenda-templates/{template_id}/agendas/{agenda_id}` 删除 agenda-templates 下 agendas。
- 请求体:
  - 参数:
    - template_id (path, string, uuid, 必填) - UUID of the agenda template
    - agenda_id (path, string, uuid, 必填) - UUID of the templated agenda
  - Body:
    - 必填: 否
    - Content-Type: application/json -> object
- 响应体:
  - 204: Templated agenda successfully deleted
  - 403: Forbidden
  - 404: Templated agenda not found

### PATCH /agenda-templates/{template_id}/agendas/{agenda_id}
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `PATCH /agenda-templates/{template_id}/agendas/{agenda_id}` 更新 agenda-templates 下 agendas 部分字段。
- 请求体:
  - 参数:
    - template_id (path, string, 必填)
    - agenda_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{hour_type_id, short_description, day_index, start_at_time, end_at_time, convoc_minutes_before, convoc_minutes_after, location_capacity, ...}
- 响应体:
  - 200: Success

### POST /agenda-templates/{template_id}/duplicateDay
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `POST /agenda-templates/{template_id}/duplicateDay` 在 agenda-templates 下创建 duplicateDay。
- 请求体:
  - 参数:
    - template_id (path, string, 必填)
  - Body:
    - 必填: 是
    - Content-Type: application/json -> object{source_day_index, destination_day_indices}
- 响应体:
  - 201: Templated agendas duplicated | application/json -> array<ref: TemplatedAgenda>
  - 422: Validation error

### POST /agenda-templates/{template_id}/duplicateLine
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `POST /agenda-templates/{template_id}/duplicateLine` 在 agenda-templates 下创建 duplicateLine。
- 请求体:
  - 参数:
    - template_id (path, string, 必填)
  - Body:
    - 必填: 是
    - Content-Type: application/json -> object{subcategory_id, location_ids}
- 响应体:
  - 201: Templated agendas duplicated | application/json -> array<ref: TemplatedAgenda>
  - 422: Validation error

### POST /agenda-templates/{template_id}/preview
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `POST /agenda-templates/{template_id}/preview` 执行复核操作。
- 请求体:
  - 参数:
    - template_id (path, string, 必填) - UUID of the agenda template (typical_week)
  - Body:
    - 必填: 是
    - 描述: Dates to preview agenda template deployment on
    - Content-Type: application/json -> object{dates}
- 响应体:
  - 202: Preview batch started successfully | application/json -> object{metadata}
  - 404: Template not found
  - 422: Validation error

### GET /agenda-templates/{template_id}/preview/{batch_id}
- 作用: 用于当前系统中 Agenda Templates 模块的接口，通过 `GET /agenda-templates/{template_id}/preview/{batch_id}` 执行复核操作。
- 请求体:
  - 参数:
    - template_id (path, string, 必填) - UUID of the agenda template (typical_week)
    - batch_id (path, string, 必填) - Batch ID associated with the preview request
  - Body:
    - 无
- 响应体:
  - 200: Preview result for the specified batch | application/json -> object{data, metadata}
  - 404: Template or batch not found

## Agendas
> Operations related to Agendas

### GET /agendas
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `GET /agendas` 获取 agendas 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[start_at] (query, string, date-time, 可选) - Filter agendas with a partial start datetime
    - filter[end_at] (query, string, date-time, 可选) - Filter agendas with a partial end datetime
    - filter[starts_between] (query, string, 可选) - Scope agendas which start between two datetimes
    - filter[ends_between] (query, string, 可选) - Scope agendas which end between two datetimes
    - filter[created_at] (query, string, date-time, 可选) - Filter agendas with a partial creation datetime
    - filter[updated_at] (query, string, date-time, 可选) - Filter agendas with a partial update datetime
    - filter[suspended] (query, boolean, 可选) - Filter agendas which are suspended or not suspended
    - filter[venture_id] (query, string, 可选) - Filter agendas with an EXACT venture ID
    - filter[activities.id] (query, string, 可选) - Filter agendas with an EXACT activity ID
    - filter[locations.label] (query, string, 可选) - Filter agendas with a partial location Label
    - filter[locations.id] (query, string, 可选) - Filter agendas with an EXACT location ID
    - filter[hour_types.label] (query, string, 可选) - Filter agendas with a partial hour_type Label
    - filter[hour_types.id] (query, string, 可选) - Filter agendas with an EXACT hour_type ID
    - filter[subcategory.label] (query, string, 可选) - Filter agendas with a partial subcategory Label
    - filter[subcategory_id] (query, string, 可选) - Filter agendas with and EXACT subcategory ID
    - include (query, string, 可选) - Filter agendas with and EXACT subcategory ID
    - withSubcategory (query, boolean, 可选) - Expand agendas with detailed subcategory instead of only ID
    - withLocation (query, boolean, 可选) - Expand agendas with detailed location instead of only ID
    - withHourType (query, boolean, 可选) - Expand agendas with detailed hour_type instead of only ID
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /agendas
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `POST /agendas` 创建 agendas。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, start_at, end_at, convoc_minutes_before, convoc_minutes_after, hour_type_id, subcategory_id, location_id, ...}
- 响应体:
  - 201: Success

### GET /agendas/daily-stats/{year}
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `GET /agendas/daily-stats/{year}` 获取 agendas 下 daily-stats 详情。
- 请求体:
  - 参数:
    - year (path, integer, 必填) - Year to retrieve stats for
    - typical_week_id (query, string, uuid, 可选) - Typical week ID to compare against agendas
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> array<object{date, is_current_typical_week, has_bookings}>
  - 404: Year not found or out of range

### POST /agendas/deploy
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `POST /agendas/deploy` 在 agendas 下创建 deploy。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 必填: 是
    - Content-Type: application/json -> object{mode, agenda_template_id, target_dates, day_index, days_index}
- 响应体:
  - 202: Deployment job queued | application/json -> object{message, metadata}
  - 422: Validation error

### POST /agendas/duplicate
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `POST /agendas/duplicate` 在 agendas 下创建 duplicate。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 必填: 是
    - Content-Type: application/json -> object{venture_id, agenda_ids, direction, multiplier}
- 响应体:
  - 201: Agendas successfully duplicated | application/json -> array<ref: Agenda>
  - 422: Validation error

### POST /agendas/duplicate-day
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `POST /agendas/duplicate-day` 在 agendas 下创建 duplicate-day。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 必填: 是
    - Content-Type: application/json -> object{venture_id, from_date, to_dates}
- 响应体:
  - 201: Agendas duplicated | application/json -> array<ref: Agenda>
  - 422: Validation error

### POST /agendas/mass-update
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `POST /agendas/mass-update` 更新过期时间。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{is_agenda_template, venture_id, location_id, activity_id, agenda_ids, date_range, bookings_limit, arrivals_limit, ...}
- 响应体:
  - 202: Mass update scheduled | application/json -> object{data, metadata}
  - 422: Validation error

### POST /agendas/operational
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `POST /agendas/operational` 在 agendas 下创建 operational。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, start_date, end_date, subCategoriesIds, group_by, start_hour, end_hour}
- 响应体:
  - 201: Success | application/json -> object{message, metadata}
  - 401: Not allowed
  - 422: Validation error

### GET /agendas/{agenda_id}
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `GET /agendas/{agenda_id}` 获取 agendas 详情。
- 请求体:
  - 参数:
    - agenda_id (path, string, 必填) - The ID of the agenda we're querying.
    - include (query, string, 可选) - sub-resources to include
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 404: No Match for query

### DELETE /agendas/{agenda_id}
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `DELETE /agendas/{agenda_id}` 删除 agendas。
- 请求体:
  - 参数:
    - agenda_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success

### PATCH /agendas/{agenda_id}
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `PATCH /agendas/{agenda_id}` 更新 agendas 部分字段。
- 请求体:
  - 参数:
    - agenda_id (path, string, 必填) - The ID of the agenda we want to update.
  - Body:
    - Content-Type: application/json -> object{hour_type_id, start_at, end_at, convoc_minutes_after, convoc_minutes_before, max_occupant, max_incoming, max_booking, ...}
- 响应体:
  - 201: Success

### GET /agendas/{agenda_id}/activities
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `GET /agendas/{agenda_id}/activities` 获取 agendas 下 activities 列表。
- 请求体:
  - 参数:
    - agenda_id (path, string, 必填)
    - withProduct (query, boolean, 可选)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /agendas/{agenda_id}/test-michv8
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `POST /agendas/{agenda_id}/test-michv8` 在 agendas 下创建 test-michv8。
- 请求体:
  - 参数:
    - agenda_id (path, string, uuid, 必填) - The ID of the agenda to recalculate
    - booking_id (query, string, uuid, 可选) - Optional booking ID to include in the recalculation context
  - Body:
    - 无
- 响应体:
  - 200: MichV8 recalculation completed successfully | application/json -> object{message, data}
  - 404: Agenda not found

### GET /agendas/{venture_id}/availabilities-days
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `GET /agendas/{venture_id}/availabilities-days` 获取 agendas 下 availabilities-days 列表。
- 请求体:
  - 参数:
    - venture_id (path, string, 必填)
    - from (query, string, date-time, 必填)
    - to (query, string, date-time, 必填)
    - products (query, array, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, common_days}
  - 404: No Match for query

### GET /agendas/{venture_id}/availabilities-slots
- 作用: 用于当前系统中 Agendas 模块的接口，通过 `GET /agendas/{venture_id}/availabilities-slots` 获取 agendas 下 availabilities-slots 列表。
- 请求体:
  - 参数:
    - venture_id (path, string, 必填)
    - from (query, string, date-time, 必填)
    - to (query, string, date-time, 必填)
    - products (query, array, 必填)
    - isValidStartDate  (query, boolean, 可选)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, common_slots}
  - 404: No Match for query

## Attributes
> Operations related to Attributes

### GET /attributes
- 作用: 用于当前系统中 Attributes 模块的接口，通过 `GET /attributes` 获取 attributes 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[key] (query, string, 可选) - Filter logs with a partial key
    - filter[attributable_id] (query, string, 可选) - Filter logs with a exact attributable_id
    - filter[locale] (query, string, 可选) - Filter logs with a exact lang
    - filter[attributable_type] (query, string, 可选) - Filter logs with a partial attributable type
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

## Auth
> Operations related to Auth

### POST /auth/impersonate
- 作用: 用于当前系统中 Auth 模块的接口，通过 `POST /auth/impersonate` 在 auth 下创建 impersonate。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 必填: 是
    - Content-Type: application/json -> object{slug, token}
- 响应体:
  - 200: Success - returns user_id, token and qweekler_id
  - 401: Invalid or expired token

### POST /users/login
- 作用: 用于当前系统中 Auth 模块的接口，通过 `POST /users/login` 登录并获取认证信息。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{username, password}
- 响应体:
  - 200: Success

### POST /users/logout
- 作用: 用于当前系统中 Auth 模块的接口，通过 `POST /users/logout` 注销当前会话。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 无
- 响应体:
  - 200: Success

### POST /users/pin-login
- 作用: 用于当前系统中 Auth 模块的接口，通过 `POST /users/pin-login` 登录并获取认证信息。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{pin, user_id, is_token_revoked}
- 响应体:
  - 200: Success

## Bookings
> Operations related to Bookings

### GET /bookings
- 作用: 用于当前系统中 Bookings 模块的接口，通过 `GET /bookings` 获取 bookings 列表，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[locations.id] (query, string, 可选) - Filter bookings with an EXACT location ID.
    - filter[agenda.id] (query, string, 可选) - Filter bookings with an EXACT agenda ID
    - filter[venture_id] (query, string, 可选) - Filter bookings with an EXACT venture ID
    - filter[in_category] (query, string, 可选) - Scope bookings with an EXACT category ID
    - filter[in_subcategory] (query, string, 可选) - Scope bookings with an EXACT subcategory ID
    - filter[client_id] (query, string, 可选) - Filter bookings with an EXACT client ID
    - filter[order_id] (query, string, 可选) - Filter bookings with an EXACT order ID
    - filter[order_number] (query, string, 可选) - Filter bookings with an order number containing the given value
    - filter[created_by] (query, string, 可选) - Filter bookings with an EXACT user id
    - filter[locations.label] (query, string, 可选) - Filter bookings with a partial location label.
    - filter[activities.label] (query, string, 可选) - Filter bookings with a partial activity label.
    - filter[activity_id] (query, string, 可选) - Filter bookings on activity_id
    - filter[client_search] (query, string, 可选) - Filter bookings with a partial client email/name/society.
    - filter[recipient_search] (query, string, 可选) - Filter bookings with a partial recipient email/name/society.
    - filter[state] (query, string, 可选) - Filter bookings with an EXACT state.
    - filter[activated] (query, boolean, 可选) - Filter bookings which occupy capacity in your planning
    - filter[agenda.starts_between] (query, string, 可选) - Scope AGENDA which start between two datetimes
    - filter[starts_between] (query, string, 可选) - Scope bookings which start between two datetimes
    - filter[ends_between] (query, string, 可选) - Scope bookings which start between two datetimes
    - filter[created_at] (query, string, date-time, 可选) - Filter bookings with a partial creation datetime
    - filter[created_between] (query, string, 可选) - Scope bookings created between two datetimes
    - filter[updated_at] (query, string, date-time, 可选) - Filter bookings with a partial update datetime
    - filter[order_has_client_note] (query, boolean, 可选) - Filter bookings with client notes.
    - filter[order_has_internal_note] (query, boolean, 可选) - Filter bookings with internal notes.
    - filter[is_pack] (query, boolean, 可选) - Scope only bookings that are part of a package.
    - filter[with_cancelled] (query, boolean, 可选) - Scope only bookings that belongs to a cancelled order
    - filter[expired_after] (query, string, date-time, 可选) - Scope only bookings which order_item are expired after a given date.
    - filter[origin_pos_type] (query, ref: PosType, 可选) - Scope only bookings which order_item with given origin_pos_type.
    - filter[with_custom_tagged_order] (query, string, 可选) - Scope only bookings which order is bearing specified custom_tags.
    - filter[staffed] (query, array, 可选) - Scope only bookings with specific affected staff members.
    - filter[single_capacity] (query, boolean, 可选) - Scope only bookings marked single_capacity.
    - filter[status] (query, ref: OrderStatus, 可选) - Filter bookings with order status.
    - filter[origin] (query, ref: BookingOrigin, 可选) - Filter bookings on origin.
    - sort (query, string, 可选) - Sort results (defaults to -start_at).
    - include (query, string, 可选) - Sub-resources to include
    - filter[orphan] (query, boolean, 可选) - Filter bookings where agenda_id is NULL
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /bookings
- 作用: 用于当前系统中 Bookings 模块的接口，通过 `POST /bookings` 创建 bookings。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id}
- 响应体:
  - 201: Success | application/json -> object{data}

### POST /bookings/export
- 作用: 用于当前系统中 Bookings 模块的接口，通过 `POST /bookings/export` 导出 bookings 相关数据，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[locations.id] (query, string, 可选) - Filter bookings with an EXACT location ID.
    - filter[agenda.id] (query, string, 可选) - Filter bookings with an EXACT agenda ID
    - filter[venture_id] (query, string, 可选) - Filter bookings with an EXACT venture ID
    - filter[in_category] (query, string, 可选) - Scope bookings with an EXACT category ID
    - filter[in_subcategory] (query, string, 可选) - Scope bookings with an EXACT subcategory ID
    - filter[client_id] (query, string, 可选) - Filter bookings with an EXACT client ID
    - filter[order_id] (query, string, 可选) - Filter bookings with an EXACT order ID
    - filter[order_number] (query, string, 可选) - Filter bookings with an order number containing the given value
    - filter[created_by] (query, string, 可选) - Filter bookings with an EXACT user id
    - filter[locations.label] (query, string, 可选) - Filter bookings with a partial location label.
    - filter[activities.label] (query, string, 可选) - Filter bookings with a partial activity label.
    - filter[activity_id] (query, string, 可选) - Filter bookings on activity_id
    - filter[client_search] (query, string, 可选) - Filter bookings with a partial client email/name/society.
    - filter[recipient_search] (query, string, 可选) - Filter bookings with a partial recipient email/name/society.
    - filter[state] (query, string, 可选) - Filter bookings with an EXACT state.
    - filter[activated] (query, boolean, 可选) - Filter bookings which occupy capacity in your planning
    - filter[agenda.starts_between] (query, string, 可选) - Scope bookings which start between two datetimes
    - filter[created_at] (query, string, date-time, 可选) - Filter bookings with a partial creation datetime
    - filter[created_between] (query, string, 可选) - Scope bookings created between two datetimes
    - filter[updated_at] (query, string, date-time, 可选) - Filter bookings with a partial update datetime
    - filter[order_has_client_note] (query, boolean, 可选) - Filter bookings with client notes.
    - filter[order_has_internal_note] (query, boolean, 可选) - Filter bookings with internal notes.
    - filter[is_pack] (query, boolean, 可选) - Scope only bookings that are part of a package.
    - filter[cancelled] (query, boolean, 可选) - Scope only bookings that belongs to a cancelled order
    - filter[expired_after] (query, string, date-time, 可选) - Scope only bookings which order_item are expired after a given date.
    - filter[origin_pos_type] (query, ref: PosType, 可选) - Scope only bookings which order_item with given origin_pos_type.
    - filter[with_custom_tagged_order] (query, string, 可选) - Scope only bookings which order is bearing specified custom_tags.
    - filter[staffed] (query, array, 可选) - Scope only bookings with specific affected staff members.
    - filter[single_capacity] (query, boolean, 可选) - Scope only bookings marked single_capacity.
    - filter[status] (query, ref: OrderStatus, 可选) - Filter bookings with order status.
    - filter[origin] (query, ref: BookingOrigin, 可选) - Filter bookings on origin.
    - sort (query, string, 可选) - Sort results (defaults to -start_at).
  - Body:
    - 无
- 响应体:
  - 200: Success | application/pdf -> object
  - 401: Not allowed

### GET /bookings/{booking_id}
- 作用: 用于当前系统中 Bookings 模块的接口，通过 `GET /bookings/{booking_id}` 获取 bookings 详情。
- 请求体:
  - 参数:
    - booking_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### DELETE /bookings/{booking_id}
- 作用: 用于当前系统中 Bookings 模块的接口，通过 `DELETE /bookings/{booking_id}` 删除 bookings。
- 请求体:
  - 参数:
    - booking_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success

### PATCH /bookings/{booking_id}
- 作用: 用于当前系统中 Bookings 模块的接口，通过 `PATCH /bookings/{booking_id}` 更新 bookings 部分字段。
- 请求体:
  - 参数:
    - booking_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{agenda_id, activated, users, client_subs, invitation_card_id, invitation_card_content, expiration_at}
- 响应体:
  - 200: Success

### PATCH /bookings/{booking_id}/check-in
- 作用: 用于当前系统中 Bookings 模块的接口，通过 `PATCH /bookings/{booking_id}/check-in` 校验 bookings 并返回结果。
- 请求体:
  - 参数:
    - booking_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{check_in_at}
- 响应体:
  - 200: Success | application/json -> object{id, venture_id, sale_item_id, client_id, client, recipient_client_id, recipient, color, ...}

### PATCH /bookings/{booking_id}/check-out
- 作用: 用于当前系统中 Bookings 模块的接口，通过 `PATCH /bookings/{booking_id}/check-out` 校验 bookings 并返回结果。
- 请求体:
  - 参数:
    - booking_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{check_out_at}
- 响应体:
  - 200: Success | application/json -> object{id, venture_id, sale_item_id, client_id, client, recipient_client_id, recipient, color, ...}

### PATCH /bookings/{booking_id}/planning
- 作用: 用于当前系统中 Bookings 模块的接口，通过 `PATCH /bookings/{booking_id}/planning` 更新 bookings 下 planning 部分字段。
- 请求体:
  - 参数:
    - booking_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{agenda_id}
- 响应体:
  - 200: Success | application/json -> object{id, venture_id, sale_item_id, client_id, client, recipient_client_id, recipient, color, ...}

## Cash Diffs
> Operations related to Cash Diffs

### GET /cash-diffs
- 作用: 用于当前系统中 Cash Diffs 模块的接口，通过 `GET /cash-diffs` 获取 cash-diffs 列表。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /cash-diffs/{cash_diff_id}
- 作用: 用于当前系统中 Cash Diffs 模块的接口，通过 `GET /cash-diffs/{cash_diff_id}` 获取 cash-diffs 详情。
- 请求体:
  - 参数:
    - cash_diff_id (path, string, 必填)
    - withAttributes (query, string, 可选) - Expand cashDiff with attributes
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### PATCH /cash-diffs/{cash_diff_id}
- 作用: 用于当前系统中 Cash Diffs 模块的接口，通过 `PATCH /cash-diffs/{cash_diff_id}` 更新 cash-diffs 部分字段。
- 请求体:
  - 参数:
    - cash_diff_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, account_code, type}
- 响应体:
  - 200: Success

## Categories
> Operations related to Categories

### GET /categories
- 作用: 用于当前系统中 Categories 模块的接口，通过 `GET /categories` 获取 categories 列表，并支持条件筛选、支持分页。
- 请求体:
  - 参数:
    - all (query, boolean, 可选) - Return all results without pagination
    - limit (query, integer, 可选) - Maximum number of results when using all=true (default: 500)
    - filter[label] (query, string, 可选) - Filter categories with a partial label.
    - filter[venture_id] (query, string, 可选) - Filter categories with an EXACT venture_id
    - filter[parent_id] (query, string, 可选) - Filter categories with an EXACT parent_id
    - filter[bookable_at] (query, string, date, 可选) - Filter categories that are bookable on a given date
    - filter[plannable] (query, boolean, 可选) - Scope only sub-categories that are plannable.
    - filter[tag_id] (query, string, date, 可选) - Filter categories with an exact tag_id
    - filter[show_archived] (query, boolean, 可选) - Don't filter archived
    - filter[has_parent] (query, boolean, 可选) - Scope categories on wether they have parent or not
    - filter[ids] (query, string, 可选) - Scope categories matching given ids
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /categories
- 作用: 用于当前系统中 Categories 模块的接口，通过 `POST /categories` 创建 categories。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, label, parent_id, vat, sort_order, is_plannable, is_reserved, eshop_label, ...}
- 响应体:
  - 201: Success | application/json -> object{id, venture_id, parent_id, label, image, front_label, front_slug, front_description, ...}
  - 401: Not allowed

### POST /categories/re-order
- 作用: 用于当前系统中 Categories 模块的接口，通过 `POST /categories/re-order` 在 categories 下创建 re-order。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 必填: 是
    - Content-Type: application/json -> object{categories}
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### GET /categories/{category_id}
- 作用: 用于当前系统中 Categories 模块的接口，通过 `GET /categories/{category_id}` 获取 categories 详情。
- 请求体:
  - 参数:
    - category_id (path, string, 必填) - Id of the category we wish to review.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{id, venture_id, parent_id, label, image, front_label, front_slug, front_description, ...}
  - 401: Not allowed

### DELETE /categories/{category_id}
- 作用: 用于当前系统中 Categories 模块的接口，通过 `DELETE /categories/{category_id}` 删除 categories。
- 请求体:
  - 参数:
    - category_id (path, string, 必填) - Id of the category we wish to delete.
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: No Content
  - 401: Not allowed

### PATCH /categories/{category_id}
- 作用: 用于当前系统中 Categories 模块的接口，通过 `PATCH /categories/{category_id}` 更新 categories 部分字段。
- 请求体:
  - 参数:
    - category_id (path, string, 必填) - Id of the category we wish to update.
  - Body:
    - Content-Type: application/json -> object{label, is_plannable, vat, attributes}
- 响应体:
  - 200: Success | application/json -> object{id, venture_id, parent_id, label, image, front_label, front_slug, front_description, ...}
  - 401: Not allowed

### POST /categories/{category_id}/media
- 作用: 用于当前系统中 Categories 模块的接口，通过 `POST /categories/{category_id}/media` 上传或更新 categories 的媒体资源。
- 请求体:
  - 参数:
    - category_id (path, string, 必填)
  - Body:
    - Content-Type: multipart/form-data -> object{image}
- 响应体:
  - 200: Success

## ClientGroups
> Operations related to ClientGroups

### GET /client-groups
- 作用: 用于当前系统中 ClientGroups 模块的接口，通过 `GET /client-groups` 获取 client-groups 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter client groups with a partial label.
    - include (query, string, 可选)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /client-groups
- 作用: 用于当前系统中 ClientGroups 模块的接口，通过 `POST /client-groups` 创建 client-groups。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{label}
- 响应体:
  - 201: Success | application/json -> object{data}
  - 401: Not allowed

### GET /client-groups/{client_group_id}
- 作用: 用于当前系统中 ClientGroups 模块的接口，通过 `GET /client-groups/{client_group_id}` 获取 client-groups 详情。
- 请求体:
  - 参数:
    - client_group_id (path, string, 必填)
    - withAttributes (query, string, 可选) - Expand client with attributes
    - withPaymodes (query, string, 可选) - Expand client with paymodes
    - withProducts (query, string, 可选) - Expand client with products
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### DELETE /client-groups/{client_group_id}
- 作用: 用于当前系统中 ClientGroups 模块的接口，通过 `DELETE /client-groups/{client_group_id}` 删除 client-groups。
- 请求体:
  - 参数:
    - client_group_id (path, string, 必填) - ID of the client group to delete.
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success
  - 401: Not allowed

### PATCH /client-groups/{client_group_id}
- 作用: 用于当前系统中 ClientGroups 模块的接口，通过 `PATCH /client-groups/{client_group_id}` 更新 client-groups 部分字段。
- 请求体:
  - 参数:
    - client_group_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, paymode_ids, product_ids, is_auto_invoice, can_distribute_tickets}
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### POST /client-groups/{client_group_id}/clients
- 作用: 用于当前系统中 ClientGroups 模块的接口，通过 `POST /client-groups/{client_group_id}/clients` 在 client-groups 下创建 clients。
- 请求体:
  - 参数:
    - client_group_id (path, string, 必填) - ID of the client group to which you want to attach clients.
  - Body:
    - Content-Type: application/json -> object{client_ids}
- 响应体:
  - 201: Success | application/json -> object{data}
  - 401: Not allowed

### DELETE /client-groups/{client_group_id}/clients/{client_id}
- 作用: 用于当前系统中 ClientGroups 模块的接口，通过 `DELETE /client-groups/{client_group_id}/clients/{client_id}` 删除 client-groups 下 clients。
- 请求体:
  - 参数:
    - client_group_id (path, string, 必填) - ID of the client group from which you want to detach clients.
    - client_id (path, string, 必填) - ID of the client to detach from the group.
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 201: Success | application/json -> object{data}
  - 401: Not allowed

## Clients
> Operations related to Clients

### GET /clients
- 作用: 用于当前系统中 Clients 模块的接口，通过 `GET /clients` 获取 clients 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - include (query, string, 可选)
    - filter[email] (query, string, email, 可选) - Filter clients with a partial email.
    - filter[lastname] (query, string, 可选) - Filter clients with a partial lastname.
    - filter[firstname] (query, string, 可选) - Filter clients with a partial firstname.
    - filter[society] (query, string, 可选) - Filter clients with a partial society.
    - filter[venture_id] (query, string, 可选) - Filter clients with an EXACT venture ID
    - filter[type] (query, string, 可选) - Filter clients with an EXACT client_type
    - filter[birth_month] (query, integer, 可选) - Scope clients with a given birth month
    - filter[created_after] (query, string, 可选) - Scope clients created after a given datetime
    - filter[created_before] (query, string, 可选) - Scope clients created before a given datetime
    - filter[updated_after] (query, string, 可选) - Scope clients updated after a given datetime
    - filter[updated_before] (query, string, 可选) - Scope clients updated before a given datetime
    - filter[in_group] (query, string, 可选) - Scope clients part of a given client group
    - filter[phone] (query, string, 可选) - Scope clients with a partial phone
    - filter[phone2] (query, string, 可选) - Scope clients with a partial phone2
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /clients
- 作用: 用于当前系统中 Clients 模块的接口，通过 `POST /clients` 创建 clients。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, parent_id, external_id, card_number, type, email, society, register_no, ...}
- 响应体:
  - 201: Success

### POST /clients/export
- 作用: 用于当前系统中 Clients 模块的接口，通过 `POST /clients/export` 导出 clients 相关数据，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[email] (query, string, email, 可选) - Filter clients with a partial email.
    - filter[lastname] (query, string, 可选) - Filter clients with a partial lastname.
    - filter[firstname] (query, string, 可选) - Filter clients with a partial firstname.
    - filter[society] (query, string, 可选) - Filter clients with a partial society.
    - filter[venture_id] (query, string, 可选) - Filter clients with an EXACT venture ID
    - filter[type] (query, string, 可选) - Filter clients with an EXACT client_type
    - filter[birth_month] (query, integer, 可选) - Scope clients with a given birth month
    - filter[created_after] (query, string, 可选) - Scope clients created after a given datetime
    - filter[created_before] (query, string, 可选) - Scope clients created before a given datetime
    - filter[updated_after] (query, string, 可选) - Scope clients updated after a given datetime
    - filter[updated_before] (query, string, 可选) - Scope clients updated before a given datetime
    - filter[in_group] (query, string, 可选) - Scope clients part of a given client group
    - filter[phone] (query, string, 可选) - Scope clients with a partial phone
    - filter[phone2] (query, string, 可选) - Scope clients with a partial phone2
  - Body:
    - 无
- 响应体:
  - 200: Success | application/pdf -> object
  - 401: Not allowed

### GET /clients/{client_id}
- 作用: 用于当前系统中 Clients 模块的接口，通过 `GET /clients/{client_id}` 获取 clients 详情。
- 请求体:
  - 参数:
    - client_id (path, string, 必填) - ID of the client
    - withAttributes (query, boolean, 可选) - Expand client with attributes
    - withGroups (query, boolean, 可选) - Expand client with groups
    - withLoyalties (query, boolean, 可选) - Expand client with loyalties
    - withActiveLoyaltyClient (query, boolean, 可选) - Expand client with active loyaltyClient
    - include (query, string, 可选) - Sub-resources to include.
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 400: Bad request

### DELETE /clients/{client_id}
- 作用: 用于当前系统中 Clients 模块的接口，通过 `DELETE /clients/{client_id}` 删除 clients。
- 请求体:
  - 参数:
    - client_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success

### PATCH /clients/{client_id}
- 作用: 用于当前系统中 Clients 模块的接口，通过 `PATCH /clients/{client_id}` 更新 clients 部分字段。
- 请求体:
  - 参数:
    - client_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{parent_id, external_id, card_number, type, email, society, register_no, vat, ...}
- 响应体:
  - 200: Success

### POST /clients/{client_id}/anonymize
- 作用: 用于当前系统中 Clients 模块的接口，通过 `POST /clients/{client_id}/anonymize` 在 clients 下创建 anonymize。
- 请求体:
  - 参数:
    - client_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Client successfully anonymized | application/json -> object{id, venture_id, card_number, profile_picture, parent_id, type, email, society, ...}
  - 400: Bad Request

### POST /clients/{client_id}/assign-loyalty-step/{loyalty_step_id}/venture/{venture_id}
- 作用: 用于当前系统中 Clients 模块的接口，通过 `POST /clients/{client_id}/assign-loyalty-step/{loyalty_step_id}/venture/{venture_id}` 在 clients 下对 venture 执行创建或业务操作。
- 请求体:
  - 参数:
    - client_id (path, string, 必填) - The client id we wish to attach a loyalty step
    - loyalty_step_id (path, string, 必填) - The loyalty step
    - venture_id (path, string, 必填) - Venture of the step
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 201: Success

### GET /clients/{client_id}/loyalties
- 作用: 用于当前系统中 Clients 模块的接口，通过 `GET /clients/{client_id}/loyalties` 获取 clients 下 loyalties 列表。
- 请求体:
  - 参数:
    - client_id (path, string, 必填) - The client id we wish to list loyalties for
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /clients/{client_id}/loyalties
- 作用: 用于当前系统中 Clients 模块的接口，通过 `POST /clients/{client_id}/loyalties` 在 clients 下创建 loyalties。
- 请求体:
  - 参数:
    - client_id (path, string, 必填) - The client id we wish to attach a loyalty to
  - Body:
    - Content-Type: application/json -> object{venture_id, loyalty_id}
- 响应体:
  - 201: Success

### GET /clients/{client_id}/loyalties/points
- 作用: 用于当前系统中 Clients 模块的接口，通过 `GET /clients/{client_id}/loyalties/points` 获取 clients 下 points 列表。
- 请求体:
  - 参数:
    - client_id (path, string, 必填) - ID of the client whose loyalty points are being retrieved
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 404: Client not found

### DELETE /clients/{client_id}/loyalties/{loyalty_client_id}
- 作用: 用于当前系统中 Clients 模块的接口，通过 `DELETE /clients/{client_id}/loyalties/{loyalty_client_id}` 删除 clients 下 loyalties。
- 请求体:
  - 参数:
    - client_id (path, string, 必填)
    - loyalty_client_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success

### PATCH /clients/{client_id}/loyalties/{loyalty_id}
- 作用: 用于当前系统中 Clients 模块的接口，通过 `PATCH /clients/{client_id}/loyalties/{loyalty_id}` 更新 clients 下 loyalties 部分字段。
- 请求体:
  - 参数:
    - client_id (path, string, 必填)
    - loyalty_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success

### POST /clients/{client_id}/loyalty_client/{loyalty_client_id}/adjust-points
- 作用: 用于当前系统中 Clients 模块的接口，通过 `POST /clients/{client_id}/loyalty_client/{loyalty_client_id}/adjust-points` 在 clients 下创建 adjust-points。
- 请求体:
  - 参数:
    - client_id (path, string, 必填) - Client ID
    - loyalty_client_id (path, string, 必填) - LoyaltyClient ID
  - Body:
    - 必填: 是
    - Content-Type: application/json -> object{pts, action, venture_id}
- 响应体:
  - 201: Point adjustment created successfully
  - 404: Loyalty client not found

### POST /clients/{client_id}/media
- 作用: 用于当前系统中 Clients 模块的接口，通过 `POST /clients/{client_id}/media` 上传或更新 clients 的媒体资源。
- 请求体:
  - 参数:
    - client_id (path, string, 必填)
  - Body:
    - Content-Type: multipart/form-data -> object{profile_picture}
- 响应体:
  - 200: success

### DELETE /clients/{client_id}/media
- 作用: 用于当前系统中 Clients 模块的接口，通过 `DELETE /clients/{client_id}/media` 上传或更新 clients 的媒体资源。
- 请求体:
  - 参数:
    - client_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: success

## Clientsubs
> Operations related to Clientsubs

### GET /clientsubs
- 作用: 用于当前系统中 Clientsubs 模块的接口，通过 `GET /clientsubs` 获取 clientsubs 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[lastname] (query, string, 可选) - Filter client_subs with a partial lastname.
    - filter[firstname] (query, string, 可选) - Filter client_subs with a partial firstname.
    - filter[client_id] (query, string, 可选) - Filter client_subs with an EXACT client ID
    - filter[venture_id] (query, string, 可选) - Filter client_subs with an EXACT venture ID
    - filter[birth_month] (query, integer, 可选) - Scope client_subs with a given birth month
    - filter[created_after] (query, string, 可选) - Scope client_subs created after a given datetime
    - filter[created_before] (query, string, 可选) - Scope client_subs created before a given datetime
    - filter[updated_after] (query, string, 可选) - Scope client_subs updated after a given datetime
    - filter[updated_before] (query, string, 可选) - Scope client_subs updated before a given datetime
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /clientsubs
- 作用: 用于当前系统中 Clientsubs 模块的接口，通过 `POST /clientsubs` 创建 clientsubs。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, client_id, external_id, firstname, lastname, birthday_at, gender, type}
- 响应体:
  - 201: Success

### GET /clientsubs/{clientsub_id}
- 作用: 用于当前系统中 Clientsubs 模块的接口，通过 `GET /clientsubs/{clientsub_id}` 获取 clientsubs 详情。
- 请求体:
  - 参数:
    - clientsub_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### DELETE /clientsubs/{clientsub_id}
- 作用: 用于当前系统中 Clientsubs 模块的接口，通过 `DELETE /clientsubs/{clientsub_id}` 删除 clientsubs。
- 请求体:
  - 参数:
    - clientsub_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success

### PATCH /clientsubs/{clientsub_id}
- 作用: 用于当前系统中 Clientsubs 模块的接口，通过 `PATCH /clientsubs/{clientsub_id}` 更新 clientsubs 部分字段。
- 请求体:
  - 参数:
    - clientsub_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{external_id, firstname, lastname, birthday_at, gender, type}
- 响应体:
  - 200: Success

## CustomTags
> Operations related to CustomTags

### GET /custom-tags
- 作用: 用于当前系统中 CustomTags 模块的接口，通过 `GET /custom-tags` 获取 custom-tags 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filters CustomTags on a PARTIAL label
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /custom-tags
- 作用: 用于当前系统中 CustomTags 模块的接口，通过 `POST /custom-tags` 创建 custom-tags。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{label, color, icon}
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### GET /custom-tags/{custom_tag_id}
- 作用: 用于当前系统中 CustomTags 模块的接口，通过 `GET /custom-tags/{custom_tag_id}` 获取 custom-tags 详情。
- 请求体:
  - 参数:
    - custom_tag_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### DELETE /custom-tags/{custom_tag_id}
- 作用: 用于当前系统中 CustomTags 模块的接口，通过 `DELETE /custom-tags/{custom_tag_id}` 删除 custom-tags。
- 请求体:
  - 参数:
    - custom_tag_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### PATCH /custom-tags/{custom_tag_id}
- 作用: 用于当前系统中 CustomTags 模块的接口，通过 `PATCH /custom-tags/{custom_tag_id}` 更新 custom-tags 部分字段。
- 请求体:
  - 参数:
    - custom_tag_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, color, icon}
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

## Event Logs
> Operations related to Event Logs

### GET /event-logs
- 作用: 用于当前系统中 Event Logs 模块的接口，通过 `GET /event-logs` 获取 event-logs 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[action] (query, string, 可选) - Filter logs with a partial action
    - filter[venture_id] (query, string, 可选) - Filter logs with a exact venture_id
    - filter[user_id] (query, string, 可选) - Filter logs with a exact user_id
    - filter[loggable_id] (query, string, 可选) - Filter logs with a exact loggable_id
    - include (query, string, 可选) - Include subresources
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### GET /event-logs/{event_log_id}
- 作用: 用于当前系统中 Event Logs 模块的接口，通过 `GET /event-logs/{event_log_id}` 获取 event-logs 详情。
- 请求体:
  - 参数:
    - event_log_id (path, string, 必填) - Filter logs with a partial action
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 404: No Match for query

## Export
> Operations related to Export

### GET /export/acceptances
- 作用: 用于当前系统中 Export 模块的接口，通过 `GET /export/acceptances` 获取 export 下 acceptances 列表。
- 请求体:
  - 参数:
    - fields (query, string, 必填) - Fields to export, comma separated, ex: id, created_at 
    - filters (query, string, 可选) - Filters to apply
  - Body:
    - 无
- 响应体:
  - 200: Success | text/csv -> string
  - 401: Not allowed

### GET /export/activities
- 作用: 用于当前系统中 Export 模块的接口，通过 `GET /export/activities` 获取 export 下 activities 列表。
- 请求体:
  - 参数:
    - fields (query, string, 必填) - Fields to export, comma separated, ex: id, created_at 
    - filters (query, string, 可选) - Filters to apply
  - Body:
    - 无
- 响应体:
  - 200: Success | text/csv -> string
  - 401: Not allowed

### GET /export/bookings
- 作用: 用于当前系统中 Export 模块的接口，通过 `GET /export/bookings` 获取 export 下 bookings 列表。
- 请求体:
  - 参数:
    - fields (query, string, 必填) - Fields to export, comma separated, ex: id, created_at 
    - filters (query, string, 可选) - Filters to apply
  - Body:
    - 无
- 响应体:
  - 200: Success | text/csv -> string
  - 401: Not allowed

### GET /export/categories
- 作用: 用于当前系统中 Export 模块的接口，通过 `GET /export/categories` 获取 export 下 categories 列表。
- 请求体:
  - 参数:
    - fields (query, string, 必填) - Fields to export, comma separated, ex: id, created_at 
    - filters (query, string, 可选) - Filters to apply
  - Body:
    - 无
- 响应体:
  - 200: Success | text/csv -> string
  - 401: Not allowed

### GET /export/client_subs
- 作用: 用于当前系统中 Export 模块的接口，通过 `GET /export/client_subs` 获取 export 下 client_subs 列表。
- 请求体:
  - 参数:
    - fields (query, string, 必填) - Fields to export, comma separated, ex: id, created_at 
    - filters (query, string, 可选) - Filters to apply
  - Body:
    - 无
- 响应体:
  - 200: Success | text/csv -> string
  - 401: Not allowed

### GET /export/clients
- 作用: 用于当前系统中 Export 模块的接口，通过 `GET /export/clients` 获取 export 下 clients 列表。
- 请求体:
  - 参数:
    - fields (query, string, 必填) - Fields to export, comma separated, ex: id, created_at 
    - filters (query, string, 可选) - Filters to apply
  - Body:
    - 无
- 响应体:
  - 200: Success | text/csv -> string
  - 401: Not allowed

### GET /export/metadata
- 作用: 用于当前系统中 Export 模块的接口，通过 `GET /export/metadata` 获取 export 下 metadata 列表。
- 请求体:
  - 参数:
    - fields (query, string, 必填) - Fields to export, comma separated, ex: id, created_at 
    - filters (query, string, 可选) - Filters to apply
  - Body:
    - 无
- 响应体:
  - 200: Success | text/csv -> string
  - 401: Not allowed

### GET /export/pos
- 作用: 用于当前系统中 Export 模块的接口，通过 `GET /export/pos` 获取 export 下 pos 列表。
- 请求体:
  - 参数:
    - fields (query, string, 必填) - Fields to export, comma separated, ex: id, created_at 
    - filters (query, string, 可选) - Filters to apply
  - Body:
    - 无
- 响应体:
  - 200: Success | text/csv -> string
  - 401: Not allowed

### GET /export/pos_sessions
- 作用: 用于当前系统中 Export 模块的接口，通过 `GET /export/pos_sessions` 获取 export 下 pos_sessions 列表。
- 请求体:
  - 参数:
    - fields (query, string, 必填) - Fields to export, comma separated, ex: id, created_at 
    - filters (query, string, 可选) - Filters to apply
  - Body:
    - 无
- 响应体:
  - 200: Success | text/csv -> string
  - 401: Not allowed

### GET /export/products
- 作用: 用于当前系统中 Export 模块的接口，通过 `GET /export/products` 获取 export 下 products 列表。
- 请求体:
  - 参数:
    - fields (query, string, 必填) - Fields to export, comma separated, ex: id, created_at 
    - filters (query, string, 可选) - Filters to apply
  - Body:
    - 无
- 响应体:
  - 200: Success | text/csv -> string
  - 401: Not allowed

### GET /export/sale_items
- 作用: 用于当前系统中 Export 模块的接口，通过 `GET /export/sale_items` 获取 export 下 sale_items 列表。
- 请求体:
  - 参数:
    - fields (query, string, 必填) - Fields to export, comma separated, ex: id, created_at 
    - filters (query, string, 可选) - Filters to apply
  - Body:
    - 无
- 响应体:
  - 200: Success | text/csv -> string
  - 401: Not allowed

### GET /export/sales
- 作用: 用于当前系统中 Export 模块的接口，通过 `GET /export/sales` 获取 export 下 sales 列表。
- 请求体:
  - 参数:
    - fields (query, string, 必填) - Fields to export, comma separated, ex: id, number, created_at 
    - filters (query, string, 可选) - Filters to apply
  - Body:
    - 无
- 响应体:
  - 200: Success | text/csv -> string
  - 401: Not allowed

### GET /export/ventures
- 作用: 用于当前系统中 Export 模块的接口，通过 `GET /export/ventures` 获取 export 下 ventures 列表。
- 请求体:
  - 参数:
    - fields (query, string, 必填) - Fields to export, comma separated, ex: id, created_at 
    - filters (query, string, 可选) - Filters to apply
  - Body:
    - 无
- 响应体:
  - 200: Success | text/csv -> string
  - 401: Not allowed

## Gift Cards
> Operations related to Gift Cards

### GET /gift-cards
- 作用: 用于当前系统中 Gift Cards 模块的接口，通过 `GET /gift-cards` 获取 gift-cards 列表，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[token] (query, string, 可选) - Filter gift_cards with a partial Token/Code
    - filter[label] (query, string, 可选) - Filter gift_cards with a partial label
    - filter[client_id] (query, string, 可选) - Filter gift_cards with an EXACT client ID
    - filter[sale_id] (query, string, 可选) - Filter gift_cards with an EXACT sale ID
    - filter[sale_number] (query, string, 可选) - Filter gift_cards with an EXACT sale number
    - filter[order_id] (query, string, 可选) - Scope gift_cards with an EXACT order ID
    - filter[venture_id] (query, string, 可选) - Filter gift_cards with an EXACT venture ID
    - filter[client_user] (query, string, 可选) - Filter gift_cards by the user client's name, email or id
    - filter[owner] (query, string, 可选) - Filter gift_cards by the owner's name, email or id
    - filter[created_at] (query, string, date-time, 可选) - Filter gift_cards with a partial creation datetime
    - filter[updated_at] (query, string, date-time, 可选) - Filter gift_cards with a partial update datetime
    - filter[valid_between] (query, string, 可选) - Filter gift_cards valid until between two dates (comma separated)
    - filter[sold_between] (query, string, 可选) - Filter gift_cards sold between two dates (comma separated)
    - include (query, string, 可选) - List of sub-resources to include
    - sort (query, string, 可选) - Sort gift_cards by creation date, updated date, sale number, token (code) or label. Prefix with - for descending order.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /gift-cards
- 作用: 用于当前系统中 Gift Cards 模块的接口，通过 `POST /gift-cards` 创建 gift-cards。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 必填: 是
    - Content-Type: application/json -> object{provider_id, product_id, value, stringable_token}
- 响应体:
  - 201: Gift Card successfully created. | application/json -> object{data}
  - 400: Invalid input

### POST /gift-cards/export
- 作用: 用于当前系统中 Gift Cards 模块的接口，通过 `POST /gift-cards/export` 导出 gift-cards 相关数据，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[token] (query, string, 可选) - Filter gift_cards with a partial Token/Code
    - filter[label] (query, string, 可选) - Filter gift_cards with a partial label
    - filter[client_id] (query, string, 可选) - Filter gift_cards with an EXACT client ID
    - filter[owner_id] (query, string, 可选) - Filter gift_cards with an EXACT owner ID
    - filter[sale_id] (query, string, 可选) - Filter gift_cards with an EXACT sale ID
    - filter[order_id] (query, string, 可选) - Scope gift_cards with an EXACT order ID
    - filter[venture_id] (query, string, 必填) - Filter gift_cards with an EXACT venture ID
    - filter[client_user] (query, string, 可选) - Filter gift_cards by the user client's name, email or id
    - filter[owner] (query, string, 可选) - Filter gift_cards by the owner's name, email or id
    - filter[created_at] (query, string, date-time, 可选) - Filter gift_cards with a partial creation datetime
    - filter[updated_at] (query, string, date-time, 可选) - Filter gift_cards with a partial update datetime
    - filter[valid_between] (query, string, 可选) - Filter gift_cards valid until between two dates (comma separated)
    - filter[sold_between] (query, string, 可选) - Filter gift_cards sold between two dates (comma separated)
    - sort (query, string, 可选) - Sort gift_cards by creation date, updated date, sale number, token (code) or label. Prefix with - for descending order.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/pdf -> object
  - 401: Not allowed

### GET /gift-cards/{giftcard_id}
- 作用: 用于当前系统中 Gift Cards 模块的接口，通过 `GET /gift-cards/{giftcard_id}` 获取 gift-cards 详情。
- 请求体:
  - 参数:
    - giftcard_id (path, string, 必填) - The ID of the Gift Card we wish to get.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### PATCH /gift-cards/{giftcard_id}
- 作用: 用于当前系统中 Gift Cards 模块的接口，通过 `PATCH /gift-cards/{giftcard_id}` 更新 gift-cards 部分字段。
- 请求体:
  - 参数:
    - giftcard_id (path, string, 必填) - The ID of the Gift Card to update.
  - Body:
    - Content-Type: application/json -> object{token, valid_until_at}
- 响应体:
  - 200: Gift Card successfully disabled. | application/json -> object{data}
  - 401: Not allowed
  - 404: Gift Card not found

### PATCH /gift-cards/{giftcard_id}/disable
- 作用: 用于当前系统中 Gift Cards 模块的接口，通过 `PATCH /gift-cards/{giftcard_id}/disable` 更新 gift-cards 下 disable 部分字段。
- 请求体:
  - 参数:
    - giftcard_id (path, string, 必填) - The ID of the Gift Card to disable.
  - Body:
    - 无
- 响应体:
  - 200: Gift Card successfully disabled. | application/json -> object{data}
  - 401: Not allowed
  - 404: Gift Card not found

### GET /gift-cards/{giftcard_id}/pdf
- 作用: 用于当前系统中 Gift Cards 模块的接口，通过 `GET /gift-cards/{giftcard_id}/pdf` 生成或获取 gift-cards 的 PDF 文件。
- 请求体:
  - 参数:
    - giftcard_id (path, string, 必填) - The giftcard_id of the GiftCard we wish to get.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/pdf -> object
  - 202: Accepted | application/json -> object
  - 401: Not allowed

### GET /gift-cards/{giftcard_id}/usages
- 作用: 用于当前系统中 Gift Cards 模块的接口，通过 `GET /gift-cards/{giftcard_id}/usages` 获取 gift-cards 下 usages 列表。
- 请求体:
  - 参数:
    - giftcard_id (path, string, 必填) - The ID of the Gift Card we wish to list usages for.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

## Hour Types
> Operations related to Hour Types

### GET /hour-types
- 作用: 用于当前系统中 Hour Types 模块的接口，通过 `GET /hour-types` 获取 hour-types 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter hour types with a PARTIAL label
    - filter[venture_id] (query, string, 可选) - Filter hour types with an EXACT venture_id
    - filter[archived] (query, string, 可选) - Filter by archive status: all, only, exclude (default)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /hour-types
- 作用: 用于当前系统中 Hour Types 模块的接口，通过 `POST /hour-types` 创建 hour-types。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, label, color}
- 响应体:
  - 201: Success | application/json -> object{data}

### GET /hour-types/{hour_type_id}
- 作用: 用于当前系统中 Hour Types 模块的接口，通过 `GET /hour-types/{hour_type_id}` 获取 hour-types 详情。
- 请求体:
  - 参数:
    - hour_type_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### DELETE /hour-types/{hour_type_id}
- 作用: 用于当前系统中 Hour Types 模块的接口，通过 `DELETE /hour-types/{hour_type_id}` 删除 hour-types。
- 请求体:
  - 参数:
    - hour_type_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success

### PATCH /hour-types/{hour_type_id}
- 作用: 用于当前系统中 Hour Types 模块的接口，通过 `PATCH /hour-types/{hour_type_id}` 更新 hour-types 部分字段。
- 请求体:
  - 参数:
    - hour_type_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, color}
- 响应体:
  - 200: Success

## Inventories
> Operations related to Inventories

### GET /inventories
- 作用: 用于当前系统中 Inventories 模块的接口，通过 `GET /inventories` 获取 inventories 列表，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter inventories with a partial label.
    - filter[venture_id] (query, string, 可选) - Filter inventories with an exact venture id.
    - filter[validated_at_between] (query, string, 可选) - Filter inventories with the date validated_at.
    - filter[created_at_between] (query, string, 可选) - Filter inventories with the date validated_at.
    - sort (query, string, 可选) - Sort results.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 401: Not allowed
  - 404: No Match for query

### POST /inventories
- 作用: 用于当前系统中 Inventories 模块的接口，通过 `POST /inventories` 创建 inventories。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{label, venture_id}
- 响应体:
  - 201: Success | application/json -> object{data}
  - 401: Not allowed

### GET /inventories/{inventory_id}
- 作用: 用于当前系统中 Inventories 模块的接口，通过 `GET /inventories/{inventory_id}` 获取 inventories 详情。
- 请求体:
  - 参数:
    - inventory_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### DELETE /inventories/{inventory_id}
- 作用: 用于当前系统中 Inventories 模块的接口，通过 `DELETE /inventories/{inventory_id}` 删除 inventories。
- 请求体:
  - 参数:
    - inventory_id (path, string, 必填) - ID of the inventory to delete.
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success
  - 401: Not allowed

### PATCH /inventories/{inventory_id}
- 作用: 用于当前系统中 Inventories 模块的接口，通过 `PATCH /inventories/{inventory_id}` 更新 inventories 部分字段。
- 请求体:
  - 参数:
    - inventory_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label}
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### POST /inventories/{inventory_id}/validate
- 作用: 用于当前系统中 Inventories 模块的接口，通过 `POST /inventories/{inventory_id}/validate` 在 inventories 下创建 validate。
- 请求体:
  - 参数:
    - inventory_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{inventory_product_ids}
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

## InventoryItems
> Operations related to InventoryItems

### GET /inventories/{inventory_id}/items
- 作用: 用于当前系统中 InventoryItems 模块的接口，通过 `GET /inventories/{inventory_id}/items` 获取 inventories 下 items 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - inventory_id (path, string, 必填)
    - filter[sku] (query, string, 可选) - Filter inventory products with a SKU.
    - filter[product_label] (query, string, 可选) - Filter inventory products with a partial label.
    - filter[supplier_ref] (query, string, 可选) - Filter inventory products with an exact supplier reference.
    - filter[supplier_label] (query, string, 可选) - Filter inventory products with a partial supplier label.
    - filter[subcategory_label] (query, string, 可选) - Filter inventory products with a partial subcategory label.
    - filter[category_label] (query, string, 可选) - Filter inventory products with a partial category label.
    - filter[under_stock_alert] (query, boolean, 可选) - Scope products under their stock alert threshold
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 401: Not allowed
  - 404: No Match for query

### DELETE /inventories/{inventory_id}/items/{item_id}
- 作用: 用于当前系统中 InventoryItems 模块的接口，通过 `DELETE /inventories/{inventory_id}/items/{item_id}` 删除 inventories 下 items。
- 请求体:
  - 参数:
    - inventory_id (path, string, 必填)
    - item_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success
  - 401: Not allowed

### PATCH /inventories/{inventory_id}/items/{item_id}
- 作用: 用于当前系统中 InventoryItems 模块的接口，通过 `PATCH /inventories/{inventory_id}/items/{item_id}` 更新 inventories 下 items 部分字段。
- 请求体:
  - 参数:
    - inventory_id (path, string, 必填)
    - item_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{inventoried_qty, venture_id, comment}
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

## Invitation Cards
> Operations related to Invitation Cards

### GET /invitation-cards
- 作用: 用于当前系统中 Invitation Cards 模块的接口，通过 `GET /invitation-cards` 获取 invitation-cards 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter invitation cards with a PARTIAL label
    - filter[venture_id] (query, string, 可选) - Filter invitation cards with an exact venture_id
    - filter[archived] (query, string, 可选) - Filter by archive status: all, only, exclude (default)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /invitation-cards
- 作用: 用于当前系统中 Invitation Cards 模块的接口，通过 `POST /invitation-cards` 创建 invitation-cards。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{label, card_format, default_content, font, text_color, font_size, positions}
- 响应体:
  - 201: Success | application/json -> object{data}

### POST /invitation-cards/pdf
- 作用: 用于当前系统中 Invitation Cards 模块的接口，通过 `POST /invitation-cards/pdf` 生成或获取 invitation-cards 的 PDF 文件。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{booking_id, invitation_card_id, markdown}
- 响应体:
  - 200: Success | application/pdf -> object
  - 202: Accepted | application/json -> object
  - 401: Not allowed

### GET /invitation-cards/{invitation_card_id}
- 作用: 用于当前系统中 Invitation Cards 模块的接口，通过 `GET /invitation-cards/{invitation_card_id}` 获取 invitation-cards 详情。
- 请求体:
  - 参数:
    - invitation_card_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### DELETE /invitation-cards/{invitation_card_id}
- 作用: 用于当前系统中 Invitation Cards 模块的接口，通过 `DELETE /invitation-cards/{invitation_card_id}` 删除 invitation-cards。
- 请求体:
  - 参数:
    - invitation_card_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success

### PATCH /invitation-cards/{invitation_card_id}
- 作用: 用于当前系统中 Invitation Cards 模块的接口，通过 `PATCH /invitation-cards/{invitation_card_id}` 更新 invitation-cards 部分字段。
- 请求体:
  - 参数:
    - invitation_card_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, card_format, default_content, font, text_color, font_size, positions}
- 响应体:
  - 200: Success

### POST /invitation-cards/{invitation_card_id}/media
- 作用: 用于当前系统中 Invitation Cards 模块的接口，通过 `POST /invitation-cards/{invitation_card_id}/media` 上传或更新 invitation-cards 的媒体资源。
- 请求体:
  - 参数:
    - invitation_card_id (path, string, 必填)
  - Body:
    - Content-Type: multipart/form-data -> object{image}
- 响应体:
  - 200: Success

### DELETE /invitation-cards/{invitation_card_id}/media
- 作用: 用于当前系统中 Invitation Cards 模块的接口，通过 `DELETE /invitation-cards/{invitation_card_id}/media` 上传或更新 invitation-cards 的媒体资源。
- 请求体:
  - 参数:
    - invitation_card_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: success

## Invoices
> Operations related to Invoices

### GET /invoices
- 作用: 用于当前系统中 Invoices 模块的接口，通过 `GET /invoices` 获取 invoices 列表，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[number] (query, string, 可选) - Filter invoices with number
    - filter[sale_number] (query, string, 可选) - Filter invoices with sale_number
    - filter[client_id] (query, string, 可选) - Filter invoices with client_id
    - filter[buyer] (query, string, 可选) - Scope invoices by buyer name
    - filter[venture_id] (query, string, 可选) - Filter invoices with venture_id
    - filter[pos_session_id] (query, string, 可选) - Filter invoices with pos_session_id
    - filter[created_after] (query, string, 可选) - Scope invoices created after a given datetime
    - filter[created_before] (query, string, 可选) - Scope invoices created before a given datetime
    - filter[created_between] (query, string, 可选) - Scope invoices created before a given datetime
    - filter[sold_between] (query, string, 可选) - Scope invoices created before a given datetime
    - filter[updated_after] (query, string, 可选) - Scope invoices updated after a given datetime
    - filter[updated_before] (query, string, 可选) - Scope invoices updated before a given datetime
    - filter[created_at] (query, string, 可选) - Scope invoices created at a given datetime (partial)
    - filter[updated_at] (query, string, 可选) - Scope invoices updated at a given datetime (partial)
    - include (query, string, 可选) - Subresources to include.
    - sort (query, string, 可选) - sort by field
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 401: Not allowed

### POST /invoices/export
- 作用: 用于当前系统中 Invoices 模块的接口，通过 `POST /invoices/export` 导出 invoices 相关数据，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[number] (query, string, 可选) - Filter invoices with number
    - filter[client_id] (query, string, 可选) - Filter invoices with client_id
    - filter[venture_id] (query, string, 可选) - Filter invoices with venture_id
    - filter[pos_session_id] (query, string, 可选) - Filter invoices with pos_session_id
    - filter[created_after] (query, string, 可选) - Scope invoices created after a given datetime
    - filter[created_before] (query, string, 可选) - Scope invoices created before a given datetime
    - filter[updated_after] (query, string, 可选) - Scope invoices updated after a given datetime
    - filter[updated_before] (query, string, 可选) - Scope invoices updated before a given datetime
    - filter[created_at] (query, string, 可选) - Scope invoices created at a given datetime (partial)
    - filter[updated_at] (query, string, 可选) - Scope invoices updated at a given datetime (partial)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/pdf -> object
  - 401: Not allowed

### GET /invoices/{invoice_id}
- 作用: 用于当前系统中 Invoices 模块的接口，通过 `GET /invoices/{invoice_id}` 获取 invoices 详情。
- 请求体:
  - 参数:
    - invoice_id (path, string, 必填) - The invoice_id of the Invoice we wish to get.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### GET /invoices/{invoice_id}/pdf
- 作用: 用于当前系统中 Invoices 模块的接口，通过 `GET /invoices/{invoice_id}/pdf` 生成或获取 invoices 的 PDF 文件。
- 请求体:
  - 参数:
    - invoice_id (path, string, 必填) - The invoice_id of the Invoice we wish to get.
  - Body:
    - Content-Type: application/json -> object{pos_id}
- 响应体:
  - 200: Success | application/pdf -> object
  - 401: Not allowed

### POST /invoices/{invoice_id}/send-mails
- 作用: 用于当前系统中 Invoices 模块的接口，通过 `POST /invoices/{invoice_id}/send-mails` 执行发送操作。
- 请求体:
  - 参数:
    - invoice_id (path, string, 必填) - The invoice_id of the Invoice we wish to send delivrables for.
  - Body:
    - Content-Type: application/json -> object{recipients_mails}
- 响应体:
  - 200: Success
  - 401: Not allowed

## Jobs
> Operations related to Jobs

### GET /jobs/{batched_job_id}/progress
- 作用: 用于当前系统中 Jobs 模块的接口，通过 `GET /jobs/{batched_job_id}/progress` 获取 jobs 下 progress 列表。
- 请求体:
  - 参数:
    - batched_job_id (path, string, 必填) - The UUID of the job batch
  - Body:
    - 无
- 响应体:
  - 200: Batch progress retrieved successfully | application/json -> object{data, metadata}
  - 404: Batched job not found

## Locations
> Operations related to Locations

### GET /location/{location_id}
- 作用: 用于当前系统中 Locations 模块的接口，通过 `GET /location/{location_id}` 获取 location 详情。
- 请求体:
  - 参数:
    - location_id (path, string, 必填)
    - withAttributes (query, string, 可选) - Expand location with attributes
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 404: No Match for query

### GET /locations
- 作用: 用于当前系统中 Locations 模块的接口，通过 `GET /locations` 获取 locations 列表，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter locations with a partial label.
    - filter[subcategory_id] (query, string, 可选) - Filter locations with an EXACT subcategory_id.
    - filter[venture_id] (query, string, 可选) - Filter locations with an EXACT venture ID
    - filter[ids] (query, string, 可选) - Scope locations matching given ids.
    - filter[archived] (query, string, 可选) - Filter by archive status: all (default), only, exclude
    - include (query, string, 可选) - Sub-resources to include
    - sort (query, string, 可选) - Sort by fields
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /locations
- 作用: 用于当前系统中 Locations 模块的接口，通过 `POST /locations` 创建 locations。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, label, slug, description, capacity, sort_order, image, bookable, ...}
- 响应体:
  - 201: Success

### DELETE /locations/{location_id}
- 作用: 用于当前系统中 Locations 模块的接口，通过 `DELETE /locations/{location_id}` 删除 locations。
- 请求体:
  - 参数:
    - location_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: No content

### PATCH /locations/{location_id}
- 作用: 用于当前系统中 Locations 模块的接口，通过 `PATCH /locations/{location_id}` 更新 locations 部分字段。
- 请求体:
  - 参数:
    - location_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, slug, description, capacity, sort_order, image, bookable, attributes}
- 响应体:
  - 200: Success

### POST /locations/{location_id}/media
- 作用: 用于当前系统中 Locations 模块的接口，通过 `POST /locations/{location_id}/media` 上传或更新 locations 的媒体资源。
- 请求体:
  - 参数:
    - location_id (path, string, 必填)
  - Body:
    - Content-Type: multipart/form-data -> object{image}
- 响应体:
  - 200: Success

### DELETE /locations/{location_id}/media
- 作用: 用于当前系统中 Locations 模块的接口，通过 `DELETE /locations/{location_id}/media` 上传或更新 locations 的媒体资源。
- 请求体:
  - 参数:
    - location_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: success

## Loyalties
> Operations related to Loyalties

### GET /loyalties
- 作用: 用于当前系统中 Loyalties 模块的接口，通过 `GET /loyalties` 获取 loyalties 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter loyalties with label
    - filter[venture_id] (query, string, 可选) - Filter loyalties with venture_id
    - include (query, string, 可选) - Expand loyalties with steps array
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 401: Not allowed

### POST /loyalties
- 作用: 用于当前系统中 Loyalties 模块的接口，通过 `POST /loyalties` 创建 loyalties。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, label, ticket_reward, validity, is_default, pts_precision, unit, enabled}
- 响应体:
  - 200: Success

### GET /loyalties/{loyalty_id}
- 作用: 用于当前系统中 Loyalties 模块的接口，通过 `GET /loyalties/{loyalty_id}` 获取 loyalties 详情。
- 请求体:
  - 参数:
    - loyalty_id (path, string, 必填)
    - withSteps (query, boolean, 可选) - Expand loyalty with steps array
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### DELETE /loyalties/{loyalty_id}
- 作用: 用于当前系统中 Loyalties 模块的接口，通过 `DELETE /loyalties/{loyalty_id}` 删除 loyalties。
- 请求体:
  - 参数:
    - loyalty_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success

### PATCH /loyalties/{loyalty_id}
- 作用: 用于当前系统中 Loyalties 模块的接口，通过 `PATCH /loyalties/{loyalty_id}` 更新 loyalties 部分字段。
- 请求体:
  - 参数:
    - loyalty_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, ticket_reward, validity, is_default, pts_precision, unit, enabled, steps}
- 响应体:
  - 200: Success

## Marketing Automations
> Operations related to Marketing Automations

### GET /marketing-automations
- 作用: 用于当前系统中 Marketing Automations 模块的接口，通过 `GET /marketing-automations` 获取 marketing-automations 列表。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

## Notification Logs
> Operations related to Notification Logs

### GET /notification-logs
- 作用: 用于当前系统中 Notification Logs 模块的接口，通过 `GET /notification-logs` 获取 notification-logs 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[venture_id] (query, string, 可选) - Filter notification_logs with an exact venture_id
    - filter[notifiable_id] (query, string, 可选) - Filter notification_logs with an exact notifiable_id
    - filter[notifiable_type] (query, string, 可选) - Filter notification_logs with an exact notifiable_type
    - filter[notification_type] (query, string, 可选) - Filter notification_logs with an exact notification_type
    - filter[channel] (query, ref: NotificationChannels, 可选) - Filter notification_logs with an exact channel
    - filter[extra] (query, object, 可选) - Filter notification_logs whose extra column contains the provided key/value pairs (bound by: AND).
    - include (query, string, 可选) - Include related resources (comma-separated)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### GET /notification-logs/{notification_log_id}
- 作用: 用于当前系统中 Notification Logs 模块的接口，通过 `GET /notification-logs/{notification_log_id}` 获取 notification-logs 详情。
- 请求体:
  - 参数:
    - notification_log_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 404: No Match for query

### DELETE /notification-logs/{notification_log_id}
- 作用: 用于当前系统中 Notification Logs 模块的接口，通过 `DELETE /notification-logs/{notification_log_id}` 删除 notification-logs。
- 请求体:
  - 参数:
    - notification_log_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success

### PATCH /notification-logs/{notification_log_id}
- 作用: 用于当前系统中 Notification Logs 模块的接口，通过 `PATCH /notification-logs/{notification_log_id}` 更新 notification-logs 部分字段。
- 请求体:
  - 参数:
    - notification_log_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{fingerprint, extra, anonymous_notifiable_properties, read_at}
- 响应体:
  - 200: Success

## Order Items
> Operations related to Order Items

### POST /orders/{order_id}/items
- 作用: 用于当前系统中 Order Items 模块的接口，通过 `POST /orders/{order_id}/items` 在 orders 下创建 items。
- 请求体:
  - 参数:
    - order_id (path, integer, int64, 必填) - Identifiant de la commande
  - Body:
    - 必填: 是
    - 描述: Données de l'élément de commande
    - Content-Type: application/json -> object{pos_session_id, order_item_data}
- 响应体:
  - 200: Élément de commande créé avec succès | application/json -> array<ref: OrderItem>
  - 401: Non autorisé
  - 404: Session POS non trouvée
  - 422: Données invalides

### GET /orders/{order_id}/items/{order_item_id}
- 作用: 用于当前系统中 Order Items 模块的接口，通过 `GET /orders/{order_id}/items/{order_item_id}` 获取 orders 下 items 详情，并支持条件筛选。
- 请求体:
  - 参数:
    - order_id (path, string, 必填) - ID de la commande
    - order_item_id (path, string, 必填) - ID de l'élément de commande
    - filter[under_stock_alert] (query, boolean, 可选) - Scope products under their stock alert threshold
  - Body:
    - 无
- 响应体:
  - 200: Succès | application/json -> object{id, order_id, origin_pos_type, pos_session_id, label, parent_id, product, sale_item, ...}
  - 401: Non autorisé
  - 404: Élément de commande non trouvé

### PATCH /orders/{order_id}/items/{order_item_id}
- 作用: 用于当前系统中 Order Items 模块的接口，通过 `PATCH /orders/{order_id}/items/{order_item_id}` 更新 orders 下 items 部分字段。
- 请求体:
  - 参数:
    - order_id (path, integer, int64, 必填) - Identifiant de la commande
    - order_item_id (path, integer, int64, 必填) - Identifiant de l'élément de commande
  - Body:
    - 必填: 是
    - 描述: Request body for updating an order item.
    - Content-Type: application/json -> object{order_item_data}
- 响应体:
  - 200: Élément de commande mis à jour avec succès | application/json -> object{id, order_id, origin_pos_type, pos_session_id, label, parent_id, product, sale_item, ...}
  - 401: Non autorisé
  - 404: Commande ou élément de commande non trouvé
  - 422: Données invalides

### POST /orders/{order_id}/items/{order_item_id}/deposit
- 作用: 用于当前系统中 Order Items 模块的接口，通过 `POST /orders/{order_id}/items/{order_item_id}/deposit` 在 orders 下创建 deposit。
- 请求体:
  - 参数:
    - order_id (path, integer, int64, 必填) - Identifiant de la commande
    - order_item_id (path, integer, int64, 必填) - Identifiant de l'élément de commande
  - Body:
    - Content-Type: application/json -> object{deposit_product_id, type, amount}
- 响应体:
  - 200: Dépôt ajouté à l'élément de commande avec succès | application/json -> object{dev_in_progress}
  - 401: Non autorisé
  - 404: Commande ou élément de commande non trouvé
  - 422: Données invalides

## Orders
> Operations related to Orders

### GET /orders
- 作用: 用于当前系统中 Orders 模块的接口，通过 `GET /orders` 获取 orders 列表，并支持条件筛选、支持分页、支持排序。
- 请求体:
  - 参数:
    - filter[barcode] (query, string, 可选) - filter on barcode
    - filter[number] (query, string, 可选) - filter on number
    - filter[venture_id] (query, string, 可选) - Filter orders with venture_id
    - filter[created_by] (query, string, 可选) - Filter orders with user_id
    - filter[client_id] (query, string, 可选) - Filter orders with client_id
    - filter[pos_session_id] (query, string, 可选) - Filter orders with exact pos_session_id
    - filter[pos_id] (query, string, 可选) - Scopes orders with exact pos_id
    - filter[sale_id] (query, number, integer, 可选) - Scopes orders with exact sale_id
    - filter[origin_pos_type] (query, ref: PosType, 可选) - Filter orders with exact origin_po_type
    - filter[status] (query, ref: OrderStatus, 可选) - Filter orders with exact status
    - filter[type] (query, ref: OrderType, 可选) - Filter orders with exact type
    - filter[created_between] (query, string, 可选) - Filter orders created between two dates.
    - filter[expired_between] (query, string, 可选) - Filter orders expired between two dates.
    - filter[booked_between] (query, string, 可选) - Scopes orders with bookings between two dates.
    - filter[expired_after] (query, string, 可选) - Filter orders expired before a given date.
    - filter[show_in_pos] (query, boolean, 可选) - Filter orders with exact show_in_pos.
    - filter[unlocked] (query, boolean, 可选) - Scopes only unlocked orders.
    - filter[has_internal_note] (query, boolean, 可选) - Scopes only orders with an internal note.
    - filter[ids] (query, boolean, 可选) - Scopes only orders matching a list of ids.
    - filter[client_type] (query, ref: ClientType, 可选) - Scopes only orders with matching client_type.
    - filter[custom_tagged] (query, string, 可选) - Scopes only orders with matching custom tag.
    - filter[total_between] (query, string, 可选) - Scopes only orders with total amount between two integers.
    - filter[with_cancelled] (query, boolean, 可选) - If false, scopes orders which are not cancelled. Otherwise, cancelled ones will be INCLUDED, with others, in the results.
    - all (query, boolean, 可选) - Return all results without pagination
    - limit (query, integer, 可选) - Maximum number of results when using all=true (default: 500)
    - include (query, string, 可选) - Sub-resources to include
    - sort (query, string, 可选) - Sort results on a given attribute.
  - Body:
    - 无
- 响应体:
  - 200: Liste des commandes | application/json -> object{data, links, meta}
  - 401: Not allowed

### POST /orders
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders` 创建 orders。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{pos_id, client_id, type, lang, show_in_pos}
- 响应体:
  - 201: Commande créée avec succès
  - 401: Non autorisé
  - 422: Données invalides

### POST /orders/pricing
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders/pricing` 在 orders 下创建 pricing。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{product_id, qty, order_item_id, label, user_label, duration, user_unit_price, discount, ...}
- 响应体:
  - 200: Prix calculé avec succès | application/json -> object{total}
  - 422: Données invalides

### GET /orders/{order_id}
- 作用: 用于当前系统中 Orders 模块的接口，通过 `GET /orders/{order_id}` 获取 orders 详情。
- 请求体:
  - 参数:
    - order_id (path, string, 必填) - The targeted Order.
    - include (query, string, 可选) - relation to include
    - withVisitorCount (query, boolean, 可选) - Inclure le nombre de visiteurs
    - withBookings (query, boolean, 可选) - Inclure les réservations
    - withTransactions (query, boolean, 可选) - Inclure les transactions
  - Body:
    - 无
- 响应体:
  - 200: Succès | application/json -> object{id, venture_id, client_id, client, recipient_client_id, recipient_client, origin_pos_type, pos_session, ...}
  - 401: Non autorisé
  - 404: Commande non trouvée

### DELETE /orders/{order_id}
- 作用: 用于当前系统中 Orders 模块的接口，通过 `DELETE /orders/{order_id}` 删除 orders。
- 请求体:
  - 参数:
    - order_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success
  - 401: Not allowed

### PATCH /orders/{order_id}
- 作用: 用于当前系统中 Orders 模块的接口，通过 `PATCH /orders/{order_id}` 更新 orders 部分字段。
- 请求体:
  - 参数:
    - order_id (path, string, 必填) - The targeted Order.
  - Body:
    - Content-Type: application/json -> object{client_id, recipient_client_id, status, type, lang, show_in_pos, expired_at, front_note, ...}
- 响应体:
  - 200: Commande mise à jour avec succès
  - 401: Non autorisé
  - 404: Commande non trouvée
  - 422: Données invalides

### POST /orders/{order_id}/amend-quote
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders/{order_id}/amend-quote` 在 orders 下创建 amend-quote。
- 请求体:
  - 参数:
    - order_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{pos_session_id}
- 响应体:
  - 201: Success
  - 401: Not allowed

### POST /orders/{order_id}/automatic-deposit
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders/{order_id}/automatic-deposit` 在 orders 下创建 automatic-deposit。
- 请求体:
  - 参数:
    - order_id (path, integer, int64, 必填) - Identifiant de la commande
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Dépôt global ajouté avec succès | application/json -> array<ref: OrderItem>
  - 401: Non autorisé
  - 404: Commande non trouvée
  - 422: Données invalides

### PATCH /orders/{order_id}/cancel
- 作用: 用于当前系统中 Orders 模块的接口，通过 `PATCH /orders/{order_id}/cancel` 执行 cancel 操作。
- 请求体:
  - 参数:
    - order_id (path, string, 必填) - Identifiant de la commande
  - Body:
    - Content-Type: application/json -> object{order_status, no_sale_items}
- 响应体:
  - 204: Success
  - 401: Not allowed

### POST /orders/{order_id}/close
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders/{order_id}/close` 执行 close 操作。
- 请求体:
  - 参数:
    - order_id (path, integer, int64, 必填) - Identifiant de la commande
  - Body:
    - Content-Type: application/json -> object{zip, auto_access_scan}
- 响应体:
  - 200: Commande fermée avec succès | application/json -> object{sale, order}
  - 401: Non autorisé
  - 404: Commande non trouvée
  - 422: Données invalides

### POST /orders/{order_id}/duplicate-quote
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders/{order_id}/duplicate-quote` 在 orders 下创建 duplicate-quote。
- 请求体:
  - 参数:
    - order_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{pos_session_id}
- 响应体:
  - 201: Success
  - 401: Not allowed

### POST /orders/{order_id}/global-deposit
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders/{order_id}/global-deposit` 在 orders 下创建 global-deposit。
- 请求体:
  - 参数:
    - order_id (path, integer, int64, 必填) - Identifiant de la commande
  - Body:
    - Content-Type: application/json -> object{deposit_product_id, type, amount}
- 响应体:
  - 200: Dépôt global ajouté avec succès | application/json -> array<ref: OrderItem>
  - 401: Non autorisé
  - 404: Commande non trouvée
  - 422: Données invalides

### POST /orders/{order_id}/print-deliverables
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders/{order_id}/print-deliverables` 在 orders 下创建 print-deliverables。
- 请求体:
  - 参数:
    - order_id (path, string, 必填) - The order_id of the Order we wish to print delivrables for.
  - Body:
    - Content-Type: application/json -> object{printer_id, selected_for_print}
- 响应体:
  - 200: Success
  - 401: Not allowed

### POST /orders/{order_id}/print-recap
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders/{order_id}/print-recap` 在 orders 下创建 print-recap。
- 请求体:
  - 参数:
    - order_id (path, string, 必填) - The order_id of the Order we wish to get a recap for.
  - Body:
    - Content-Type: application/json -> object{printer_id}
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /orders/{order_id}/recap
- 作用: 用于当前系统中 Orders 模块的接口，通过 `GET /orders/{order_id}/recap` 获取 orders 下 recap 列表。
- 请求体:
  - 参数:
    - order_id (path, string, 必填) - The order_id of the Order we wish to get a recap for.
    - format (query, string, 可选) - The print format of the recap.
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success | application/json -> object{formatted_receipt}
  - 401: Not allowed

### POST /orders/{order_id}/send-mails
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders/{order_id}/send-mails` 执行发送操作。
- 请求体:
  - 参数:
    - order_id (path, string, 必填) - The order_id of the Order we wish to send delivrables for.
  - Body:
    - Content-Type: application/json -> object{dry_run, template, recipients_mails, subject, message, selected_for_sending}
- 响应体:
  - 200: Success
  - 401: Not allowed

### POST /orders/{order_id}/send-quote
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders/{order_id}/send-quote` 执行发送操作。
- 请求体:
  - 参数:
    - order_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 201: Success
  - 401: Not allowed

### POST /orders/{order_id}/transactions
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders/{order_id}/transactions` 在 orders 下创建 transactions。
- 请求体:
  - 参数:
    - order_id (path, integer, int64, 必填) - Identifiant de la commande
  - Body:
    - Content-Type: application/json -> object{paymode_id, pos_id, amount, cashback}
- 响应体:
  - 200: Transaction ajoutée avec succès | application/json -> array<ref: Transaction>
  - 401: Non autorisé
  - 404: Commande non trouvée
  - 422: Données invalides

### DELETE /orders/{order_id}/transactions/{transaction_id}
- 作用: 用于当前系统中 Orders 模块的接口，通过 `DELETE /orders/{order_id}/transactions/{transaction_id}` 删除 orders 下 transactions。
- 请求体:
  - 参数:
    - order_id (path, integer, int64, 必填) - Identifiant de la commande
    - transaction_id (path, integer, int64, 必填) - Identifiant de la transaction
  - Body:
    - 无
- 响应体:
  - 200: Transaction supprimée avec succès | application/json -> object{id, venture_id, client_id, client, recipient_client_id, recipient_client, origin_pos_type, pos_session, ...}
  - 401: Non autorisé
  - 404: Commande ou transaction non trouvée

### GET /orders/{order_id}/usable-gift-cards
- 作用: 用于当前系统中 Orders 模块的接口，通过 `GET /orders/{order_id}/usable-gift-cards` 获取 orders 下 usable-gift-cards 列表。
- 请求体:
  - 参数:
    - order_id (path, string, 必填) - Identifiant de la commande
    - owner_search (path, string, 可选) - Recherche du propriétaire
  - Body:
    - 无
- 响应体:
  - 200: Liste des cartes cadeaux utilisables récupérée avec succès | application/json -> array<ref: UsableGiftCardResource>
  - 401: Non autorisé
  - 404: Commande non trouvée

### POST /orders/{order_id}/use-gift-card/{token}
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders/{order_id}/use-gift-card/{token}` 在 orders 下对 use-gift-card 执行创建或业务操作。
- 请求体:
  - 参数:
    - order_id (path, string, 必填) - Identifiant de la commande
    - token (path, string, 必填) - Token de la carte cadeau
  - Body:
    - Content-Type: application/json -> object{pos_session_id, pos_id}
- 响应体:
  - 200: Carte cadeau utilisée avec succès | application/json -> object{success, dev_in_progess, token}
  - 401: Non autorisé
  - 404: Commande ou carte cadeau non trouvée
  - 422: Données invalides

### POST /orders/{order_id}/voucher
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST /orders/{order_id}/voucher` 在 orders 下创建 voucher。
- 请求体:
  - 参数:
    - order_id (path, string, 必填) - Identifiant de la commande
  - Body:
    - Content-Type: application/json -> object{token}
- 响应体:
  - 200: Voucher appliqué avec succès | application/json -> object{id, venture_id, client_id, client, recipient_client_id, recipient_client, origin_pos_type, pos_session, ...}
  - 401: Non autorisé
  - 404: Commande ou voucher non trouvé
  - 422: Données invalides

### DELETE /orders/{order_id}/voucher
- 作用: 用于当前系统中 Orders 模块的接口，通过 `DELETE /orders/{order_id}/voucher` 删除 orders 下 voucher。
- 请求体:
  - 参数:
    - order_id (path, string, 必填) - Identifiant de la commande
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Voucher appliqué avec succès | application/json -> object{id, venture_id, client_id, client, recipient_client_id, recipient_client, origin_pos_type, pos_session, ...}
  - 401: Non autorisé
  - 404: Commande non trouvé
  - 422: Données invalides

### POST orders/quotes/export
- 作用: 用于当前系统中 Orders 模块的接口，通过 `POST orders/quotes/export` 导出 orders 相关数据，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[barcode] (query, string, 可选) - filter on barcode
    - filter[number] (query, string, 可选) - filter on number
    - filter[venture_id] (query, string, 可选) - Filter orders with venture_id
    - filter[created_by] (query, string, 可选) - Filter orders with user_id
    - filter[client_id] (query, string, 可选) - Filter orders with client_id
    - filter[pos_session_id] (query, string, 可选) - Filter orders with exact pos_session_id
    - filter[pos_id] (query, string, 可选) - Scopes orders with exact pos_id
    - filter[origin_pos_type] (query, ref: PosType, 可选) - Filter orders with exact origin_po_type
    - filter[status] (query, ref: OrderStatus, 可选) - Filter orders with exact status
    - filter[type] (query, ref: OrderType, 可选) - Filter orders with exact type
    - filter[created_between] (query, string, 可选) - Filter orders created between two dates.
    - filter[expired_between] (query, string, 可选) - Filter orders expired between two dates.
    - filter[expired_after] (query, string, 可选) - Filter orders expired before a given date.
    - filter[show_in_pos] (query, boolean, 可选) - Filter orders with exact show_in_pos.
    - filter[unlocked] (query, boolean, 可选) - Scopes only unlocked orders.
    - filter[has_internal_note] (query, boolean, 可选) - Scopes only orders with an internal note.
    - filter[client_type] (query, ref: ClientType, 可选) - Scopes only orders with matching client_type.
    - filter[custom_tagged] (query, string, 可选) - Scopes only orders with matching custom tag.
    - filter[total_between] (query, string, 可选) - Scopes only orders with total amount between two integers.
    - filter[booked_between] (query, string, 可选) - Scopes orders with bookings between two dates.
    - sort (query, string, 可选) - Sort results on a given attribute.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/pdf -> object
  - 401: Not allowed

## Paylinks
> Operations related to Paylinks

### POST /orders/{order_id}/paylinks
- 作用: 用于当前系统中 Paylinks 模块的接口，通过 `POST /orders/{order_id}/paylinks` 在 orders 下创建 paylinks。
- 请求体:
  - 参数:
    - order_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 201: Success | application/json -> object{data}
  - 401: Not allowed

### DELETE /orders/{order_id}/paylinks/{paylink_id}
- 作用: 用于当前系统中 Paylinks 模块的接口，通过 `DELETE /orders/{order_id}/paylinks/{paylink_id}` 删除 orders 下 paylinks。
- 请求体:
  - 参数:
    - order_id (path, string, 必填)
    - paylink_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /paylinks
- 作用: 用于当前系统中 Paylinks 模块的接口，通过 `GET /paylinks` 获取 paylinks 列表。
- 请求体:
  - 参数:
    - include (query, string, 可选) - List of sub-resources to include
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 401: Not allowed
  - 404: No Match for query

### GET /paylinks/{paylink_id}
- 作用: 用于当前系统中 Paylinks 模块的接口，通过 `GET /paylinks/{paylink_id}` 获取 paylinks 详情。
- 请求体:
  - 参数:
    - paylink_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

## Paymodes
> Operations related to Paymodes

### GET /paymodes
- 作用: 用于当前系统中 Paymodes 模块的接口，通过 `GET /paymodes` 获取 paymodes 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[is_pos] (query, boolean, 可选) - Scope is pos
    - filter[venture_id] (query, string, 可选) - Filter orders with venture_id
    - filter[is_reserved] (query, boolean, 可选) - Filter on reserved payment modes
    - filter[ids] (query, string, 可选) - Scope paymodes matching given ids.
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### POST /paymodes
- 作用: 用于当前系统中 Paymodes 模块的接口，通过 `POST /paymodes` 创建 paymodes。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, label, is_active, is_online, is_pos, is_reserved, open_drawer, mode, ...}
- 响应体:
  - 200: Success

### GET /paymodes/{paymode_id}
- 作用: 用于当前系统中 Paymodes 模块的接口，通过 `GET /paymodes/{paymode_id}` 获取 paymodes 详情。
- 请求体:
  - 参数:
    - paymode_id (path, string, 必填)
    - withAttributes (query, string, 可选) - Expand paymode with attributes
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### DELETE /paymodes/{paymode_id}
- 作用: 用于当前系统中 Paymodes 模块的接口，通过 `DELETE /paymodes/{paymode_id}` 删除 paymodes。
- 请求体:
  - 参数:
    - paymode_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success

### PATCH /paymodes/{paymode_id}
- 作用: 用于当前系统中 Paymodes 模块的接口，通过 `PATCH /paymodes/{paymode_id}` 更新 paymodes 部分字段。
- 请求体:
  - 参数:
    - paymode_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, is_active, is_online, is_pos, is_reserved, open_drawer, account_code, sort_order}
- 响应体:
  - 200: Success

## Permissions
> Operations related to Permissions

### GET /permissions
- 作用: 用于当前系统中 Permissions 模块的接口，通过 `GET /permissions` 获取 permissions 列表。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 无
- 响应体:
  - 200: Success

### GET /permissions/business-sets
- 作用: 用于当前系统中 Permissions 模块的接口，通过 `GET /permissions/business-sets` 获取 permissions 下 business-sets 列表。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 无
- 响应体:
  - 200: Success

## Planner
> Operations related to Planner

### POST /planner/reassign-orphaned-bookings
- 作用: 用于当前系统中 Planner 模块的接口，通过 `POST /planner/reassign-orphaned-bookings` 执行 reassign-orphaned-bookings 操作。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 必填: 否
    - Content-Type: application/json -> object{booking_ids, start_date, end_date, location_id}
- 响应体:
  - 202: Job de réattribution dispatché avec succès | application/json -> object{message, job}
  - 422: Erreur de validation | application/json -> object{message, errors}

## Pos
> Operations related to Pos

### GET /pos
- 作用: 用于当前系统中 Pos 模块的接口，通过 `GET /pos` 获取 pos 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[venture_id] (query, string, 可选) - Filter by venture id
    - filter[label] (query, string, 可选) - Filter by partial label
    - filter[type] (query, ref: PosType, 可选) - Filter orders with exact type
    - include (query, string, 可选) - Sub-resources to include
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /pos/{pos_id}
- 作用: 用于当前系统中 Pos 模块的接口，通过 `GET /pos/{pos_id}` 获取 pos 详情。
- 请求体:
  - 参数:
    - pos_id (path, string, 必填)
    - withAttributes (query, string, 可选) - Expand pos with attributes
    - withCurrentSession (query, boolean, 可选) - Expand pos with cuurent pos session (if possible)
    - withFavorites (query, boolean, 可选) - Expand pos with favorites products
    - include (query, string, 可选) - Sub-resources to include
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### PATCH /pos/{pos_id}
- 作用: 用于当前系统中 Pos 模块的接口，通过 `PATCH /pos/{pos_id}` 更新 pos 部分字段。
- 请求体:
  - 参数:
    - pos_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, hardware_id, print_server, booking_gauge_enabled, printers, booking_gauge_params, booking_to_come_enabled, broadcast_printer_pos_id, ...}
- 响应体:
  - 200: Success

### POST /pos/{pos_id}/assign
- 作用: 用于当前系统中 Pos 模块的接口，通过 `POST /pos/{pos_id}/assign` 执行 assign 操作。
- 请求体:
  - 参数:
    - pos_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{hardware_id}
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /pos/{pos_id}/current-pos-session
- 作用: 用于当前系统中 Pos 模块的接口，通过 `GET /pos/{pos_id}/current-pos-session` 获取 pos 下 current-pos-session 列表。
- 请求体:
  - 参数:
    - pos_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /pos/{pos_id}/last-closed-pos-session
- 作用: 用于当前系统中 Pos 模块的接口，通过 `GET /pos/{pos_id}/last-closed-pos-session` 执行 last-closed-pos-session 操作。
- 请求体:
  - 参数:
    - pos_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 204: No closed session found
  - 401: Not allowed

### POST /pos/{pos_id}/pos-sessions
- 作用: 用于当前系统中 Pos 模块的接口，通过 `POST /pos/{pos_id}/pos-sessions` 在 pos 下创建 pos-sessions。
- 请求体:
  - 参数:
    - pos_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{total, cash, open_comment}
- 响应体:
  - 200: Success
  - 401: Not allowed

### POST /pos/{pos_id}/pos-sessions/close
- 作用: 用于当前系统中 Pos 模块的接口，通过 `POST /pos/{pos_id}/pos-sessions/close` 执行 close 操作。
- 请求体:
  - 参数:
    - pos_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{amount_counted}
- 响应体:
  - 200: Success
  - 401: Not allowed

## Pos Sessions
> Operations related to Pos Sessions

### GET /pos-sessions
- 作用: 用于当前系统中 Pos Sessions 模块的接口，通过 `GET /pos-sessions` 获取 pos-sessions 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[venture_id] (query, string, 可选) - Filters pos_sessions with an exact Venture_id.
    - filter[pos_id] (query, string, 可选) - Filters pos_sessions with an exact Pos_id.
    - filter[active] (query, boolean, 可选) - Filters active pos_sessions.
    - filter[closed] (query, boolean, 可选) - Filters closed pos_sessions.
    - filter[archived] (query, boolean, 可选) - Filters archived pos_sessions.
    - filter[amount_expected_between] (query, string, 可选) - Filters pos_sessions with an amount_expected between two values.
    - filter[amount_counted_between] (query, string, 可选) - Filters pos_sessions with an amount_counted between two values.
    - filter[amount_gap_between] (query, string, 可选) - Filters pos_sessions with an amount_gap between two values.
    - filter[opened_between] (query, string, 可选) - Filters pos_sessions opened between two dates.
    - filter[closed_between] (query, string, 可选) - Filters pos_sessions closed between two dates.
    - filter[created_between] (query, string, 可选) - Filters pos_sessions created between two dates.
    - filter[tolerated_gap] (query, boolean, 可选) - Filters pos_sessions with an amount_gap <= to their associated venture's cash_diff_tolerance. PosSessions associated to a venture without cash_diff_tolerance will be displayed.
    - include (query, string, 可选) - Sub-resources to include
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### POST /pos-sessions
- 作用: 用于当前系统中 Pos Sessions 模块的接口，通过 `POST /pos-sessions` 创建 pos-sessions。
- 请求体:
  - 参数:
    - pos_id (query, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /pos-sessions/{pos_session_id}
- 作用: 用于当前系统中 Pos Sessions 模块的接口，通过 `GET /pos-sessions/{pos_session_id}` 获取 pos-sessions 详情。
- 请求体:
  - 参数:
    - pos_session_id (path, string, 必填)
    - withPosClosedTransactions (query, boolean, 可选) - Expand orders with pos closed transactions
    - include (query, string, 可选) - Sub-resources to include
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### PATCH /pos-sessions/{pos_session_id}
- 作用: 用于当前系统中 Pos Sessions 模块的接口，通过 `PATCH /pos-sessions/{pos_session_id}` 更新 pos-sessions 部分字段。
- 请求体:
  - 参数:
    - pos_session_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### POST /pos-sessions/{pos_session_id}/close
- 作用: 用于当前系统中 Pos Sessions 模块的接口，通过 `POST /pos-sessions/{pos_session_id}/close` 执行 close 操作。
- 请求体:
  - 参数:
    - pos_session_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### POST /pos-sessions/{pos_session_id}/refills
- 作用: 用于当前系统中 Pos Sessions 模块的接口，通过 `POST /pos-sessions/{pos_session_id}/refills` 在 pos-sessions 下创建 refills。
- 请求体:
  - 参数:
    - pos_session_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{total, action, cash, user_comment}
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /pos-sessions/{pos_session_id}/z-report
- 作用: 用于当前系统中 Pos Sessions 模块的接口，通过 `GET /pos-sessions/{pos_session_id}/z-report` 获取 pos-sessions 下 z-report 列表。
- 请求体:
  - 参数:
    - pos_session_id (path, string, 必填) - POS Session ID
    - format (query, string, 可选) - Output format: pdf (default)
  - Body:
    - 无
- 响应体:
  - 200: Z Report file | application/pdf -> object
  - 401: Not allowed
  - 404: Session not found

## Printers
> Operations related to Printers

### GET /printers
- 作用: 用于当前系统中 Printers 模块的接口，通过 `GET /printers` 获取 printers 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[hardware_id] (query, string, 可选) - Filter printers with an exact hardware_id
    - filter[venture_id] (query, string, 可选) - Filter printers with a exact venture_id
    - filter[template_id] (query, integer, 可选) - Filter printers with a specific template_id
    - filter[is_online] (query, boolean, 可选) - Filter printers that are reported online
    - filter[is_shared] (query, boolean, 可选) - Filter printers that are shared
    - include (query, string, 可选) - Include related resources (e.g., template)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /printers
- 作用: 用于当前系统中 Printers 模块的接口，通过 `POST /printers` 创建 printers。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, template_id, label, hardware_id, qz_name, uri, pdl, width, ...}
- 响应体:
  - 201: Success

### GET /printers/{printer_id}
- 作用: 用于当前系统中 Printers 模块的接口，通过 `GET /printers/{printer_id}` 获取 printers 详情。
- 请求体:
  - 参数:
    - printer_id (path, integer, 必填)
    - include (query, string, 可选) - Include related resources (e.g., template)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 404: Not found

### DELETE /printers/{printer_id}
- 作用: 用于当前系统中 Printers 模块的接口，通过 `DELETE /printers/{printer_id}` 删除 printers。
- 请求体:
  - 参数:
    - printer_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success

### PATCH /printers/{printer_id}
- 作用: 用于当前系统中 Printers 模块的接口，通过 `PATCH /printers/{printer_id}` 更新 printers 部分字段。
- 请求体:
  - 参数:
    - printer_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{template_id, label, hardware_id, pdl, width, qz_name, uri, is_shared, ...}
- 响应体:
  - 200: Success

### POST /printers/{printer_id}/print
- 作用: 用于当前系统中 Printers 模块的接口，通过 `POST /printers/{printer_id}/print` 在 printers 下创建 print。
- 请求体:
  - 参数:
    - printer_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{venture_id, printer_id, hardware_id, format, payload}
- 响应体:
  - 201: Success

## Printing Templates
> Operations related to Printing Templates

### GET /printing-templates
- 作用: 用于当前系统中 Printing Templates 模块的接口，通过 `GET /printing-templates` 获取 printing-templates 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[venture_id] (query, string, 可选) - The UUID of the venture
    - filter[type] (query, ref: PrintingTemplateType, 可选) - The printing template type to filter on
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: Printing Template not found

### POST /printing-templates
- 作用: 用于当前系统中 Printing Templates 模块的接口，通过 `POST /printing-templates` 创建 printing-templates。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 无
- 响应体:
  - 201: Printing template created successfully | application/json -> object{id, venture_id, label, type, paper_width, paper_height, orientation, is_custom, ...}

### GET /printing-templates/{printing_template_id}
- 作用: 用于当前系统中 Printing Templates 模块的接口，通过 `GET /printing-templates/{printing_template_id}` 获取 printing-templates 详情。
- 请求体:
  - 参数:
    - printing_template_id (path, number, integer, 必填) - The id of the printing_template
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: Printing Template not found

### DELETE /printing-templates/{printing_template_id}
- 作用: 用于当前系统中 Printing Templates 模块的接口，通过 `DELETE /printing-templates/{printing_template_id}` 删除 printing-templates。
- 请求体:
  - 参数:
    - printing_template_id (path, integer, 必填) - The id of the printing template
  - Body:
    - 无
- 响应体:
  - 204: Printing template deleted successfully
  - 403: Forbidden - System templates cannot be deleted
  - 404: Printing Template not found

### PATCH /printing-templates/{printing_template_id}
- 作用: 用于当前系统中 Printing Templates 模块的接口，通过 `PATCH /printing-templates/{printing_template_id}` 更新 printing-templates 部分字段。
- 请求体:
  - 参数:
    - printing_template_id (path, integer, 必填) - The id of the printing template
  - Body:
    - Content-Type: application/json -> object{label, paper_width, paper_height, orientation, custom_markup}
- 响应体:
  - 200: Printing template updated successfully | application/json -> object{id, venture_id, label, type, paper_width, paper_height, orientation, is_custom, ...}
  - 404: Printing Template not found
  - 422: Validation error

### POST /printing-templates/{printing_template_id}/duplicate
- 作用: 用于当前系统中 Printing Templates 模块的接口，通过 `POST /printing-templates/{printing_template_id}/duplicate` 在 printing-templates 下创建 duplicate。
- 请求体:
  - 参数:
    - printing_template_id (path, integer, 必填) - The id of the printing template to duplicate
  - Body:
    - 无
- 响应体:
  - 201: Printing template duplicated successfully | application/json -> object{id, venture_id, label, type, paper_width, paper_height, orientation, is_custom, ...}
  - 404: Printing Template not found

### POST /printing-templates/{printing_template_id}/media
- 作用: 用于当前系统中 Printing Templates 模块的接口，通过 `POST /printing-templates/{printing_template_id}/media` 上传或更新 printing-templates 的媒体资源。
- 请求体:
  - 参数:
    - printing_template_id (path, integer, 必填) - The id of the printing template
  - Body:
    - Content-Type: multipart/form-data -> object{background_image}
- 响应体:
  - 200: Background image uploaded successfully | application/json -> object{id, venture_id, label, type, paper_width, paper_height, orientation, is_custom, ...}
  - 403: Forbidden - Background images can only be uploaded for PDF templates
  - 404: Printing Template not found
  - 422: Validation error

### DELETE /printing-templates/{printing_template_id}/media
- 作用: 用于当前系统中 Printing Templates 模块的接口，通过 `DELETE /printing-templates/{printing_template_id}/media` 上传或更新 printing-templates 的媒体资源。
- 请求体:
  - 参数:
    - printing_template_id (path, integer, 必填) - The id of the printing template
  - Body:
    - 无
- 响应体:
  - 200: Background image deleted successfully | application/json -> object{id, venture_id, label, type, paper_width, paper_height, orientation, is_custom, ...}
  - 404: Printing Template not found

### GET /printing-templates/{printing_template_id}/preview
- 作用: 用于当前系统中 Printing Templates 模块的接口，通过 `GET /printing-templates/{printing_template_id}/preview` 执行复核操作。
- 请求体:
  - 参数:
    - printing_template_id (path, integer, 必填) - The id of the printing template to preview
  - Body:
    - 无
- 响应体:
  - 200: HTML preview of the template | text/html -> string
  - 404: Printing Template not found

## Products
> Operations related to Products

### GET /products
- 作用: 用于当前系统中 Products 模块的接口，通过 `GET /products` 获取 products 列表，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter products with a partial label.
    - filter[type] (query, ref: ProductType, 可选) - Filter products with an EXACT type
    - filter[sku] (query, string, 可选) - Filter products with an EXACT sku
    - filter[is_reserved] (query, boolean, 可选) - Filter products that are reserved
    - filter[ids] (query, string, 可选) - Scope products matching given ids.
    - filter[is_sold_on_web] (query, boolean, 可选) - Filter products that should appear in the online shop
    - filter[is_sold_on_back] (query, boolean, 可选) - Filter products that should appear in the planning
    - filter[is_sold_on_pos] (query, boolean, 可选) - Filter products that should appear in the pos
    - filter[show_in_kiosk] (query, boolean, 可选) - Filter products that should appear in the kiosk
    - filter[venture_id] (query, string, 可选) - Filter products with an EXACT venture ID
    - filter[supplier_id] (query, string, 可选) - Filter products with an EXACT supplier ID
    - filter[category_id] (query, string, 可选) - Filter products with an EXACT category ID
    - filter[subcategory_id] (query, string, 可选) - Filter products with an EXACT subcategory ID
    - filter[is_bookable] (query, boolean, 可选) - Scope products that are bookable
    - filter[credentiable] (query, boolean, 可选) - Scope products that are attachable to a AccessRuleTemplate
    - filter[tag_id] (query, string, 可选) - Filter products with an EXACT tag ID
    - filter[parent_id] (query, string, 可选) - Filter products with an EXACT parent ID
    - filter[stockable] (query, boolean, 可选) - Scope products with stock management
    - filter[under_stock_alert] (query, boolean, 可选) - Scope products under their stock alert threshold
    - filter[in_locotation] (query, string, 可选) - Scope products that are attached to a given location
    - filter[created_at] (query, string, date-time, 可选) - Filter products with a partial creation datetime
    - filter[created_between] (query, string, 可选) - Filter products created between two datetimes
    - filter[updated_at] (query, string, date-time, 可选) - Filter products with a partial update datetime
    - filter[archived] (query, string, 可选) - Filter by archive status: all, only, exclude (default)
    - include (query, string, 可选) - List of sub-resources to include
    - sort (query, string, 可选) - Key to sort by : label, category_label, subcategory_label, created_at, updated_at, sort_order, count_in_stock...
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /products
- 作用: 用于当前系统中 Products 模块的接口，通过 `POST /products` 创建 products。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, subcategory_id, type, label, supplier_id, vat}
- 响应体:
  - 201: Success | application/json -> object{data}
  - 401: Not allowed

### POST /products/export
- 作用: 用于当前系统中 Products 模块的接口，通过 `POST /products/export` 导出 products 相关数据，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter products with a partial label.
    - filter[type] (query, ref: ProductType, 可选) - Filter products with an EXACT type
    - filter[sku] (query, string, 可选) - Filter products with an EXACT sku
    - filter[is_reserved] (query, boolean, 可选) - Filter products that are reserved
    - filter[is_sold_on_web] (query, boolean, 可选) - Filter products that should appear in the online shop
    - filter[is_sold_on_back] (query, boolean, 可选) - Filter products that should appear in the planning
    - filter[is_sold_on_pos] (query, boolean, 可选) - Filter products that should appear in the pos
    - filter[show_in_kiosk] (query, boolean, 可选) - Filter products that should appear in the kiosk
    - filter[venture_id] (query, string, 必填) - Filter products with an EXACT venture ID
    - filter[supplier_id] (query, string, 可选) - Filter products with an EXACT supplier ID
    - filter[category_id] (query, string, 可选) - Filter products with an EXACT category ID
    - filter[subcategory_id] (query, string, 可选) - Filter products with an EXACT subcategory ID
    - filter[is_bookable] (query, boolean, 可选) - Scope products that are bookable
    - filter[tag_id] (query, string, 可选) - Filter products with an EXACT tag ID
    - filter[parent_id] (query, string, 可选) - Filter products with an EXACT parent ID
    - filter[stockable] (query, boolean, 可选) - Scope products with stock management
    - filter[under_stock_alert] (query, boolean, 可选) - Scope products under their stock alert threshold
    - filter[created_between] (query, string, 可选) - Filter products created between two datetimes
    - filter[created_at] (query, string, date-time, 可选) - Filter products with a partial creation datetime
    - filter[updated_at] (query, string, date-time, 可选) - Filter products with a partial update datetime
    - sort (query, string, 可选) - Key to sort by : label, category_label, subcategory_label, created_at, updated_at, sort_order, count_in_stock...
  - Body:
    - 无
- 响应体:
  - 200: Success | application/pdf -> object
  - 401: Not allowed

### GET /products/{product_id}
- 作用: 用于当前系统中 Products 模块的接口，通过 `GET /products/{product_id}` 获取 products 详情。
- 请求体:
  - 参数:
    - product_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### DELETE /products/{product_id}
- 作用: 用于当前系统中 Products 模块的接口，通过 `DELETE /products/{product_id}` 删除 products。
- 请求体:
  - 参数:
    - product_id (path, string, 必填) - Id of the product we wish to delete.
  - Body:
    - 无
- 响应体:
  - 204: Success | application/json -> object{id, external_id, venture_id, category_id, category_label, subcategory_id, subcategory_label, supplier_id, ...}
  - 401: Not allowed

### PATCH /products/{product_id}
- 作用: 用于当前系统中 Products 模块的接口，通过 `PATCH /products/{product_id}` 更新 products 部分字段。
- 请求体:
  - 参数:
    - product_id (path, string, 必填) - Id of the product we wish to update.
  - Body:
    - Content-Type: application/json -> object{subcategory_id, label, supplier_id, deposit_product_id, is_specific_vat, vat, description, long_description, ...}
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### POST /products/{product_id}/attached-files
- 作用: 用于当前系统中 Products 模块的接口，通过 `POST /products/{product_id}/attached-files` 在 products 下创建 attached-files。
- 请求体:
  - 参数:
    - product_id (path, string, 必填)
  - Body:
    - Content-Type: multipart/form-data -> object{attached_files_quote, attached_files_confirm, attached_files_proposal}
- 响应体:
  - 200: Success

### DELETE /products/{product_id}/attached-files/{media_id}
- 作用: 用于当前系统中 Products 模块的接口，通过 `DELETE /products/{product_id}/attached-files/{media_id}` 删除 products 下 attached-files。
- 请求体:
  - 参数:
    - product_id (path, string, 必填)
    - media_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success

### GET /products/{product_id}/check-delete
- 作用: 用于当前系统中 Products 模块的接口，通过 `GET /products/{product_id}/check-delete` 校验 products 并返回结果。
- 请求体:
  - 参数:
    - product_id (path, string, 必填) - Id of the product to check.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{is_safe, future_bookings_count, future_bookings, compound_usages_count, compound_usages}
  - 401: Not allowed
  - 404: Product not found

### GET /products/{product_id}/compound-choices
- 作用: 用于当前系统中 Products 模块的接口，通过 `GET /products/{product_id}/compound-choices` 获取 products 下 compound-choices 列表。
- 请求体:
  - 参数:
    - product_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### POST /products/{product_id}/inventory
- 作用: 用于当前系统中 Products 模块的接口，通过 `POST /products/{product_id}/inventory` 在 products 下创建 inventory。
- 请求体:
  - 参数:
    - product_id (path, string, 必填)
    - inventory_id (query, string, 可选)
  - Body:
    - Content-Type: application/json -> object{inventory_id, comment}
- 响应体:
  - 201: Success | application/json -> object{data}
  - 401: Not allowed

### POST /products/{product_id}/media
- 作用: 用于当前系统中 Products 模块的接口，通过 `POST /products/{product_id}/media` 上传或更新 products 的媒体资源。
- 请求体:
  - 参数:
    - product_id (path, string, 必填)
  - Body:
    - Content-Type: multipart/form-data -> object{image, background}
- 响应体:
  - 200: Success

### GET /products/{product_id}/pack-steps/{step}
- 作用: 用于当前系统中 Products 模块的接口，通过 `GET /products/{product_id}/pack-steps/{step}` 获取 products 下 pack-steps 详情。
- 请求体:
  - 参数:
    - product_id (path, string, 必填)
    - step (path, string, 必填)
    - include (query, string, 可选)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### POST /products/{product_id}/stock-order
- 作用: 用于当前系统中 Products 模块的接口，通过 `POST /products/{product_id}/stock-order` 在 products 下创建 stock-order。
- 请求体:
  - 参数:
    - product_id (path, string, 必填)
    - stock_order_id (query, string, 可选)
  - Body:
    - Content-Type: application/json -> object{stock_order_id}
- 响应体:
  - 201: Success | application/json -> object{data}
  - 401: Not allowed

### GET /products/{product_id}/stocks
- 作用: 用于当前系统中 Products 模块的接口，通过 `GET /products/{product_id}/stocks` 获取 products 下 stocks 列表，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - product_id (path, string, 必填)
    - filter[operation] (query, string, 可选)
    - sort (query, string, 可选)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

## Qweeklers
> Operations related to Qweeklers

### GET /qweeklers
- 作用: 用于当前系统中 Qweeklers 模块的接口，通过 `GET /qweeklers` 获取 qweeklers 列表。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

## Roles
> Operations related to Roles

### GET /roles
- 作用: 用于当前系统中 Roles 模块的接口，通过 `GET /roles` 获取 roles 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[name] (query, string, 可选)
    - filter[venture_id] (query, string, 可选)
    - include (query, string, 可选)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /roles
- 作用: 用于当前系统中 Roles 模块的接口，通过 `POST /roles` 创建 roles。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, name}
- 响应体:
  - 201: Success

### GET /roles/{role_id}
- 作用: 用于当前系统中 Roles 模块的接口，通过 `GET /roles/{role_id}` 获取 roles 详情。
- 请求体:
  - 参数:
    - role_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### DELETE /roles/{role_id}
- 作用: 用于当前系统中 Roles 模块的接口，通过 `DELETE /roles/{role_id}` 删除 roles。
- 请求体:
  - 参数:
    - role_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 201: Success

### PATCH /roles/{role_id}
- 作用: 用于当前系统中 Roles 模块的接口，通过 `PATCH /roles/{role_id}` 更新 roles 部分字段。
- 请求体:
  - 参数:
    - role_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{name, permissions}
- 响应体:
  - 201: Success

## SaleItems
> Operations related to SaleItems

### GET /sale-items
- 作用: 用于当前系统中 SaleItems 模块的接口，通过 `GET /sale-items` 获取 sale-items 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[venture_id] (query, string, 可选) - Filters sale_items with and EXACT venture_id
    - filter[label] (query, string, 可选) - Filters sale_items with and Partial label
    - filter[pos_id] (query, string, 可选) - Scope sale_items with and EXACT pos_id
    - filter[sale_number] (query, string, 可选) - Scope sale_items with and EXACT sale number
    - filter[sale_id] (query, string, 可选) - Filters sale_items with and EXACT sale_id
    - filter[product_id] (query, string, 可选) - Filters sale_items with and EXACT product_id
    - filter[client_id] (query, string, 可选) - Scope sale_items belonging to a specific client_id
    - filter[ticket_number] (query, string, 可选) - Scope sale_items matching a specific ticket_number
    - filter[type] (query, string, 可选) - Filters sale_items with and EXACT type
    - filter[without_deposit_products] (query, boolean, 可选) - Scope sale_items which have no deposit products
    - filter[created_after] (query, string, 可选) - Scope sale_items created after a given datetime
    - filter[created_before] (query, string, 可选) - Scope sale_items created before a given datetime
    - filter[updated_after] (query, string, 可选) - Scope sale_items updated after a given datetime
    - filter[updated_before] (query, string, 可选) - Scope sale_items updated before a given datetime
    - filter[created_between] (query, string, 可选) - Scope sale_items created between two dates.
    - include (query, string, 可选) - Sub-resources to include
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 401: Not allowed

## Sales
> Operations related to Sales

### GET /sales
- 作用: 用于当前系统中 Sales 模块的接口，通过 `GET /sales` 获取 sales 列表，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[number] (query, string, 可选) - Filter sales with number
    - filter[client_id] (query, string, 可选) - Filter sales with client_id
    - filter[client_search] (query, string, 可选) - Filter sales with a partial client email/name/society.
    - filter[venture_id] (query, string, 可选) - Filter sales with venture_id
    - filter[pos_session_id] (query, string, 可选) - Filter sales with pos_session_id
    - filter[pos_id] (query, string, 可选) - Scope sales with pos_id
    - filter[order_id] (query, string, 可选) - Filter sales with order_id
    - filter[origin_id] (query, string, 可选) - Filter sales with origin_id
    - filter[created_by] (query, string, 可选) - Filter sales with creator user_id
    - filter[created_after] (query, string, 可选) - Scope sales created after a given datetime
    - filter[created_before] (query, string, 可选) - Scope sales created before a given datetime
    - filter[updated_after] (query, string, 可选) - Scope sales updated after a given datetime
    - filter[updated_before] (query, string, 可选) - Scope sales updated before a given datetime
    - filter[created_between] (query, string, 可选) - Scope sales created between two dates.
    - filter[amount_between] (query, string, 可选) - Scope sales with total amount between two integers.
    - filter[amount_greater_than] (query, string, 可选) - Scope sales with total amount stricly above an integer.
    - filter[amount_lower_than] (query, string, 可选) - Scope sales with total amount stricly below an integer.
    - filter[deferred_amount_between] (query, string, 可选) - Scope sales with total deferred between two integers.
    - filter[deferred_amount_greater_than] (query, string, 可选) - Scope sales with deferred amount stricly above an integer.
    - filter[deferred_amount_lower_than] (query, string, 可选) - Scope sales with total deferred amount stricly below an integer.
    - filter[expired_between] (query, string, 可选) - Scope sales with invoice expiring between two dates.
    - filter[deferred] (query, boolean, 可选) - Scope sales with deferred transactions.
    - filter[paid] (query, boolean, 可选) - Scope deferred sales on their payment status.
    - filter[expired] (query, boolean, 可选) - Scope sales on the expiration status of their attached invoices.
    - filter[modifiedBySource] (query, string, 可选) - Scope sales wich were cancelled by their ascendant sale (use filter value 'cancellation'), or modified (use filter value 'modification'), or reimbursed (use filter value 'reimbursement'), or a combination of them.
    - filter[invoice_number] (query, string, 可选) - Filter sales by their invoice number
    - filter[invoice_id] (query, string, 可选) - Filter sales by their invoice ID
    - sort (query, string, 可选) - sorts results by a given field
    - include (query, string, 可选) - Sub-resources to include
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 401: Not allowed

### POST /sales/journal
- 作用: 用于当前系统中 Sales 模块的接口，通过 `POST /sales/journal` 在 sales 下创建 journal。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 必填: 是
    - Content-Type: application/json -> object{pos_session_id, venture_id, from, to}
- 响应体:
  - 200: Journal created successfully | application/json -> object{message}
  - 400: Bad request
  - 401: Unauthorized

### GET /sales/metrics
- 作用: 用于当前系统中 Sales 模块的接口，通过 `GET /sales/metrics` 获取 sales 下 metrics 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[number] (query, string, 可选) - Filter sales with number
    - filter[client_id] (query, string, 可选) - Filter sales with client_id
    - filter[client_search] (query, string, 可选) - Filter sales with a partial client email/name/society.
    - filter[venture_id] (query, string, 可选) - Filter sales with venture_id
    - filter[pos_session_id] (query, string, 可选) - Filter sales with pos_session_id
    - filter[pos_id] (query, string, 可选) - Scope sales with pos_id
    - filter[order_id] (query, string, 可选) - Filter sales with order_id
    - filter[origin_id] (query, string, 可选) - Filter sales with origin_id
    - filter[created_by] (query, string, 可选) - Filter sales with creator user_id
    - filter[created_after] (query, string, 可选) - Scope sales created after a given datetime
    - filter[created_before] (query, string, 可选) - Scope sales created before a given datetime
    - filter[updated_after] (query, string, 可选) - Scope sales updated after a given datetime
    - filter[updated_before] (query, string, 可选) - Scope sales updated before a given datetime
    - filter[created_between] (query, string, 可选) - Scope sales created between two dates.
    - filter[amount_between] (query, string, 可选) - Scope sales with total amount between two integers.
    - filter[amount_greater_than] (query, string, 可选) - Scope sales with total amount stricly above an integer.
    - filter[amount_lower_than] (query, string, 可选) - Scope sales with total amount stricly below an integer.
    - filter[deferred_amount_between] (query, string, 可选) - Scope sales with total deferred between two integers.
    - filter[deferred_amount_greater_than] (query, string, 可选) - Scope sales with deferred amount stricly above an integer.
    - filter[deferred_amount_lower_than] (query, string, 可选) - Scope sales with total deferred amount stricly below an integer.
    - filter[expired_between] (query, string, 可选) - Scope sales with invoice expiring between two dates.
    - filter[deferred] (query, boolean, 可选) - Scope sales with deferred transactions.
    - filter[paid] (query, boolean, 可选) - Scope deferred sales on their payment status.
    - filter[expired] (query, boolean, 可选) - Scope sales on the expiration status of their attached invoices.
    - filter[modifiedBySource] (query, string, 可选) - Scope sales wich were cancelled by their ascendant sale (use filter value 'cancellation'), or modified (use filter value 'modification'), or reimbursed (use filter value 'reimbursement'), or a combination of them.
  - Body:
    - 无
- 响应体:
  - 200: Success

### GET /sales/{sale_id}
- 作用: 用于当前系统中 Sales 模块的接口，通过 `GET /sales/{sale_id}` 获取 sales 详情。
- 请求体:
  - 参数:
    - sale_id (path, string, 必填) - The sale_id of the Sale we wish to get.
    - include (query, string, 可选) - Relations to include
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### DELETE /sales/{sale_id}
- 作用: 用于当前系统中 Sales 模块的接口，通过 `DELETE /sales/{sale_id}` 删除 sales。
- 请求体:
  - 参数:
    - sale_id (path, string, 必填) - The sale_id of the Sale we wish to cancel.
  - Body:
    - Content-Type: application/json -> object{paymode_id, pos_session_id, origin}
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### POST /sales/{sale_id}/invoice
- 作用: 用于当前系统中 Sales 模块的接口，通过 `POST /sales/{sale_id}/invoice` 在 sales 下创建 invoice。
- 请求体:
  - 参数:
    - sale_id (path, string, 必填) - The sale_id of the Sale we wish to create an invoice for.
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### POST /sales/{sale_id}/invoice/send
- 作用: 用于当前系统中 Sales 模块的接口，通过 `POST /sales/{sale_id}/invoice/send` 执行发送操作。
- 请求体:
  - 参数:
    - sale_id (path, string, 必填) - The sale_id of the Sale we wish to send an invoice for.
  - Body:
    - Content-Type: application/json -> object{client_id}
- 响应体:
  - 202: Accepted | application/json -> object{data}
  - 401: Not allowed

### POST /sales/{sale_id}/modification-preview
- 作用: 用于当前系统中 Sales 模块的接口，通过 `POST /sales/{sale_id}/modification-preview` 执行复核操作。
- 请求体:
  - 参数:
    - sale_id (path, string, 必填) - The sale_id of the Sale we wish to preview modification for.
  - Body:
    - Content-Type: application/json -> object{modification_type}
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### POST /sales/{sale_id}/print-receipt
- 作用: 用于当前系统中 Sales 模块的接口，通过 `POST /sales/{sale_id}/print-receipt` 在 sales 下创建 print-receipt。
- 请求体:
  - 参数:
    - sale_id (path, string, 必填) - The sale_id of the Sale we wish to get a receipt for.
  - Body:
    - Content-Type: application/json -> object{printer_id}
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /sales/{sale_id}/receipt
- 作用: 用于当前系统中 Sales 模块的接口，通过 `GET /sales/{sale_id}/receipt` 获取 sales 下 receipt 列表。
- 请求体:
  - 参数:
    - sale_id (path, string, 必填) - The sale_id of the Sale we wish to get a receipt for.
    - format (query, string, 可选) - The print format of the receipt.
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success | application/json -> object{formatted_receipt}
  - 401: Not allowed

### POST /sales/{sale_id}/recover-deferred
- 作用: 用于当前系统中 Sales 模块的接口，通过 `POST /sales/{sale_id}/recover-deferred` 在 sales 下创建 recover-deferred。
- 请求体:
  - 参数:
    - sale_id (path, string, 必填) - The sale_id of the Sale we wish to send an invoice for.
  - Body:
    - Content-Type: application/json -> object{amount, paymode_id, pos_id, comment, deferred_recovered_at}
- 响应体:
  - 202: Accepted | application/json -> object{data}
  - 401: Not allowed

### POST /sales/{sale_id}/temporary
- 作用: 用于当前系统中 Sales 模块的接口，通过 `POST /sales/{sale_id}/temporary` 在 sales 下创建 temporary。
- 请求体:
  - 参数:
    - sale_id (path, string, 必填) - The sale_id of the Sale we wish to modify.
  - Body:
    - Content-Type: application/json -> object{modification_type}
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

## Search
> Operations related to Search

### POST /scan
- 作用: 用于当前系统中 Search 模块的接口，通过 `POST /scan` 校验 scan 并返回结果。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, code, type}
- 响应体:
  - 200: Matching resources grouped by type. Empty if not found, per type.

### GET /search
- 作用: 用于当前系统中 Search 模块的接口，通过 `GET /search` 搜索 search 数据。
- 请求体:
  - 参数:
    - keyword (query, string, 必填) - Your full-text search.
    - model_types (query, array, 必填) - The types of model to use full-text search on.
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 404: No Match for query

## Segments
> Operations related to Segments

### GET /segments
- 作用: 用于当前系统中 Segments 模块的接口，通过 `GET /segments` 获取 segments 列表，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter segments with a partial label.
    - sort (query, string, 可选) - Sort results on a given attribute.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /segments
- 作用: 用于当前系统中 Segments 模块的接口，通过 `POST /segments` 创建 segments。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{label, filters, is_with_clients, is_with_subs}
- 响应体:
  - 200: Success

### GET /segments/{segment_id}
- 作用: 用于当前系统中 Segments 模块的接口，通过 `GET /segments/{segment_id}` 获取 segments 详情。
- 请求体:
  - 参数:
    - segment_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success

### DELETE /segments/{segment_id}
- 作用: 用于当前系统中 Segments 模块的接口，通过 `DELETE /segments/{segment_id}` 删除 segments。
- 请求体:
  - 参数:
    - segment_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success

### PATCH /segments/{segment_id}
- 作用: 用于当前系统中 Segments 模块的接口，通过 `PATCH /segments/{segment_id}` 更新 segments 部分字段。
- 请求体:
  - 参数:
    - segment_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, filters, is_with_clients, is_with_subs}
- 响应体:
  - 200: Success

### POST /segments/{segment_id}/count
- 作用: 用于当前系统中 Segments 模块的接口，通过 `POST /segments/{segment_id}/count` 在 segments 下创建 count。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success | application/json -> object
  - 401: Not allowed

### POST /segments/{segment_id}/export
- 作用: 用于当前系统中 Segments 模块的接口，通过 `POST /segments/{segment_id}/export` 导出 segments 相关数据。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success | application/json -> object
  - 401: Not allowed

## Shifts
> Operations related to Shifts

### GET /shifts
- 作用: 用于当前系统中 Shifts 模块的接口，通过 `GET /shifts` 获取 shifts 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[venture_id] (query, string, 可选) - Filter shifts with an exact venture_id
    - filter[user_id] (query, string, 可选) - Filter shifts with an exact user_id
    - filter[start_at] (query, string, 可选) - Filter shifts with a partial start_at date
    - filter[end_at] (query, string, 可选) - Filter shifts with a partial end_at date
    - filter[ongoing] (query, boolean, 可选) - Scope shifts with a start_at date but no end_at date
    - filter[starts_between] (query, string, 可选) - Scope shifts which start between two datetimes
    - filter[ends_between] (query, string, 可选) - Scope shifts which end between two datetimes
    - filter[check_starts_between] (query, string, 可选) - Scope shifts which check start between two datetimes
    - filter[check_ends_between] (query, string, 可选) - Scope shifts which check end between two datetimes
    - filter[active_users] (query, boolean, 可选) - Scope shifts from active users only
    - filter[checked] (query, boolean, 可选) - Scope shifts which have been checked by a user
    - withUser (query, boolean, 可选) - Expand shifts which user data
    - withChecker (query, boolean, 可选) - Expand shifts which user data
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /shifts
- 作用: 用于当前系统中 Shifts 模块的接口，通过 `POST /shifts` 创建 shifts。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, user_id, start_at, end_at, comment}
- 响应体:
  - 201: Success

### DELETE /shifts/{shift_id}
- 作用: 用于当前系统中 Shifts 模块的接口，通过 `DELETE /shifts/{shift_id}` 删除 shifts。
- 请求体:
  - 参数:
    - shift_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success

### PATCH /shifts/{shift_id}
- 作用: 用于当前系统中 Shifts 模块的接口，通过 `PATCH /shifts/{shift_id}` 更新 shifts 部分字段。
- 请求体:
  - 参数:
    - shift_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{start_at, end_at, start_at_check, end_at_check, is_forced, comment}
- 响应体:
  - 200: Success

## Sort Order
> Operations related to Sort Order

### POST /sort-order
- 作用: 用于当前系统中 Sort Order 模块的接口，通过 `POST /sort-order` 创建 sort-order。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 必填: 是
    - Content-Type: application/json -> object{categories, products, tags}
- 响应体:
  - 200: Sort orders successfully updated | application/json -> object{data}
  - 422: Validation error

## StockOrderItems
> Operations related to StockOrderItems

### GET /stock-orders/{stock_order_id}/items
- 作用: 用于当前系统中 StockOrderItems 模块的接口，通过 `GET /stock-orders/{stock_order_id}/items` 获取 stock-orders 下 items 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - stock_order_id (path, string, 必填)
    - filter[product_sku] (query, string, 可选)
    - filter[venture_id] (query, string, 可选)
    - filter[supplier_id] (query, string, 可选)
    - filter[product.is_stock] (query, boolean, 可选)
    - filter[supplier_ref] (query, string, 可选)
    - filter[supplier_label] (query, string, 可选)
    - filter[product_label] (query, string, 可选)
    - filter[subcategory_label] (query, string, 可选)
    - filter[category_label] (query, string, 可选)
    - include (query, string, 可选) - List of sub-resources to include
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 401: Not allowed
  - 404: No Match for query

### DELETE /stock-orders/{stock_order_id}/items/{item_id}
- 作用: 用于当前系统中 StockOrderItems 模块的接口，通过 `DELETE /stock-orders/{stock_order_id}/items/{item_id}` 删除 stock-orders 下 items。
- 请求体:
  - 参数:
    - stock_order_id (path, string, 必填)
    - item_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success
  - 401: Not allowed

### PATCH /stock-orders/{stock_order_id}/items/{item_id}
- 作用: 用于当前系统中 StockOrderItems 模块的接口，通过 `PATCH /stock-orders/{stock_order_id}/items/{item_id}` 更新 stock-orders 下 items 部分字段。
- 请求体:
  - 参数:
    - stock_order_id (path, string, 必填)
    - item_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{stock_order_qty, stock_received_qty, stock_received_comment, stock_order_comment}
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

## StockOrders
> Operations related to StockOrders

### GET /stock-orders
- 作用: 用于当前系统中 StockOrders 模块的接口，通过 `GET /stock-orders` 获取 stock-orders 列表，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[number] (query, string, 可选) - filter on number (= reference)
    - filter[status] (query, string, 可选) - filter on status
    - filter[received_at_before] (query, string, 可选) - Filter on before the delivery date
    - filter[received_at_after] (query, string, 可选) - Filter on after the delivery date
    - filter[received_at_between] (query, string, 可选) - Filter orders delivered between two dates (format: YYYY-MM-DD,YYYY-MM-DD)
    - filter[venture_id] (query, string, 可选) - Filter orders with an exact venture id.
    - filter[created_at_between] (query, string, 可选) - Filter orders created between two dates (format: YYYY-MM-DD,YYYY-MM-DD)
    - filter[sent_at_between] (query, string, 可选) - Filter orders sent between two dates inclusively (format: YYYY-MM-DD,YYYY-MM-DD)
    - sort (query, string, 可选) - Sort results.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 401: Not allowed
  - 404: No Match for query

### POST /stock-orders
- 作用: 用于当前系统中 StockOrders 模块的接口，通过 `POST /stock-orders` 创建 stock-orders。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{number, venture_id, supplier_id}
- 响应体:
  - 201: Success | application/json -> object{data}
  - 401: Not allowed

### GET /stock-orders/{stock_order_id}
- 作用: 用于当前系统中 StockOrders 模块的接口，通过 `GET /stock-orders/{stock_order_id}` 获取 stock-orders 详情。
- 请求体:
  - 参数:
    - stock_order_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### DELETE /stock-orders/{stock_order_id}
- 作用: 用于当前系统中 StockOrders 模块的接口，通过 `DELETE /stock-orders/{stock_order_id}` 删除 stock-orders。
- 请求体:
  - 参数:
    - stock_order_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success
  - 401: Not allowed

### PATCH /stock-orders/{stock_order_id}
- 作用: 用于当前系统中 StockOrders 模块的接口，通过 `PATCH /stock-orders/{stock_order_id}` 更新 stock-orders 部分字段。
- 请求体:
  - 参数:
    - stock_order_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{venture_id, number, comment, supplier_receipt_ref}
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### POST /stock-orders/{stock_order_id}/receive
- 作用: 用于当前系统中 StockOrders 模块的接口，通过 `POST /stock-orders/{stock_order_id}/receive` 执行接收操作。
- 请求体:
  - 参数:
    - stock_order_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

### POST /stock-orders/{stock_order_id}/send
- 作用: 用于当前系统中 StockOrders 模块的接口，通过 `POST /stock-orders/{stock_order_id}/send` 执行发送操作。
- 请求体:
  - 参数:
    - stock_order_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success | application/json -> object{data}
  - 401: Not allowed

## Suppliers
> Operations related to Suppliers

### GET /suppliers
- 作用: 用于当前系统中 Suppliers 模块的接口，通过 `GET /suppliers` 获取 suppliers 列表，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter suppliers with a partial label.
    - filter[customer_number] (query, string, 可选) - Filter suppliers with a partial customer_number.
    - filter[contact_name] (query, string, 可选) - Filter suppliers with a partial contact_name.
    - filter[venture_id] (query, string, 可选) - Filter suppliers with an exact venture id.
    - sort (query, string, 可选) - Sort results.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /suppliers
- 作用: 用于当前系统中 Suppliers 模块的接口，通过 `POST /suppliers` 创建 suppliers。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{label}
- 响应体:
  - 200: Success

### GET /suppliers/{supplier_id}
- 作用: 用于当前系统中 Suppliers 模块的接口，通过 `GET /suppliers/{supplier_id}` 获取 suppliers 详情。
- 请求体:
  - 参数:
    - supplier_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### DELETE /suppliers/{supplier_id}
- 作用: 用于当前系统中 Suppliers 模块的接口，通过 `DELETE /suppliers/{supplier_id}` 删除 suppliers。
- 请求体:
  - 参数:
    - supplier_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: Success

### PATCH /suppliers/{supplier_id}
- 作用: 用于当前系统中 Suppliers 模块的接口，通过 `PATCH /suppliers/{supplier_id}` 更新 suppliers 部分字段。
- 请求体:
  - 参数:
    - supplier_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, address, num, box, zip, city, phone, email, ...}
- 响应体:
  - 200: Success

## Tags
> Operations related to Tags

### GET /tags
- 作用: 用于当前系统中 Tags 模块的接口，通过 `GET /tags` 获取 tags 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[label] (query, string, 可选) - Filter tags with a partial label.
    - filter[venture_id] (query, string, 可选) - Filter tags with an EXACT venture_id
    - withProducts (query, boolean, 可选) - Expand tags resource with products
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /tags
- 作用: 用于当前系统中 Tags 模块的接口，通过 `POST /tags` 创建 tags。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, label, description, sort_order, view_mode, image, show_in_kiosk, show_in_front}
- 响应体:
  - 201: Success | application/json -> object{id, venture_id, label, sort_order, description, show_in_front, show_in_kiosk, image, ...}
  - 401: Not allowed

### GET /tags/{tag_id}
- 作用: 用于当前系统中 Tags 模块的接口，通过 `GET /tags/{tag_id}` 获取 tags 详情。
- 请求体:
  - 参数:
    - tag_id (path, string, 必填) - Id of the tag we wish to review.
    - withProducts (query, boolean, 可选) - Expand tags resource with products
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{id, venture_id, label, sort_order, description, show_in_front, show_in_kiosk, image, ...}
  - 401: Not allowed

### DELETE /tags/{tag_id}
- 作用: 用于当前系统中 Tags 模块的接口，通过 `DELETE /tags/{tag_id}` 删除 tags。
- 请求体:
  - 参数:
    - tag_id (path, string, 必填) - Id of the tag we wish to delete.
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: No content | application/json -> object{id, venture_id, label, sort_order, description, show_in_front, show_in_kiosk, image, ...}
  - 401: Not allowed

### PATCH /tags/{tag_id}
- 作用: 用于当前系统中 Tags 模块的接口，通过 `PATCH /tags/{tag_id}` 更新 tags 部分字段。
- 请求体:
  - 参数:
    - tag_id (path, string, 必填) - Id of the tag we wish to update.
  - Body:
    - Content-Type: application/json -> object{label, description, sort_order, view_mode, image, show_in_kiosk, show_in_front, attributes}
- 响应体:
  - 200: Success | application/json -> object{id, venture_id, label, sort_order, description, show_in_front, show_in_kiosk, image, ...}
  - 401: Not allowed

## Tickets
> Operations related to Tickets

### GET /tickets
- 作用: 用于当前系统中 Tickets 模块的接口，通过 `GET /tickets` 获取 tickets 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[venture_id] (query, string, 可选) - Filter tickets with an EXACT venture ID
    - filter[sale_id] (query, string, 可选) - Filter tickets with an EXACT sale ID
    - filter[sale_item_id] (query, string, 可选) - Filter tickets with an EXACT sale item ID
    - filter[order_id] (query, string, 可选) - Scope tickets with an EXACT order ID
    - filter[created_at] (query, string, date-time, 可选) - Filter tickets with a partial creation datetime
    - filter[updated_at] (query, string, date-time, 可选) - Filter tickets with a partial update datetime
    - filter[client_buyer] (query, string, 可选) - Filter tickets by buyer client's name or society
    - filter[number] (query, string, 可选) - Filter tickets with a partial number
    - filter[sale_number] (query, string, 可选) - Scope tickets with an EXACT sale number
    - filter[used] (query, boolean, 可选) - Filter tickets by usage status
    - filter[sold_between] (query, string, 可选) - Filter tickets sold between two dates
    - filter[used_at] (query, string, date, 可选) - Filter tickets used on a specific date
    - filter[expired_at] (query, string, date-time, 可选) - Filter tickets expiring on a specific date
    - filter[show_cancelled] (query, boolean, 可选) - Include cancelled tickets
    - filter[product_label] (query, string, 可选) - Filter tickets linked to one or more product names (comma separated)
    - filter[used_between] (query, string, 可选) - Filter tickets used between two dates (comma separated)
    - filter[created_between] (query, string, 可选) - Filter tickets created between two dates (comma separated)
    - filter[is_used] (query, string, 可选) - Filter tickets by usage state (true/false)
    - filter[usage_count_greater_than] (query, integer, 可选) - Filter tickets with at least the provided number of usages
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /tickets/batch/export
- 作用: 用于当前系统中 Tickets 模块的接口，通过 `POST /tickets/batch/export` 导出 tickets 相关数据，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[venture_id] (query, string, 可选) - Filter batches with an EXACT venture ID
  - Body:
    - 无
- 响应体:
  - 200: Success | application/pdf -> object
  - 401: Not allowed

### POST /tickets/export
- 作用: 用于当前系统中 Tickets 模块的接口，通过 `POST /tickets/export` 导出 tickets 相关数据，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[venture_id] (query, string, 可选) - Filter tickets with an EXACT venture ID
    - filter[sale_id] (query, string, 可选) - Filter tickets with an EXACT sale ID
    - filter[sale_item_id] (query, string, 可选) - Filter tickets with an EXACT sale item ID
    - filter[order_id] (query, string, 可选) - Scope tickets with an EXACT order ID
    - filter[created_at] (query, string, date-time, 可选) - Filter tickets with a partial creation datetime
    - filter[updated_at] (query, string, date-time, 可选) - Filter tickets with a partial update datetime
    - filter[client_buyer] (query, string, 可选) - Filter tickets by buyer client's name or society
    - filter[number] (query, string, 可选) - Filter tickets with a partial number
    - filter[sale_number] (query, string, 可选) - Scope tickets with an EXACT sale number
    - filter[used] (query, boolean, 可选) - Filter tickets by usage status
    - filter[sold_between] (query, string, 可选) - Filter tickets sold between two dates
    - filter[used_at] (query, string, date, 可选) - Filter tickets used on a specific date
    - filter[expired_at] (query, string, date-time, 可选) - Filter tickets expiring on a specific date
    - filter[show_cancelled] (query, boolean, 可选) - Include cancelled tickets
    - filter[product_label] (query, string, 可选) - Filter tickets linked to one or more product names (comma separated)
    - filter[used_between] (query, string, 可选) - Filter tickets used between two dates (comma separated)
    - filter[created_between] (query, string, 可选) - Filter tickets created between two dates (comma separated)
    - filter[is_used] (query, string, 可选) - Filter tickets by usage state (true/false)
    - filter[usage_count_greater_than] (query, integer, 可选) - Filter tickets with at least the provided number of usages
  - Body:
    - 无
- 响应体:
  - 200: Success | application/pdf -> object
  - 401: Not allowed

### POST /tickets/pdf
- 作用: 用于当前系统中 Tickets 模块的接口，通过 `POST /tickets/pdf` 生成或获取 tickets 的 PDF 文件。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{sale_item_id}
- 响应体:
  - 200: Success | application/pdf -> object
  - 401: Not allowed

### POST /tickets/update-expired-at
- 作用: 用于当前系统中 Tickets 模块的接口，通过 `POST /tickets/update-expired-at` 更新过期时间。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{sale_item_id, expired_at}
- 响应体:
  - 200: Success | application/pdf -> object
  - 401: Not allowed

### GET /tickets/{ticket_id}
- 作用: 用于当前系统中 Tickets 模块的接口，通过 `GET /tickets/{ticket_id}` 获取 tickets 详情。
- 请求体:
  - 参数:
    - ticket_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### PATCH /tickets/{ticket_id}
- 作用: 用于当前系统中 Tickets 模块的接口，通过 `PATCH /tickets/{ticket_id}` 更新 tickets 部分字段。
- 请求体:
  - 参数:
    - ticket_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{number, expired_at}
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /tickets/{ticket_id}/pdf
- 作用: 用于当前系统中 Tickets 模块的接口，通过 `GET /tickets/{ticket_id}/pdf` 生成或获取 tickets 的 PDF 文件。
- 请求体:
  - 参数:
    - ticket_id (path, string, 必填) - The ticket_id of the Ticket we wish to get.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/pdf -> object
  - 401: Not allowed

## Transactions
> Operations related to Transactions

### GET /transactions
- 作用: 用于当前系统中 Transactions 模块的接口，通过 `GET /transactions` 获取 transactions 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[venture_id] (query, string, uuid, 可选) - Filter orders with venture_id
    - filter[pos_session_id] (query, string, uuid, 可选) - Filter transactions on pos_session_id
    - filter[paymode_id] (query, string, uuid, 可选) - Filter transactions on paymode_id
    - filter[pos_id] (query, string, uuid, 可选) - Scope transactions on pos_id
    - filter[uncaptured] (query, boolean, 可选) - Filter only uncaptured transactions
    - filter[deferred] (query, boolean, 可选) - Scope only transactions with deferred paymodes.
    - filter[external] (query, boolean, 可选) - Scope only transactions with external payment providers.
    - filter[created_between] (query, string, 可选) - Scope transactions created between two dates.
    - filter[amount_between] (query, string, 可选) - Scope transactions with amount between two integers.
    - filter[last_reviewed_at] (query, string, 可选) - Scope transactions which were last reviewed at a date (format: Y-m-d). Either specify the date (last_reviewed_at = date), or indicate `false` (no last_reviewed_at).
    - include (query, string, 可选) - Sub-resources to include
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /transactions/{transaction_id}
- 作用: 用于当前系统中 Transactions 模块的接口，通过 `GET /transactions/{transaction_id}` 获取 transactions 详情。
- 请求体:
  - 参数:
    - transaction_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### POST /transactions/{transaction_id}/review
- 作用: 用于当前系统中 Transactions 模块的接口，通过 `POST /transactions/{transaction_id}/review` 执行复核操作。
- 请求体:
  - 参数:
    - transaction_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### DELETE /transactions/{transaction_id}/review
- 作用: 用于当前系统中 Transactions 模块的接口，通过 `DELETE /transactions/{transaction_id}/review` 执行复核操作。
- 请求体:
  - 参数:
    - transaction_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

## Users
> Operations related to Users

### GET /users
- 作用: 用于当前系统中 Users 模块的接口，通过 `GET /users` 获取 users 列表，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[firstname] (query, string, 可选) - Filter users with a partial firstname
    - filter[lastname] (query, string, 可选) - Filter users with a partial lastname
    - filter[email] (query, string, 可选) - Filter users with a partial email
    - filter[is_pinnable] (query, boolean, 可选) - Filter users that have a set pin code
    - filter[with_venture] (query, string, 可选) - Filter users that have access to a given venture
    - filter[active] (query, boolean, 可选) - Filter users that are enabled
    - filter[type] (query, ref: UserType, 可选) - Filter users on exact user type
    - include (query, string, 可选) - Specify which sub-resource to load
    - sort (query, string, 可选) - Sort users by username, firstname, lastname or email. Prefix with - for descending order.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /users
- 作用: 用于当前系统中 Users 模块的接口，通过 `POST /users` 创建 users。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{firstname, lastname, username, email, phone, qweekle_barcode, ventures, password, ...}
- 响应体:
  - 201: Success

### GET /users/{user_id}
- 作用: 用于当前系统中 Users 模块的接口，通过 `GET /users/{user_id}` 获取 users 详情。
- 请求体:
  - 参数:
    - user_id (path, string, 必填) - ID of the user
    - include (query, string, 可选) - Sub-resources to include.
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 400: Bad request

### DELETE /users/{user_id}
- 作用: 用于当前系统中 Users 模块的接口，通过 `DELETE /users/{user_id}` 删除 users。
- 请求体:
  - 参数:
    - user_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success

### PATCH /users/{user_id}
- 作用: 用于当前系统中 Users 模块的接口，通过 `PATCH /users/{user_id}` 更新 users 部分字段。
- 请求体:
  - 参数:
    - user_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{firstname, lastname, username, email, phone, qweekle_barcode, is_on_clock, is_mobile_scan, ...}
- 响应体:
  - 200: Success

### POST /users/{user_id}/assign-role
- 作用: 用于当前系统中 Users 模块的接口，通过 `POST /users/{user_id}/assign-role` 为 users 分配角色。
- 请求体:
  - 参数:
    - user_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{venture_id, role_id}
- 响应体:
  - 200: success

### POST /users/{user_id}/media
- 作用: 用于当前系统中 Users 模块的接口，通过 `POST /users/{user_id}/media` 上传或更新 users 的媒体资源。
- 请求体:
  - 参数:
    - user_id (path, string, 必填)
  - Body:
    - Content-Type: multipart/form-data -> object{avatar}
- 响应体:
  - 200: success

### DELETE /users/{user_id}/media
- 作用: 用于当前系统中 Users 模块的接口，通过 `DELETE /users/{user_id}/media` 上传或更新 users 的媒体资源。
- 请求体:
  - 参数:
    - user_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 200: success

### PATCH /users/{user_id}/password
- 作用: 用于当前系统中 Users 模块的接口，通过 `PATCH /users/{user_id}/password` 更新 users 下 password 部分字段。
- 请求体:
  - 参数:
    - user_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{password, password_confirmation}
- 响应体:
  - 200: Success

### DELETE /users/{user_id}/remove-role
- 作用: 用于当前系统中 Users 模块的接口，通过 `DELETE /users/{user_id}/remove-role` 移除 users 角色。
- 请求体:
  - 参数:
    - user_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{venture_id, role_id}
- 响应体:
  - 200: success

### GET /users/{user_id}/staff-card
- 作用: 用于当前系统中 Users 模块的接口，通过 `GET /users/{user_id}/staff-card` 获取员工卡信息。
- 请求体:
  - 参数:
    - user_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: success

## Vats
> Operations related to Vats

### GET /vats
- 作用: 用于当前系统中 Vats 模块的接口，通过 `GET /vats` 获取 vats 列表。
- 请求体:
  - 参数:
    - 无
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### GET /vats/{vat_id}
- 作用: 用于当前系统中 Vats 模块的接口，通过 `GET /vats/{vat_id}` 获取 vats 详情。
- 请求体:
  - 参数:
    - vat_id (path, string, 必填)
    - withAttributes (query, string, 可选) - Expand vat with attributes
  - Body:
    - 无
- 响应体:
  - 200: Success
  - 401: Not allowed

### PATCH /vats/{vat_id}
- 作用: 用于当前系统中 Vats 模块的接口，通过 `PATCH /vats/{vat_id}` 更新 vats 部分字段。
- 请求体:
  - 参数:
    - vat_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{label, rate, account_code, position}
- 响应体:
  - 200: Success

## Ventures
> Operations related to Ventures

### GET /ventures
- 作用: 用于当前系统中 Ventures 模块的接口，通过 `GET /ventures` 获取 ventures 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[name] (query, string, 可选) - Filters ventures on a PARTIAL name.
    - filter[short_name] (query, string, 可选) - Filters ventures on an EXACT short_name.
    - filter[slug] (query, string, 可选) - Filters ventures on an EXACT slug.
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### GET /ventures/mail-placeholders/{template}
- 作用: 用于当前系统中 Ventures 模块的接口，通过 `GET /ventures/mail-placeholders/{template}` 获取邮件模板占位符。
- 请求体:
  - 参数:
    - template (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 404: No Match for query

### GET /ventures/{venture_id}
- 作用: 用于当前系统中 Ventures 模块的接口，通过 `GET /ventures/{venture_id}` 获取 ventures 详情。
- 请求体:
  - 参数:
    - venture_id (path, string, 必填)
    - withAttributes (query, string, 可选) - Expand venture with attributes
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data}
  - 404: No Match for query

### PATCH /ventures/{venture_id}
- 作用: 用于当前系统中 Ventures 模块的接口，通过 `PATCH /ventures/{venture_id}` 更新 ventures 部分字段。
- 请求体:
  - 参数:
    - venture_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{name, short_name, slug, address, phone, email, website, num, ...}
- 响应体:
  - 200: Success

### POST /ventures/{venture_id}/media
- 作用: 用于当前系统中 Ventures 模块的接口，通过 `POST /ventures/{venture_id}/media` 上传或更新 ventures 的媒体资源。
- 请求体:
  - 参数:
    - venture_id (path, string, 必填)
  - Body:
    - Content-Type: multipart/form-data -> object{image, ticket_logo_image}
- 响应体:
  - 200: Success

## Vouchers
> Operations related to Vouchers

### GET /vouchers
- 作用: 用于当前系统中 Vouchers 模块的接口，通过 `GET /vouchers` 获取 vouchers 列表，并支持条件筛选、支持排序。
- 请求体:
  - 参数:
    - filter[venture_id] (query, string, 可选) - Filter vouchers with an exact venture_id
    - filter[code] (query, string, 可选) - Filter vouchers with a partial code
    - filter[label] (query, string, 可选) - Filter vouchers with a partial label
    - filter[active] (query, boolean, 可选) - Scope only is_active vouchers
    - filter[type] (query, ref: VoucherType, 可选) - Filter vouchers with an exact type
    - filter[label_or_code] (query, string, 可选) - Filter vouchers with a partial match on label or code
    - sort (query, string, 可选) - Sort vouchers with a given key
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /vouchers
- 作用: 用于当前系统中 Vouchers 模块的接口，通过 `POST /vouchers` 创建 vouchers。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, priority_order, type, label, description, code, product_id, amount, ...}
- 响应体:
  - 200: Success | application/json -> object{data}
  - 404: No Match for query

### GET /vouchers/{voucher_id}
- 作用: 用于当前系统中 Vouchers 模块的接口，通过 `GET /vouchers/{voucher_id}` 获取 vouchers 详情。
- 请求体:
  - 参数:
    - voucher_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### DELETE /vouchers/{voucher_id}
- 作用: 用于当前系统中 Vouchers 模块的接口，通过 `DELETE /vouchers/{voucher_id}` 删除 vouchers。
- 请求体:
  - 参数:
    - voucher_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success
  - 404: No Match for query

### PATCH /vouchers/{voucher_id}
- 作用: 用于当前系统中 Vouchers 模块的接口，通过 `PATCH /vouchers/{voucher_id}` 更新 vouchers 部分字段。
- 请求体:
  - 参数:
    - voucher_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{venture_id, priority_order, label, description, code, product_id, amount, qty, ...}
- 响应体:
  - 200: Success | application/json -> object{data}
  - 404: No Match for query

## Waivers
> Operations related to Waivers

### GET /waivers
- 作用: 用于当前系统中 Waivers 模块的接口，通过 `GET /waivers` 获取 waivers 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[email] (query, string, 可选)
    - filter[client_id] (query, string, 可选)
    - filter[venture_id] (query, string, 可选)
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

## Webhooks
> Operations related to Webhooks

### GET /webhooks
- 作用: 用于当前系统中 Webhooks 模块的接口，通过 `GET /webhooks` 获取 webhooks 列表，并支持条件筛选。
- 请求体:
  - 参数:
    - filter[venrture_id] (query, string, 可选) - Filter webhooks with an exact venture_id
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /webhooks
- 作用: 用于当前系统中 Webhooks 模块的接口，通过 `POST /webhooks` 创建 webhooks。
- 请求体:
  - 参数:
    - 无
  - Body:
    - Content-Type: application/json -> object{venture_id, provider_id, url, description, is_active, events, is_contactable, is_on_app, ...}
- 响应体:
  - 201: Success

### GET /webhooks/{webhook_id}
- 作用: 用于当前系统中 Webhooks 模块的接口，通过 `GET /webhooks/{webhook_id}` 获取 webhooks 详情。
- 请求体:
  - 参数:
    - webhook_id (path, string, 必填)
  - Body:
    - 无
- 响应体:
  - 200: Success

### DELETE /webhooks/{webhook_id}
- 作用: 用于当前系统中 Webhooks 模块的接口，通过 `DELETE /webhooks/{webhook_id}` 删除 webhooks。
- 请求体:
  - 参数:
    - webhook_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 204: Success

### PATCH /webhooks/{webhook_id}
- 作用: 用于当前系统中 Webhooks 模块的接口，通过 `PATCH /webhooks/{webhook_id}` 更新 webhooks 部分字段。
- 请求体:
  - 参数:
    - webhook_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object{url, description, is_active, events, is_contactable, is_on_app, is_on_shop, is_on_kiosk, ...}
- 响应体:
  - 200: Success

### GET /webhooks/{webhook_id}/calls
- 作用: 用于当前系统中 Webhooks 模块的接口，通过 `GET /webhooks/{webhook_id}/calls` 获取调用记录。
- 请求体:
  - 参数:
    - webhook_id (path, string, 必填) - The webhook id we wish to list calls for
  - Body:
    - 无
- 响应体:
  - 200: Success | application/json -> object{data, metadata}
  - 404: No Match for query

### POST /webhooks/{webhook_id}/calls/{call_id}/resend
- 作用: 用于当前系统中 Webhooks 模块的接口，通过 `POST /webhooks/{webhook_id}/calls/{call_id}/resend` 执行发送操作。
- 请求体:
  - 参数:
    - webhook_id (path, string, 必填)
    - call_id (path, string, 必填)
  - Body:
    - Content-Type: application/json -> object
- 响应体:
  - 201: Success

