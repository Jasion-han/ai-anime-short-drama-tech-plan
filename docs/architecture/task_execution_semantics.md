# 任务执行语义

更新日期：2026-03-18

## 1. 目标

本文件定义任务和运行记录的执行语义，包括状态机、重试规则、幂等要求、取消语义和失败处理方式。

## 2. 基本概念

- `task`：业务任务，例如“为某镜头生成一版 animatic take”
- `run`：任务的一次具体执行尝试

规则：

- 一个 `task` 可以有多个 `run`
- 一个 `run` 只能对应一次执行尝试
- 一个 `task` 的最终状态由最近有效执行结果和治理状态共同决定

## 3. 任务状态机

任务状态流转建议如下：

```text
created -> queued -> running -> succeeded
created -> queued -> running -> failed
failed -> retry_scheduled -> queued
created -> waiting_for_approval
queued -> cancelled
running -> cancelled
```

## 4. 运行状态机

运行状态流转建议如下：

```text
started -> succeeded
started -> failed
started -> timed_out
started -> cancelled
```

## 5. 重试规则

- `validation_error`：不自动重试
- `execution_error`：允许自动重试
- `quality_fail`：不视为系统级自动重试，改由业务 fallback 决定
- `governance_block`：禁止重试，必须等待人工处理

建议默认重试上限：

- 文本任务：2 次
- 图片任务：2 次
- 视频任务：1 次自动重试
- TTS 任务：2 次
- 镜像同步任务：3 次

## 6. 退避策略

首期建议使用指数退避：

- 第 1 次重试：30 秒
- 第 2 次重试：2 分钟
- 第 3 次重试：10 分钟

关键要求：

- 同一供应商连续失败时应触发告警
- 达到重试上限后必须明确转为 `failed`

## 7. 幂等规则

以下任务必须具备幂等性：

- 镜像同步
- 审批创建
- 预算检查
- 镜头生成任务创建

规则：

- 同一 `idempotency_key` 不得重复创建多个业务语义相同的任务
- Worker 执行前应检查是否已有成功 `run`

## 8. 取消语义

任务可取消的阶段：

- `created`
- `queued`
- `running`

取消规则：

- 已进入 `running` 的任务只能尽力中止
- 外部工具不支持中止时，系统应将任务标记为 `cancel_requested`
- 最终状态应由 Worker 回写为 `cancelled` 或 `succeeded`

## 9. 失败记录要求

失败时至少记录：

- `error_class`
- `error_code`
- `error_message`
- `vendor_name`
- `adapter_name`
- `retry_count`
- `failure_at`

## 10. 业务 fallback 与系统重试的边界

系统重试用于：

- 网络波动
- 平台超时
- 短时服务异常

业务 fallback 用于：

- 参考图不稳定
- 镜头本身不可成立
- 需要拆镜头
- 需要改工作流或换供应商

## 11. 审批阻塞语义

以下情况任务应进入：

- `waiting_for_approval`

适用场景：

- 超预算继续执行
- 冻结点后的结构修改
- 关键镜头替换
- 正式工具链变更

## 12. 选中 take 的一致性

规则：

- 同一 `shot` 最终只能有一个 `selected` take
- 若替换 `selected_take`，必须生成审计日志
- 冻结点后替换 `selected_take` 必须走变更申请
