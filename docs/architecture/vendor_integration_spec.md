# 供应商接入规范

更新日期：2026-03-18

## 1. 目标

本规范用于统一文本、图片、视频、TTS、QC 等外部供应商的接入方式，避免项目层直接耦合具体平台接口。

## 2. 接入原则

- 所有外部供应商必须通过适配器层接入
- 供应商能力按任务类型分层，不按品牌硬编码到业务流
- 每次调用都必须记录模型、版本、输入摘要、成本、耗时、输出路径和操作者
- 主生产链路必须支持同层 fallback
- Preview 能力默认不进入正式商用链路，除非条款和可复现性已确认

## 3. 供应商分类

### 3.1 文本类

适用于：

- 剧本拆解
- 场次生成
- 镜头规划
- 角色设定整理
- 风格设定整理
- QC 分析

建议能力标签：

- `text_l1_low_cost`
- `text_l2_mainline`
- `text_l3_high_reasoning`

### 3.2 图片类

适用于：

- 角色参考图
- 场景参考图
- 道具参考图
- 局部修图

建议能力标签：

- `image_reference`
- `image_edit`

### 3.3 视频类

适用于：

- 图生视频
- 文生视频
- 镜头重制
- 镜头改构图

建议能力标签：

- `video_animatic`
- `video_polish`
- `video_modify`

### 3.4 音频类

适用于：

- TTS 占位配音
- 成片配音
- 多语言版本

建议能力标签：

- `tts_draft`
- `tts_final`

### 3.5 QC 类

适用于：

- 图像一致性检查
- 镜头可读性检查
- 连续性与节奏检查

建议能力标签：

- `qc_visual`
- `qc_multimodal`

## 4. 适配器接口要求

每个适配器至少实现以下接口：

- `health_check()`
- `estimate_cost(payload)`
- `submit_task(payload)`
- `poll_result(run_id)`
- `normalize_result(raw_result)`
- `normalize_error(raw_error)`

执行层不直接感知具体平台字段，统一使用内部标准字段。

## 5. 标准执行记录

每次供应商调用必须生成统一执行记录，至少包含：

- `project_id`
- `task_id`
- `run_id`
- `shot_id`
- `phase`
- `tool_type`
- `vendor_name`
- `model_name`
- `model_version`
- `request_payload_hash`
- `cost`
- `credits`
- `duration_ms`
- `status`
- `output_uri`
- `operator`
- `created_at`

## 6. Fallback 规则

fallback 顺序建议如下：

1. 保持同一工作流，调整参数
2. 更换参考资产版本
3. 拆镜头或降低复杂度
4. 切换同层供应商
5. 降级到更低成本链路重新验证
6. 触发人工确认

禁止行为：

- 问题未定位就盲目升级高成本模型
- 未确认条款就直接切换 Preview 工具进入正式链路

## 7. 商用与条款要求

所有供应商接入前必须确认：

- 是否允许商业使用
- 是否可稳定访问
- 是否可复现
- 数据保留策略
- 输出版权归属
- 是否需要平台 AI 披露

未确认前，供应商状态只能为：

- `internal_test_only`

确认内部试跑可用后，可升级为：

- `approved_for_internal_use`

确认满足正式交付后，才可升级为：

- `approved_for_commercial_delivery`

## 8. 账号与密钥管理

- 平台密钥统一由密钥管理系统托管
- 不允许写入仓库、镜像文件或导出文件
- 不同环境使用不同密钥
- 高成本工具建议支持项目级额度限制

## 9. 首期建议接入顺序

1. 文本适配器
2. 图片适配器
3. 视频适配器
4. TTS 适配器
5. QC 适配器

理由：

- 先跑通 Planning 和资产阶段
- 再接 Animatic
- 最后再做自动化 QC 收敛
