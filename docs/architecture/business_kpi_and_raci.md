# 经营指标与职责分工

更新日期：2026-03-18

## 1. 目标

本文件用于补齐内部生产系统在公司商业化运行时的经营指标、角色责任、审批边界和基础 SLA。

## 2. 核心 KPI

建议至少跟踪以下指标。

### 2.1 项目级指标

- 项目平均交付周期
- 项目预算偏差率
- 里程碑按时完成率
- 项目冻结后变更次数

### 2.2 内容生产指标

- 平均每场镜头数
- 单镜头平均生成次数
- 单镜头通过率
- `selected_take` 选中率
- 镜头返工率

### 2.3 成本指标

- 单项目总成本
- 单阶段平均成本
- 单镜头平均成本
- 高成本镜头占比
- 供应商成本占比

### 2.4 质量指标

- QC 一次通过率
- Animatic 阶段回退率
- 精修阶段返工率
- 导出前缺陷密度

### 2.5 合规指标

- 资产来源完整率
- 商用状态确认完成率
- 条款未确认阻塞次数
- 交付前合规缺陷数

## 3. 推荐目标口径

首期不建议一开始就设过高目标，但建议尽早建立基线。

例如：

- 项目预算偏差率
- 单镜头平均重试次数
- M1 到 M3 的平均周期
- 关键镜头超预算触发率

先持续记录，再逐步设 KPI 目标值。

## 4. RACI

### 4.1 角色定义

- `producer`
- `director_editor`
- `artist_operator`
- `qa_reviewer`
- `compliance_reviewer`
- `admin`

### 4.2 关键活动责任

| 活动 | Responsible | Accountable | Consulted | Informed |
|---|---|---|---|---|
| 项目创建与预算初始化 | producer | producer | admin | 全组 |
| 剧本整理与场次拆解 | director_editor | producer | qa_reviewer | artist_operator |
| 镜头规划 | director_editor | producer | artist_operator | qa_reviewer |
| 资产生产 | artist_operator | producer | director_editor | qa_reviewer |
| 镜头生成执行 | artist_operator | producer | director_editor | qa_reviewer |
| QC 审查 | qa_reviewer | producer | director_editor | artist_operator |
| 合规审查 | compliance_reviewer | producer | admin | 全组 |
| M1/M2/M3 冻结批准 | producer | producer | qa_reviewer | 全组 |
| 超预算例外批准 | producer | producer | admin | 全组 |
| 工具链变更批准 | admin | producer | compliance_reviewer | 全组 |

## 5. 审批边界

审批边界的执行规则统一以 `workflow_automation.md` 和 `permissions_and_audit.md` 为准。

本文件只保留管理视角要求：

- 关键里程碑必须有明确责任人批准
- 超预算、关键镜头替换、工具链变更必须进入管理视图
- 经营看板应能反映审批积压和审批时效

## 6. 基础 SLA 建议

建议首期定义最低内部 SLA：

- 普通任务排队响应时间
- 关键审批响应时间
- P1 异常响应时间
- 镜像同步恢复时间
- 预算异常告警处理时间

首期可先按内部约定执行，不急于对外承诺。

## 7. 经营与系统联动

系统应支持自动生成以下管理视图：

- 项目成本看板
- 阶段推进看板
- 镜头通过率看板
- 供应商成本与失败率看板
- 合规阻塞看板

这些看板应直接基于系统数据生成，而不是人工汇总 Excel。
