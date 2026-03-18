# Architecture Docs

更新日期：2026-03-18

这个目录是“内部生产系统架构层”。

用途：

- 给老板和项目负责人看系统范围、治理方式和经营指标
- 给架构师和开发者看系统怎么搭、数据怎么建、流程怎么跑
- 给制片、QA、合规看审批、权限、预算、监控和风险控制

这里现在有 8 份文档。

## 文档列表

1. `system_architecture.md`
   - 作用：定义系统边界、核心模块、部署形态、数据库与 `project/` 镜像关系
   - 适合谁看：老板、架构师、Tech Lead

2. `data_model.md`
   - 作用：定义项目、剧本、场次、镜头、take、审批、审计等核心数据模型
   - 适合谁看：架构师、后端开发、数据建模人员

3. `technical_stack.md`
   - 作用：定义首期推荐技术栈、运行单元和服务职责
   - 适合谁看：架构师、后端开发、DevOps

4. `workflow_automation.md`
   - 作用：定义阶段自动推进、审批闸门、预算触发和异常回退
   - 适合谁看：架构师、后端开发、制片负责人、QA

5. `permissions_and_audit.md`
   - 作用：定义角色权限、审批边界、人工覆盖规则和审计要求
   - 适合谁看：架构师、后端开发、管理员、合规负责人

6. `ops_and_security.md`
   - 作用：定义环境、密钥、监控、告警、备份恢复和合规控制点
   - 适合谁看：DevOps、管理员、合规负责人、Tech Lead

7. `vendor_integration_spec.md`
   - 作用：定义文本、图片、视频、TTS、QC 等供应商的接入方式和 fallback 规则
   - 适合谁看：后端开发、适配器开发、架构师

8. `business_kpi_and_raci.md`
   - 作用：定义经营指标、角色职责、审批责任和基础 SLA
   - 适合谁看：老板、项目负责人、制片负责人、运营管理者

## 推荐阅读顺序

如果你是第一次看，按这个顺序最清楚：

1. `system_architecture.md`
2. `data_model.md`
3. `technical_stack.md`
4. `workflow_automation.md`
5. `permissions_and_audit.md`
6. `ops_and_security.md`
7. `vendor_integration_spec.md`
8. `business_kpi_and_raci.md`

## 按角色阅读

### 老板 / 项目负责人

优先看：

- `system_architecture.md`
- `business_kpi_and_raci.md`
- `workflow_automation.md`

### 架构师 / Tech Lead

优先看：

- `system_architecture.md`
- `data_model.md`
- `technical_stack.md`
- `workflow_automation.md`

### 后端开发

优先看：

- `data_model.md`
- `technical_stack.md`
- `workflow_automation.md`
- `vendor_integration_spec.md`
- `permissions_and_audit.md`

### DevOps / 管理员

优先看：

- `technical_stack.md`
- `ops_and_security.md`
- `permissions_and_audit.md`

### 制片 / QA / 合规

优先看：

- `workflow_automation.md`
- `permissions_and_audit.md`
- `ops_and_security.md`
- `business_kpi_and_raci.md`

## 怎么用

- 做系统评审时，从 `system_architecture.md` 开始
- 做数据库设计时，使用 `data_model.md`
- 做服务拆分和选型时，使用 `technical_stack.md`
- 做流程实现时，使用 `workflow_automation.md`
- 做权限、审批、审计实现时，使用 `permissions_and_audit.md`
- 做环境、密钥、告警、恢复设计时，使用 `ops_and_security.md`
- 做外部 AI 工具接入时，使用 `vendor_integration_spec.md`
- 做经营管理、组织协作和 KPI 设定时，使用 `business_kpi_and_raci.md`

## 说明

- 这里是“系统架构文档目录”，不是项目执行数据目录
- 真正的项目事实源和运行时文件仍在仓库根目录的 `project/`
- 这里的文档用于指导开发和管理内部生产系统本身
