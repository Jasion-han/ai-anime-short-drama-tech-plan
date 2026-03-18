# 内部生产系统架构方案

更新日期：2026-03-18

## 1. 目标

本架构用于支撑 AI 动漫短剧内部生产系统，目标是：

- 支持多项目并行
- 支持多人协作
- 支持阶段自动推进
- 支持多供应商工具接入
- 支持预算、审计、合规和冻结点管理

## 2. 架构原则

- 数据库是真相
- `project/` 是镜像，不是最终主事实源
- 所有外部工具调用必须可追踪、可重试、可计费
- 自动推进只覆盖低风险、规则明确的节点
- 关键里程碑、预算例外、合规例外必须人工批准

## 3. 总体结构

系统分为三层：

### 3.1 编排控制层

负责：

- 项目创建与配置
- 阶段状态判断
- 里程碑推进
- 预算检查
- 任务生成与分发
- 审批流触发

核心模块：

- `project-service`
- `workflow-orchestrator`
- `task-dispatcher`
- `approval-audit-service`

### 3.2 生产执行层

负责：

- 文本任务执行
- 图片任务执行
- 视频任务执行
- TTS / 音频任务执行
- QC 审查任务执行

核心模块：

- `tool-adapters`
- `tool-workers`

### 3.3 事实与资产层

负责：

- 项目、剧本、镜头、take、审批等结构化数据
- 图片、音频、视频等产物存储
- 仓库镜像导出

核心模块：

- `PostgreSQL`
- `S3` 兼容对象存储
- `mirror-sync-service`

## 4. 推荐部署形态

首期推荐以下运行单元：

- `api-server`
- `workflow-worker`
- `tool-workers`
- `mirror-sync-worker`
- `postgres`
- `redis`
- `object-storage`

说明：

- `api-server` 提供内部控制台和自动化调用接口
- `workflow-worker` 负责阶段判断、预算规则、任务推进
- `tool-workers` 负责执行外部工具调用
- `mirror-sync-worker` 负责把数据库状态导出为 `project/` 镜像

## 5. 主数据模型

主数据模型的字段级定义、关系约束、索引建议与镜像映射，统一以 `data_model.md` 为准。

核心实体范围如下：

- 项目域：`projects`、`project_configs`、`project_members`
- 内容域：`scripts`、`scenes`、`shots`、`dialogue_lines`
- 生产域：`takes`、`assets`、`tasks`、`runs`
- 治理域：`approvals`、`change_requests`、`audit_logs`、`budget_events`

## 6. 并行协作设计

为支持多项目并行和多人协作，系统必须具备：

- 项目级隔离
- 镜头级任务 ownership
- 队列优先级调度
- 事务化状态更新
- 操作审计和回放

建议：

- 项目级配置独立
- 每个 `shot` 维护 `owner`、`assignee`、`status`
- 高价值项目和关键镜头可提高队列优先级
- 所有状态流转由服务端统一执行，不允许前端或脚本直接绕过规则改库

## 7. 数据与镜像同步

同步规则如下：

1. 数据库写入后，异步触发镜像导出
2. 镜像文件导出到仓库 `project/` 目录
3. 镜像包括：
   - `master_script.md`
   - `scene_list.csv`
   - `shot_list.csv`
   - `take_log.csv`
   - 其他项目治理文档
4. 人工直接修改镜像文件时，不自动回写数据库
5. 若确需支持镜像导入，必须经过 schema 校验和差异确认

## 8. 状态推进

状态推进、自动节点、审批节点、预算触发和异常回退规则，统一以 `workflow_automation.md` 为准。

本文件只保留架构级原则：

- 系统支持阶段自动推进
- 自动推进只适用于规则明确、低风险节点
- 冻结点、超预算、合规例外必须人工批准

## 9. 首期不做的内容

- 微服务化细粒度拆分
- 客户门户
- 全链路无人审批自动发布
- 复杂实时协同编辑

## 10. 推荐落地顺序

1. 数据模型与 API
2. 阶段与预算规则引擎
3. 仓库镜像同步
4. 文本 / 图片 / 视频 / TTS 适配器
5. 审批与审计
6. 内部控制台
