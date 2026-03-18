# 内部生产系统设计

日期：2026-03-18
状态：approved
范围：内部生产系统方案

## 1. 目标

本设计用于补齐现有 AI 动漫短剧方案中缺失的公司级内部生产系统层，使系统可以从单项目文档执行，升级为支持多项目并行、多人协作、阶段自动推进、预算控制、审计留痕和供应商接入的内部生产平台。

不包含以下内容：

- 对外客户门户
- 外部客户交付协同界面
- 首期高度自动化全链路无人值守生产

## 2. 结论

采用以下方向：

- 定位：内部生产系统
- 协作规模：一开始支持多项目并行和多人协作
- 自动化程度：阶段自动推进
- 技术方向：数据库为主，仓库做项目镜像和交付镜像

## 3. 核心设计决策

### 3.1 系统边界

系统分为三层：

1. 编排控制层：负责项目、阶段、审批、预算、规则判断和任务分发
2. 生产执行层：负责调用文本、图片、视频、TTS、QC 等工具
3. 事实与资产层：数据库保存结构化事实，对象存储保存产物，Git 仓库保留 `project/` 镜像

### 3.2 主事实源

- 数据库是真相
- `project/` 是项目镜像和可审阅层
- Markdown / CSV 不再承担多人协作下的最终主写入口

### 3.3 协作模式

- 项目可并行
- 镜头级任务可多人并行
- 阶段推进可自动触发
- 冻结点、预算例外、合规例外必须人工审批

### 3.4 首期架构

首期不采用复杂微服务，采用：

- `api-server`
- `workflow-worker`
- `tool-workers`
- `mirror-sync-worker`
- `PostgreSQL`
- `Redis`
- `S3` 兼容对象存储

### 3.5 自动化边界

允许自动化：

- 结构校验
- 阶段判定
- 任务分发
- 非关键节点自动推进

不允许自动越权：

- 冻结里程碑
- 超预算继续推进
- 未确认商用状态即正式交付
- 替换关键镜头或关键资产版本

## 4. 核心模块

- `project-service`
- `script-service`
- `workflow-orchestrator`
- `task-dispatcher`
- `tool-adapters`
- `asset-registry`
- `approval-audit-service`
- `mirror-sync-service`

## 5. 主实体

系统首期至少包含以下实体：

- `projects`
- `scripts`
- `scenes`
- `shots`
- `takes`
- `assets`
- `tasks`
- `runs`
- `approvals`
- `change_requests`
- `audit_logs`

## 6. 审批与异常

系统自动推进采用“自动节点 + 审批节点”混合方式。

异常至少区分：

- `validation_error`
- `execution_error`
- `quality_fail`
- `governance_block`

## 7. 安全与权限

首期采用 RBAC，角色至少包括：

- `producer`
- `director_editor`
- `artist_operator`
- `qa_reviewer`
- `compliance_reviewer`
- `admin`

## 8. 交付物

本轮设计对应以下正式文档：

- `docs/architecture/system_architecture.md`
- `docs/architecture/vendor_integration_spec.md`
- `docs/architecture/ops_and_security.md`
- `docs/architecture/business_kpi_and_raci.md`

## 9. 下一步

下一阶段应继续输出实现计划，至少包括：

- 数据库 schema
- 队列任务定义
- 同步规则
- 适配器接口
- CLI / API 边界
- 首期里程碑
