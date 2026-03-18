# API 与事件契约

更新日期：2026-03-18

## 1. 目标

本文件定义内部生产系统首期 API、异步任务事件、幂等规则和回调契约，供后端、Worker、前端和适配器开发统一实现。

## 2. 设计原则

- API 契约优先于实现细节
- 同一个业务动作只能有一套权威输入输出定义
- 异步任务必须具备可追踪的 `task_id` 和 `run_id`
- 所有写操作必须支持审计和权限检查
- 所有重复提交场景必须有幂等策略

## 3. API 范围

首期 API 分为以下模块：

- 项目管理 API
- 内容结构 API
- 任务调度 API
- 审批与变更 API
- 查询与状态 API
- 镜像同步 API

## 4. 项目管理 API

### `POST /projects`

作用：

- 创建项目

请求字段：

- `name`
- `target_platform`
- `target_aspect_ratio`
- `target_duration_s`
- `target_language`
- `project_budget_total`

响应字段：

- `id`
- `code`
- `name`
- `current_phase`
- `status`
- `created_at`

### `GET /projects/{project_id}`

作用：

- 查询项目基础信息和当前阶段

### `PATCH /projects/{project_id}`

作用：

- 更新允许修改的项目配置

限制：

- 预算上限、平台、画幅等关键字段修改必须记录审计

## 5. 内容结构 API

### `POST /projects/{project_id}/scripts/import`

作用：

- 导入或更新剧本结构化数据

请求字段：

- `script_version`
- `title`
- `logline`
- `content_markdown`
- `scenes`
- `shots`

返回：

- `script_id`
- `scene_count`
- `shot_count`
- `validation_status`

### `GET /projects/{project_id}/shots`

作用：

- 按条件查询镜头列表

建议查询参数：

- `scene_id`
- `status`
- `priority`
- `assignee_id`

### `PATCH /shots/{shot_id}`

作用：

- 修改镜头级字段

限制：

- `selected_take_id`
- `status`
- `qc_status`
- `owner_id`
- `assignee_id`

这些字段必须经过权限校验和审计。

## 6. 任务调度 API

### `POST /tasks`

作用：

- 创建异步任务

请求字段：

- `project_id`
- `task_type`
- `phase`
- `entity_type`
- `entity_id`
- `priority`
- `payload`
- `idempotency_key`

响应字段：

- `task_id`
- `status`
- `queued_at`

### `GET /tasks/{task_id}`

作用：

- 查询任务状态

响应字段：

- `task_id`
- `status`
- `latest_run_id`
- `retry_count`
- `error_summary`

## 7. 审批与变更 API

### `POST /approvals`

作用：

- 发起审批

请求字段：

- `project_id`
- `approval_type`
- `phase`
- `request_reason`
- `affected_scope`
- `risk_level`
- `cost_impact`
- `deadline_impact`

### `POST /approvals/{approval_id}/approve`

作用：

- 批准审批请求

### `POST /change-requests`

作用：

- 发起冻结点后的变更申请

## 8. 查询与状态 API

### `GET /projects/{project_id}/readiness`

作用：

- 查询当前阶段 readiness

返回：

- `current_phase`
- `ready_for_next_stage`
- `blocking_items`
- `warnings`
- `next_actions`

### `GET /projects/{project_id}/budget-summary`

作用：

- 查询项目预算和阶段预算消耗

## 9. 镜像同步 API

### `POST /projects/{project_id}/mirror-sync`

作用：

- 触发数据库到 `project/` 的镜像导出

### `GET /projects/{project_id}/mirror-sync/status`

作用：

- 查询最近一次镜像同步状态

## 10. 任务事件契约

首期至少定义以下事件：

- `task.created`
- `task.queued`
- `task.started`
- `task.retry_scheduled`
- `task.succeeded`
- `task.failed`
- `approval.created`
- `approval.approved`
- `mirror_sync.completed`
- `mirror_sync.failed`

## 11. 任务事件字段

建议统一字段：

- `event_id`
- `event_type`
- `project_id`
- `task_id`
- `run_id`
- `entity_type`
- `entity_id`
- `status`
- `payload_version`
- `occurred_at`

## 12. 幂等规则

以下写操作必须支持 `idempotency_key`：

- 创建任务
- 发起审批
- 发起变更申请
- 触发镜像同步

规则：

- 同一调用方、同一 `idempotency_key`、同一动作语义，必须返回同一业务结果
- 幂等窗口建议至少保留 24 小时

## 13. 回调与状态更新

外部适配器或 Worker 回写结果时，至少带：

- `task_id`
- `run_id`
- `status`
- `cost`
- `credits`
- `output_uri`
- `error_code`
- `error_message`
- `finished_at`

## 14. 错误响应规范

统一错误字段：

- `error_code`
- `message`
- `details`
- `request_id`

建议错误分类：

- `validation_error`
- `permission_denied`
- `governance_block`
- `not_found`
- `conflict`
- `external_tool_error`
