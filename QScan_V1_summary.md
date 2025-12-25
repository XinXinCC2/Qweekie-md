# QScan PoC -> V1 文档摘要

来源：https://qweekle.atlassian.net/wiki/external/MGYxMzFkNTkwYTAwNDBhYmE1OWM5ZTI4YTc1NDkwMTE#5.-Livrables
页面标题：QScan V1（更新日期：2025-11-13）

## 1. 项目背景与使用场景
- Qweekle 提供移动端应用 QScan，用于移动设备门禁验票（PDA/Android），无需固定硬件。
- 典型流程：扫码（二维码/条码）→ 实时请求 Qweekle API → 返回结果（有效/无效/拒绝原因等）。

## 2. 现有 QScan（PoC）
- 现状：Android APK 侧载安装（示例设备 OXHOO TA650，Android 11）。
- 流程：USB 安装 → 配置 API Key、Venture/Caisse → 配置 Oxhoo BarcodeService。
- 体验：扫码后显示“扫描中”，后台查验，返回绿/红结果；可查看历史。
- 量级：旺季单 PDA 200–300 次/小时。
- 限制：
  - 无手动输入（条码不可读时不友好）。
  - 无应用层“扫描间隔”控制（依赖硬件设置）。
  - 结果展示与收银端不一致，拒绝原因不清晰。
  - 多实例导致重复扫码与数据问题。
  - 无离线模式，缺乏清晰连通状态提示。

## 3. 计划演进
### 3.1 主要功能
- 手动输入票号（字母数字）。
- 连续扫描“冷却期”（默认 500ms）。
- 展示与收银端统一（头像、上次通过时间、拒绝原因等）。
- 单实例模式避免多开。
- 最近 20 条扫描历史（重启清空）。
- 连接状态（Internet + Qweekle 服务器）。
- 降级模式（断网允许/拒绝/询问，后续补验）。
- 三个页面：Scan / Configuration / Rapport。

### 3.2 运行流程
- 初始化配置：安装 APK → 首次输入/扫码 API Key → 选择 Venture 与 Caisse → 保存配置。
- 正常模式：扫码（相机/扫码服务/手输）→ 调 API → 展示结果 + 通知；“冷却期”防重复扫描。
- 降级模式：
  - Refuser tout：断网拒绝并提示。
  - Accepter tout：灰色接受并暂存，待联网补验。
  - Demander：由操作员确认，接受后暂存。
  - 恢复网络后逐条补验并生成报告。

### 3.3 UI / UX 原则
- 1 秒内可读结果，信息简洁，颜色 + 声音/震动强化反馈。
- 颜色约定：绿=有效，红=拒绝，灰=处理中；降级模式橙/蓝分别代表拒绝/接受。
- 常驻状态栏：Internet 与服务器状态。
- 结果下方可显示补充信息（头像、拒绝原因、上次通过时间）。

### 3.4 设备配对（Pairing）
- 当前无原生 pairing API，先使用二维码内含 JSON（API Key、tenant ID）方式。
- 架构需可扩展，便于未来替换为更安全的账号/临时 token 机制。

## 4. 非功能要求
- 性能：结果“接近即时”。
- 资源：降低电池消耗。
- 稳定性：高强度使用（200–300 次/小时）。
- 安装：APK 侧载或更简化方案。
- 兼容：最低 Android 11。

## 5. 交付物
- APK
- 源码
- 版本说明
- 技术文档（安装与配置）

## 6. 附录
### 6.1 Oxhoo Barcode Service 传输方式
- 可选：Key events / Clipboard / Broadcast / TCP Broadcast / UDP Broadcast。
- 现有版本用 Clipboard；新版本不限定方案。

### 6.2 Qweekle API 要点（与 QScan 相关）
- 认证：Bearer API Key + 账号唯一标识 Header。
- Ventures 列表：用于选择站点/园区；包含名称与 ID。
- Caisses 列表：用于选择验票收银；包含名称与 ID；未来会增加“是否显示客户信息”的标记。
- 扫描接口：用于校验票；可能返回 400 “Open Pos_Session First”，需先打开收银会话。
- 追加信息获取：
  - `metadata.scanned_resource.ref_type` + `ref_id` 决定补充查询。
  - Ticket：GET `/tickets/{id}` → 过期时间、总次数、已用次数、上次通过时间、客户信息。
  - Booking：GET `/bookings/{id}` → 位置、开始时间、客户信息；“上次通过”暂缺。
