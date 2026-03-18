# 镜像同步策略

更新日期：2026-03-18

## 1. 目标

本文件定义数据库与仓库 `project/` 目录之间的镜像同步规则，确保数据库是真相、镜像可审阅且不产生写入口混乱。

## 2. 基本原则

- 数据库是真相
- `project/` 是镜像
- 默认只允许数据库 -> 镜像
- 不允许人工直接修改镜像后默认生效

## 3. 同步方向

### 主方向

- 数据库 -> `project/`

适用：

- 导出 `master_script.md`
- 导出 `scene_list.csv`
- 导出 `shot_list.csv`
- 导出 `take_log.csv`
- 导出治理类文档镜像

### 受控方向

- `project/` -> 数据库

仅在明确导入流程存在时允许。

## 4. 同步触发条件

以下场景应触发镜像同步：

- 剧本导入完成
- 场次或镜头更新完成
- take 记录更新完成
- 审批状态更新完成
- 手动触发导出

## 5. 同步产物

默认同步以下文件：

- `project/01_script/master_script.md`
- `project/01_script/scene_list.csv`
- `project/01_script/shot_list.csv`
- `project/01_script/take_log.csv`
- `project/00_docs/change_request_log.csv`
- `project/00_docs/asset_provenance_log.csv`

## 6. 导入策略

若未来支持镜像导入，必须满足：

- 有明确导入入口
- 先做 schema 校验
- 先做差异比较
- 通过权限检查
- 必要时走审批
- 导入后生成审计日志

## 7. 导入失败处理

以下情况导入失败：

- CSV schema 不匹配
- 枚举值非法
- 外键关系不成立
- 冻结点后未经批准修改关键字段
- 导入内容会覆盖已批准结果

失败时必须输出：

- 差异摘要
- 失败原因
- 建议修复动作

## 8. 冲突处理

冲突优先级如下：

1. 数据库当前状态
2. 已批准审批结果
3. 仓库镜像内容

若镜像内容与数据库冲突，以数据库为准。

## 9. 审批要求

以下导入动作必须审批：

- 冻结点后导入结构性修改
- 替换关键镜头的 `selected_take`
- 修改关键预算字段
- 修改商用状态

## 10. 审计要求

每次同步或导入都必须记录：

- `project_id`
- `sync_direction`
- `trigger_source`
- `changed_files`
- `status`
- `operator`
- `started_at`
- `finished_at`

## 11. 首期建议

首期只实现：

- 数据库 -> `project/` 单向导出

不建议首期实现：

- 人工编辑镜像后自动回写数据库
- 双向自动合并
- 复杂冲突自动解决
