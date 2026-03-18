# 枚举与状态定义

更新日期：2026-03-18

## 1. 目标

本文件统一定义系统中的核心枚举、状态值和状态流转命名，避免后端、前端、Worker 和镜像文件各自发明不同值。

## 2. 项目阶段

### `project_phase`

允许值：

- `planning`
- `asset_lock`
- `animatic`
- `polish`
- `delivery`

### `project_status`

允许值：

- `active`
- `paused`
- `blocked`
- `completed`
- `archived`

## 3. 里程碑

### `milestone_code`

允许值：

- `M1`
- `M2`
- `M3`
- `M4`
- `M5`

### `freeze_point`

允许值：

- `script_freeze`
- `asset_freeze`
- `animatic_freeze`
- `final_freeze`

## 4. 镜头状态

### `shot_status`

允许值：

- `planned`
- `draft`
- `review`
- `approved_for_edit`
- `redo`
- `dropped`

### `qc_status`

允许值：

- `pending`
- `pass`
- `fail`

### `priority`

允许值：

- `low`
- `normal`
- `high`
- `critical`

### `shot_type`

允许值：

- `dialogue`
- `action`
- `transition`
- `reaction`
- `insert`

### `camera_motion`

允许值：

- `static`
- `pan`
- `tilt`
- `push_in`
- `pull_out`
- `track`
- `handheld_sim`

## 5. 任务状态

### `task_status`

允许值：

- `created`
- `queued`
- `running`
- `waiting_for_approval`
- `succeeded`
- `failed`
- `cancelled`
- `retry_scheduled`

### `run_status`

允许值：

- `started`
- `succeeded`
- `failed`
- `timed_out`
- `cancelled`

## 6. 生产记录状态

### `take_status`

允许值：

- `generated`
- `failed`
- `selected`
- `rejected`

### `workflow_type`

允许值：

- `text2image`
- `image2video`
- `text2video`
- `edit`
- `lip_sync`
- `tts`

## 7. 审批与治理状态

### `approval_status`

允许值：

- `pending`
- `approved`
- `rejected`
- `cancelled`

### `approval_type`

允许值：

- `milestone_freeze`
- `budget_exception`
- `asset_change`
- `shot_change`
- `toolchain_change`
- `compliance_exception`

### `change_type`

允许值：

- `script_change`
- `shot_change`
- `asset_change`
- `budget_exception`
- `toolchain_change`

### `risk_level`

允许值：

- `low`
- `medium`
- `high`
- `critical`

## 8. 供应商与商用状态

### `commercial_status`

允许值：

- `internal_test_only`
- `approved_for_internal_use`
- `approved_for_commercial_delivery`

## 9. 异常分类

### `error_class`

允许值：

- `validation_error`
- `execution_error`
- `quality_fail`
- `governance_block`

## 10. 状态命名规则

- 所有枚举值统一使用 `snake_case`
- 不允许使用 `latest`、`final`、`new` 这类自由文本状态
- 同一业务对象只能有一套权威状态定义

## 11. 权威来源

- 项目、镜头、任务、审批状态命名以本文件为准
- 若其他文档示例与本文件冲突，以本文件为准
