# 权限与审计方案

更新日期：2026-03-18

## 1. 目标

本文件定义内部生产系统的角色权限、审批边界、人工覆盖规则和审计记录要求。

## 2. 权限模型

首期采用 RBAC。

角色至少包括：

- `producer`
- `director_editor`
- `artist_operator`
- `qa_reviewer`
- `compliance_reviewer`
- `admin`

## 3. 角色权限

### `producer`

可执行：

- 创建项目
- 管理预算
- 发起里程碑冻结
- 批准预算例外
- 查看全项目状态

### `director_editor`

可执行：

- 维护剧本
- 维护场次和镜头规划
- 提交镜头结构调整申请

### `artist_operator`

可执行：

- 执行图片、视频、音频任务
- 登记产物
- 写入 take 记录

限制：

- 不得直接冻结里程碑
- 不得越权替换关键镜头的最终入选结果

### `qa_reviewer`

可执行：

- 执行 QC 判定
- 标记失败原因
- 退回任务

### `compliance_reviewer`

可执行：

- 审核来源与商用状态
- 审核条款风险
- 提交合规阻塞意见

### `admin`

可执行：

- 管理系统配置
- 管理供应商接入
- 管理密钥与权限

## 4. 审批边界

以下行为必须走审批：

- `M1`、`M2`、`M3` 冻结
- 超预算继续推进
- 替换关键镜头
- 替换关键资产版本
- 变更正式工具链
- 合规例外放行

## 5. 审批记录字段

至少包括：

- `approval_id`
- `project_id`
- `approval_type`
- `phase`
- `requested_by`
- `request_reason`
- `affected_scope`
- `risk_level`
- `cost_impact`
- `deadline_impact`
- `status`
- `approved_by`
- `approved_at`

## 6. 审计范围

以下动作必须写入审计日志：

- 项目配置修改
- 剧本修改
- 场次或镜头修改
- `selected_take` 变更
- 预算阈值触发
- 外部工具调用
- 审批发起与批准
- 人工覆盖自动判断
- 商用状态变更

## 7. 审计字段

建议至少包括：

- `audit_id`
- `project_id`
- `entity_type`
- `entity_id`
- `action`
- `actor_id`
- `actor_role`
- `before_snapshot`
- `after_snapshot`
- `reason`
- `created_at`

## 8. 人工覆盖规则

任何人工覆盖都必须写明：

- 为什么覆盖
- 覆盖了哪条系统判断
- 风险是什么
- 是否影响预算、里程碑或合规状态

没有理由字段时，覆盖不得生效。

## 9. 最小控制要求

- 权限校验必须在服务端执行
- 关键审批动作需要二次确认
- 审计日志不可由普通业务角色删除
- 管理员高风险动作需要额外留痕
