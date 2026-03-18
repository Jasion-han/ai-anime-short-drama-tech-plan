# 数据模型方案

更新日期：2026-03-18

## 1. 目标

本文件定义内部生产系统的核心数据模型，用于支撑多项目并行、多人协作、阶段自动推进、供应商执行记录和审计留痕。

## 2. 建模原则

- 数据库是系统真相
- Git 仓库中的 `project/` 是镜像层，不是最终主写入口
- 核心实体必须支持审计字段和软删除策略
- 关键状态流转必须由服务端统一执行

## 3. 核心实体

### 3.1 项目域

#### `projects`

建议字段：

- `id`
- `code`
- `name`
- `status`
- `current_phase`
- `target_platform`
- `target_aspect_ratio`
- `target_duration_s`
- `target_language`
- `multi_language_required`
- `project_budget_total`
- `owner_id`
- `created_at`
- `updated_at`

#### `project_configs`

建议字段：

- `project_id`
- `planning_budget_cap`
- `asset_budget_cap`
- `animatic_budget_cap`
- `polish_budget_cap`
- `delivery_budget_cap`
- `shot_budget_cap`
- `critical_shot_budget_cap`
- `approved_toolchain_json`
- `runtime_root`

#### `project_members`

建议字段：

- `project_id`
- `user_id`
- `role`
- `joined_at`

## 3.2 内容域

#### `scripts`

建议字段：

- `id`
- `project_id`
- `version`
- `title`
- `logline`
- `content_markdown`
- `freeze_status`
- `is_active`
- `created_by`
- `created_at`

#### `scenes`

建议字段：

- `id`
- `project_id`
- `script_id`
- `scene_id`
- `location`
- `time_of_day`
- `summary`
- `characters`
- `dramatic_goal`
- `emotion`
- `estimated_duration_s`
- `sort_order`
- `created_at`
- `updated_at`

唯一约束建议：

- `project_id + scene_id`

#### `shots`

建议字段：

- `id`
- `project_id`
- `scene_id`
- `shot_id`
- `beat`
- `duration_s`
- `shot_type`
- `camera_motion`
- `characters`
- `dialogue`
- `audio_line_id`
- `emotion`
- `bg_ref`
- `char_ref`
- `char_ref_version`
- `bg_ref_version`
- `style_version`
- `status`
- `selected_take_id`
- `qc_status`
- `priority`
- `must_keep`
- `retry_count`
- `estimated_cost`
- `actual_cost`
- `owner_id`
- `assignee_id`
- `change_reason`
- `created_at`
- `updated_at`

唯一约束建议：

- `project_id + shot_id`

## 3.3 生产域

#### `takes`

建议字段：

- `id`
- `project_id`
- `shot_id`
- `take_id`
- `vendor_name`
- `model_name`
- `model_version`
- `workflow_type`
- `prompt`
- `negative_prompt`
- `seed`
- `duration_s`
- `aspect_ratio`
- `ref_ids`
- `input_asset_versions`
- `output_uri`
- `status`
- `failure_reason`
- `cost`
- `credits`
- `selected_for_edit`
- `operator_id`
- `created_at`

唯一约束建议：

- `project_id + take_id`

#### `assets`

建议字段：

- `id`
- `project_id`
- `asset_id`
- `asset_type`
- `asset_version`
- `source_type`
- `source_tool`
- `source_link`
- `license_scope`
- `commercial_status`
- `storage_uri`
- `owner_id`
- `notes`
- `recorded_at`

#### `tasks`

建议字段：

- `id`
- `project_id`
- `task_type`
- `phase`
- `entity_type`
- `entity_id`
- `priority`
- `status`
- `payload_json`
- `created_by`
- `assigned_to`
- `created_at`
- `updated_at`

#### `runs`

建议字段：

- `id`
- `project_id`
- `task_id`
- `run_id`
- `vendor_name`
- `adapter_name`
- `request_payload_hash`
- `status`
- `duration_ms`
- `cost`
- `credits`
- `output_uri`
- `error_code`
- `error_message`
- `started_at`
- `finished_at`

## 3.4 治理域

#### `approvals`

建议字段：

- `id`
- `project_id`
- `approval_type`
- `phase`
- `status`
- `requested_by`
- `request_reason`
- `affected_scope_json`
- `risk_level`
- `cost_impact`
- `deadline_impact`
- `approved_by`
- `approved_at`
- `created_at`

#### `change_requests`

建议字段：

- `id`
- `project_id`
- `change_id`
- `phase`
- `freeze_point`
- `change_type`
- `affected_files`
- `affected_scene_ids`
- `affected_shot_ids`
- `reason`
- `expected_impact`
- `cost_impact`
- `schedule_impact`
- `risk_level`
- `fallback_plan`
- `approval_status`
- `approved_by`
- `approved_at`
- `created_at`

#### `audit_logs`

建议字段：

- `id`
- `project_id`
- `entity_type`
- `entity_id`
- `action`
- `actor_id`
- `actor_role`
- `before_snapshot_json`
- `after_snapshot_json`
- `reason`
- `created_at`

## 4. 关系约束

- 一个 `project` 对应多个 `script`、`scene`、`shot`、`task`、`run`
- 一个 `scene` 对应多个 `shot`
- 一个 `shot` 对应多个 `take`
- 一个 `task` 可对应多个 `run`
- 一个 `project` 可对应多个 `approval` 和 `change_request`

## 5. 索引建议

- `projects.current_phase`
- `projects.status`
- `shots.project_id + shots.status`
- `shots.project_id + shots.priority`
- `takes.project_id + takes.shot_id`
- `tasks.project_id + tasks.status + tasks.priority`
- `runs.project_id + runs.status`
- `approvals.project_id + approvals.status`
- `audit_logs.project_id + audit_logs.created_at`

## 6. 与镜像文件的映射

- `scripts` -> `project/01_script/master_script.md`
- `scenes` -> `project/01_script/scene_list.csv`
- `shots` -> `project/01_script/shot_list.csv`
- `takes` -> `project/01_script/take_log.csv`
- `change_requests` -> `project/00_docs/change_request_log.csv`
- `assets` -> `project/00_docs/asset_provenance_log.csv`

## 7. 首期不做

- 通用 JSON schema 动态字段平台
- 多租户跨公司隔离模型
- 实时协同冲突合并算法
