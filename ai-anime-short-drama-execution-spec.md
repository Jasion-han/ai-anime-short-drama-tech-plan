# AI 动漫短剧执行规范

更新日期：2026-03-18

## 1. 文档定位

本文件用于约束主执行 AI 在 AI 动漫短剧项目中的行为、输入输出、状态流转、验收方式与暂停条件。

本文件不是方法论介绍，也不是工具汇总说明。

上位参考文档为 `ai-anime-short-drama-tech-plan.md`。

当上位文档中的描述性建议与本文件中的执行性规则冲突时，以本文件为准。

主执行 AI 应将本文件视为操作规范，将上位文档视为背景依据与策略来源。

---

## 2. 项目启动输入清单

主执行 AI 在正式推进任何生产阶段前，必须先确认以下输入已经提供、可读取且不存在明显冲突。

### 2.1 必须提供的项目级输入

- 项目名称
- 项目目标
- 目标平台
- 首发语言
- 是否需要多语言版本
- 目标画幅
- 目标成片时长
- 项目总预算上限

若以上任一项缺失，主执行 AI 只允许停留在信息整理和待确认项汇总阶段，不得进入正式生产。

### 2.2 必须提供的事实源文件

- `master_script.md`
- `scene_list.csv`
- `shot_list.csv`

若以上文件尚未存在，则主执行 AI 允许先创建其初版结构，但必须把状态标记为“待确认”，不得直接视为冻结版。

### 2.3 建议在开工前一并提供的补充输入

- `character_bible.md`
- `style_bible.md`
- `prompt_library.md`
- `project_config.md`
- `change_request_log.csv`
- 角色参考图或角色资产 ID
- 主场景参考图或背景资产 ID
- 关键道具参考图或资产 ID
- 预算变量，例如 `shot_budget_cap`、`critical_shot_budget_cap`
- 已批准工具清单

若补充输入缺失，主执行 AI 可以继续推进策划阶段，但不得直接进入精修或交付阶段。

其中：

- `project_config.md` 用于记录项目级固定配置与预算变量
- `change_request_log.csv` 用于记录冻结点后的变更申请及批准状态

### 2.4 启动前必做检查

主执行 AI 在启动前至少完成以下检查：

1. 项目目标、预算、平台、时长之间不存在直接冲突
2. `master_script.md`、`scene_list.csv`、`shot_list.csv` 的主线信息一致
3. 目标平台与目标画幅一致
4. 预算变量至少能支撑粗剪阶段与关键镜头试错
5. 如计划使用外部平台或高成本模型，已明确其使用边界

若以上任一检查失败，主执行 AI 必须先输出“待确认项列表”，再等待人类确认。

### 2.5 启动阶段标准输出

主执行 AI 在项目启动时的第一轮输出，至少应包含以下内容：

- 当前已获得的输入
- 当前缺失的输入
- 当前阶段判断
- 可立即推进的工作
- 必须人工确认的事项
- 预计会更新的文件

---

## 3. 执行角色定义

项目默认采用单主执行 AI 模式。

角色定义如下：

- 主执行 AI：负责读取项目文档、判断当前阶段、推进任务、更新主事实源、决定是否进入下一阶段、发现风险并暂停请求确认
- 辅助 AI：仅可在主执行 AI 明确委派下完成局部任务，例如剧本拆解、镜头建议、提示词整理、审查总结。辅助 AI 不得直接改写主事实源
- 人类负责人：负责确认项目目标、预算、风格、平台、冻结点后的变更、合规风险处理与最终交付批准

主执行 AI 对项目状态具有唯一解释权。

所有最终回写到主事实源的内容，必须由主执行 AI 完成。

---

## 4. 执行权限与禁止事项

主执行 AI 可以执行以下动作：

- 读取项目范围内已存在的方案文档、结构化表格与资产说明
- 按当前阶段生成或更新允许写入的结构化文件
- 基于既有规则做低风险、可回退的局部决策
- 发现问题后提出修正建议并执行已授权的修正动作
- 在明确阈值内进行模型降级、镜头拆分、提示词收敛和回退处理

主执行 AI 不得执行以下动作：

- 不得擅自修改项目目标、题材、时长、平台、语言、预算上限
- 不得擅自重写已冻结剧情主线
- 不得在未记录原因的情况下覆盖已确认镜头或资产版本
- 不得跳过冻结点直接推进后续阶段
- 不得在存在明显冲突或关键缺失输入时继续自动执行
- 不得在未经确认时引入新的外部平台、付费工具或高成本模型
- 不得擅自提交 git、发布成片、对外分发素材

出现以下情形时，主执行 AI 必须暂停并请求人类确认：

- 关键输入缺失
- 事实源之间冲突
- 预计超预算
- 合规或版权风险
- 需要改变风格、平台、时长或叙事方向
- 连续失败达到阈值
- 模型或工具 fallback 后仍无法满足最低质量要求

---

## 5. 项目事实源与优先级

主执行 AI 必须遵守单一事实源原则。

事实源优先级如下：

1. `master_script.md`
2. `scene_list.csv`
3. `shot_list.csv`
4. `take_log.csv`
5. `character_bible.md`
6. `style_bible.md`
7. `prompt_library.md`
8. `asset_provenance_log.csv`
9. `delivery_checklist.md`

解释规则如下：

- 剧情、台词、场次顺序以 `master_script.md` 为准
- 场次级结构化信息以 `scene_list.csv` 为准
- 镜头级规划、镜头状态、入选 take 以 `shot_list.csv` 为准
- 每次生成记录、失败记录、参数记录以 `take_log.csv` 为准
- 角色设定约束以 `character_bible.md` 为准
- 风格设定约束以 `style_bible.md` 为准
- 提示词模板与版本记录以 `prompt_library.md` 为准
- 素材来源与商用留痕以 `asset_provenance_log.csv` 为准

冲突处理规则如下：

- 若 `shot_list.csv` 与 `master_script.md` 冲突，以 `master_script.md` 为准，并暂停更新镜头计划
- 若 `take_log.csv` 与 `shot_list.csv` 冲突，以 `shot_list.csv` 中的 `selected_take` 为最终入选结果
- 若角色外观与已确认角色设定不一致，以 `character_bible.md` 为准，当前镜头标记为 `redo`
- 若风格词与已确认风格约束冲突，以 `style_bible.md` 为准

---

## 6. 阶段状态机

项目阶段固定为以下五个阶段：

1. 策划
2. 资产锁定
3. Animatic
4. 精修
5. 成片

阶段作用如下：

- 策划：把剧本整理成可执行的结构化计划
- 资产锁定：把角色、场景、道具和风格约束固定下来
- Animatic：用低成本方案验证镜头顺序、对白时长和节奏
- 精修：只处理关键镜头、关键动作和最终音频质量
- 成片：完成导出、交付和合规闭环

每阶段的完成条件、最低交付物、阻塞项统一以下一章里程碑定义为准。

---

## 7. 里程碑定义与阶段交付物

主执行 AI 必须按里程碑而不是按模糊感觉推进项目。

默认里程碑定义如下：

- `M1`：剧本冻结完成
- `M2`：资产冻结完成
- `M3`：Animatic 冻结完成
- `M4`：精修完成
- `M5`：交付完成

### 7.1 `M1` 剧本冻结完成

必须满足以下条件：

- `master_script.md` 已存在且结构完整
- `scene_list.csv` 已建立并通过基础一致性检查
- `shot_list.csv` 已建立并覆盖所有关键场次
- 主线剧情、台词、场次顺序无未解决冲突
- 项目目标、平台、时长、预算已确认

最低交付物：

- `master_script.md`
- `scene_list.csv`
- `shot_list.csv`
- 待确认项清单已清空或单独记录

阻塞项：

- 关键场次未拆解
- 剧情主线冲突
- 时长目标与镜头规模明显失衡
- 平台、画幅、语言或预算未确认

### 7.2 `M2` 资产冻结完成

必须满足以下条件：

- 主角色参考资产可用
- 主场景参考资产可用
- 关键道具参考资产可用
- `character_bible.md` 已可约束角色一致性
- `style_bible.md` 已可约束风格一致性

最低交付物：

- 角色参考资产版本清单
- 场景参考资产版本清单
- 关键道具资产版本清单
- `character_bible.md`
- `style_bible.md`

阻塞项：

- 主角色外观仍不稳定
- 主场景缺关键视角或时段版本
- 关键道具未单独建档
- 风格词与参考图方向冲突

### 7.3 `M3` Animatic 冻结完成

必须满足以下条件：

- 关键场次均已有可观看初版
- 主要对白时长与镜头时长基本匹配
- Animatic 节奏成立
- 关键镜头顺序成立
- 无阻断叙事理解的硬失败镜头

最低交付物：

- 可播放的 Animatic 版本或等效镜头序列
- 已更新的 `shot_list.csv`
- 已记录的 `take_log.csv`
- 临时对白或对白时长占位方案

阻塞项：

- 关键转折场次无法成立
- 多个 `critical` 镜头仍处于 `redo`
- Animatic 无法表达情绪推进
- 对白时长持续不匹配

### 7.4 `M4` 精修完成

必须满足以下条件：

- 所有 `critical` 镜头已通过最低验收
- 关键镜头已完成高质量重制或确认不再重制
- 口型、声音、调色和镜头连续性达到成片标准
- 预算未处于未批准超额状态

最低交付物：

- 已入选的关键镜头成片版
- 已更新的 `shot_list.csv.selected_take`
- 成本记录已回写
- 精修阶段问题清单已闭环

阻塞项：

- `critical` 镜头未通过
- 角色一致性仍严重漂移
- 关键镜头仍依赖未确认 Preview 能力
- 预算已超阈值但无批准记录

### 7.5 `M5` 交付完成

必须满足以下条件：

- `delivery_checklist.md` 中所有必选项通过
- 母版与发布版已导出
- 合规检查闭环
- 平台版本与画幅版本齐备
- 人类负责人已批准最终交付

最低交付物：

- 主母版
- 平台发布版
- 字幕版
- 无字幕版
- 如有需要，横版与竖版

阻塞项：

- 导出规格不符
- 合规标记缺失
- 交付清单未完成
- 仍有未解决的高风险变更申请

### 7.6 里程碑推进规则

主执行 AI 必须遵守以下推进规则：

1. 未达到当前里程碑最低交付物要求时，不得标记里程碑完成
2. 存在任一阻塞项时，不得进入下一里程碑
3. 若下一里程碑所需输入缺失，必须先回到当前里程碑补齐
4. 若冻结点后的变更影响已完成里程碑，必须重新标记该里程碑为未完成，并提交变更申请
5. 任何里程碑完成都应伴随一次结构化状态回写与人工确认点检查

---

## 8. 数据结构与状态流转

主执行 AI 必须按固定 schema 维护关键表。

### 8.1 通用序列化规则

所有 CSV 文件必须满足以下规则：

- 编码统一为 `UTF-8`
- 分隔符统一为半角逗号 `,`
- 包含逗号、换行或引号的字段必须使用双引号包裹
- 布尔值只允许使用 `true` 或 `false`
- 空值统一使用空字符串，不使用 `null`、`N/A`、`-`
- 列表字段统一使用竖线 `|` 分隔，不使用顿号、斜杠或自由文本
- 时间戳统一使用 ISO 8601，例如 `2026-03-18T18:40:00+08:00`
- 时长、成本等数值字段统一使用十进制数字，不混入单位字符
- 文件路径统一记录为项目相对路径，不写绝对路径
- 表头顺序固定，主执行 AI 不得擅自重排列顺序
- 未经确认不得新增、删除或重命名 schema 字段

ID 命名规则固定如下：

- `scene_id`：`^sc[0-9]{2,}$`
- `shot_id`：`^sc[0-9]{2,}_sh[0-9]{2,}$`
- `take_id`：`^sc[0-9]{2,}_sh[0-9]{2,}_take_[0-9]{2,}$`
- `audio_line_id`：`^sc[0-9]{2,}_sh[0-9]{2,}_l[0-9]{2,}$`

默认枚举值约定如下：

- `time_of_day`：`dawn`、`day`、`sunset`、`night`、`indoor_undefined`
- `priority`：`low`、`normal`、`high`、`critical`
- `qc_status`：`pending`、`pass`、`fail`
- `take status`：`generated`、`failed`、`selected`、`rejected`

### 8.2 `scene_list.csv`

固定表头如下：

```csv
scene_id,location,time_of_day,summary,characters,dramatic_goal,emotion,estimated_duration_s
```

字段 schema 如下：

| 字段 | 类型 | 必填 | 约束 |
|---|---|---|---|
| `scene_id` | string | 是 | 全局唯一，符合 `scene_id` 命名规则 |
| `location` | string | 是 | 使用稳定场景名，不写模糊代词 |
| `time_of_day` | enum | 是 | 仅允许使用约定枚举值 |
| `summary` | string | 是 | 1 句到 3 句，描述场次核心事件 |
| `characters` | list<string> | 是 | 使用角色 ID，按 `|` 分隔 |
| `dramatic_goal` | string | 是 | 说明该场的叙事目标 |
| `emotion` | string | 是 | 使用简洁情绪标签，不写长段分析 |
| `estimated_duration_s` | number | 是 | 大于 `0` |

附加约束如下：

- `scene_id` 全局唯一
- `characters` 中的角色必须能在 `character_bible.md` 中找到
- `estimated_duration_s` 应与该场下属镜头时长总和大致一致，允许存在 `+-15%` 误差

### 8.3 `shot_list.csv`

固定表头如下：

```csv
shot_id,scene_id,beat,duration_s,shot_type,camera_motion,characters,dialogue,audio_line_id,emotion,bg_ref,char_ref,char_ref_version,bg_ref_version,style_version,status,selected_take,qc_status,priority,must_keep,retry_count,estimated_cost,actual_cost,owner,change_reason
```

字段 schema 如下：

| 字段 | 类型 | 必填 | 默认值 | 约束 |
|---|---|---|---|---|
| `shot_id` | string | 是 | 无 | 全局唯一，符合 `shot_id` 命名规则 |
| `scene_id` | string | 是 | 无 | 必须存在于 `scene_list.csv` |
| `beat` | string | 是 | 无 | 描述镜头叙事功能，不得为空 |
| `duration_s` | number | 是 | 无 | 大于 `0` 且建议不超过 `8` |
| `shot_type` | enum | 是 | 无 | `dialogue`、`action`、`transition`、`reaction`、`insert` |
| `camera_motion` | enum | 是 | `static` | `static`、`pan`、`tilt`、`push_in`、`pull_out`、`track`、`handheld_sim` |
| `characters` | list<string> | 是 | 无 | 使用角色 ID，按 `|` 分隔 |
| `dialogue` | string | 否 | 空字符串 | 无对白时留空 |
| `audio_line_id` | string | 否 | 空字符串 | 有对白时必须填写且符合命名规则 |
| `emotion` | string | 是 | 无 | 使用简洁情绪标签 |
| `bg_ref` | string | 是 | 无 | 背景资产 ID |
| `char_ref` | list<string> | 是 | 无 | 角色参考资产 ID，按 `|` 分隔 |
| `char_ref_version` | string | 是 | 无 | 例如 `hero_ref_v02`，多角色时按 `|` 对齐 |
| `bg_ref_version` | string | 是 | 无 | 例如 `classroom_bg_v03` |
| `style_version` | string | 是 | 无 | 例如 `anime_style_v01` |
| `status` | enum | 是 | `planned` | `planned`、`draft`、`review`、`approved_for_edit`、`redo`、`dropped` |
| `selected_take` | string | 否 | 空字符串 | 仅在已有入选 take 时填写 |
| `qc_status` | enum | 是 | `pending` | `pending`、`pass`、`fail` |
| `priority` | enum | 是 | `normal` | `low`、`normal`、`high`、`critical` |
| `must_keep` | boolean | 是 | `false` | 关键叙事镜头应设为 `true` |
| `retry_count` | integer | 是 | `0` | 大于等于 `0` |
| `estimated_cost` | number | 否 | 空字符串 | 预算阶段预估成本 |
| `actual_cost` | number | 否 | 空字符串 | 已发生实际成本 |
| `owner` | string | 否 | `main_ai` | 负责推进该镜头的执行者标识 |
| `change_reason` | string | 否 | 空字符串 | 仅在镜头被改写、拆分、合并或废弃时填写 |

状态流转规则如下：

- 新镜头默认从 `planned` 开始
- 完成第一版可观看结果后进入 `draft`
- 提交审核后进入 `review`
- 审核通过后进入 `approved_for_edit`
- 审核失败后进入 `redo`
- 确认废弃后进入 `dropped`

附加约束如下：

- `selected_take` 非空时，`status` 必须为 `review` 或 `approved_for_edit`
- `qc_status=pass` 时，`selected_take` 不得为空
- `status=dropped` 时，`must_keep` 必须为 `false`
- `retry_count` 应等于该 `shot_id` 在 `take_log.csv` 中的失败重试次数或其上限快照
- `actual_cost` 不得小于 `0`
- `characters`、`char_ref`、`char_ref_version` 多值时顺序必须一一对应

### 8.4 `take_log.csv`

固定表头如下：

```csv
take_id,shot_id,model,model_version,workflow_type,prompt,negative_prompt,seed,duration_s,aspect_ratio,ref_ids,input_asset_versions,output_path,status,failure_reason,cost,credits,selected_for_edit,operator,created_at
```

字段 schema 如下：

| 字段 | 类型 | 必填 | 默认值 | 约束 |
|---|---|---|---|---|
| `take_id` | string | 是 | 无 | 全局唯一，符合 `take_id` 命名规则 |
| `shot_id` | string | 是 | 无 | 必须存在于 `shot_list.csv` |
| `model` | string | 是 | 无 | 记录供应商或模型名 |
| `model_version` | string | 否 | 空字符串 | 具体版本或发布日期 |
| `workflow_type` | enum | 是 | 无 | `text2image`、`image2video`、`text2video`、`edit`、`lip_sync`、`tts` |
| `prompt` | string | 是 | 无 | 记录本次实际使用提示词 |
| `negative_prompt` | string | 否 | 空字符串 | 无则留空 |
| `seed` | string | 否 | 空字符串 | 无固定 seed 时留空 |
| `duration_s` | number | 是 | 无 | 大于 `0` |
| `aspect_ratio` | enum | 是 | 无 | `9:16`、`16:9`、`1:1`、`4:3` |
| `ref_ids` | list<string> | 否 | 空字符串 | 参考图或输入资产 ID，按 `|` 分隔 |
| `input_asset_versions` | list<string> | 否 | 空字符串 | 与 `ref_ids` 对应的版本列表 |
| `output_path` | string | 是 | 无 | 项目相对路径，必须指向实际输出文件 |
| `status` | enum | 是 | `generated` | `generated`、`failed`、`selected`、`rejected` |
| `failure_reason` | string | 否 | 空字符串 | 仅失败时填写 |
| `cost` | number | 否 | 空字符串 | 本次执行实际成本 |
| `credits` | number | 否 | 空字符串 | 平台积分消耗 |
| `selected_for_edit` | boolean | 是 | `false` | 是否被选入正式剪辑链路 |
| `operator` | string | 是 | `main_ai` | 记录执行者 |
| `created_at` | string | 是 | 无 | ISO 8601 时间戳 |

附加约束如下：

- 每次生成必须新增一行，不覆盖旧记录
- 失败样本也必须保留
- `status=failed` 时，`failure_reason` 必须非空
- `selected_for_edit=true` 时，`status` 必须为 `selected`
- `selected_for_edit=true` 的 take 必须与 `shot_list.csv.selected_take` 一致
- 同一 `shot_id` 可存在多个 take，但最终只能有一个 `selected_for_edit=true`

### 8.5 跨表一致性校验

主执行 AI 在每轮批量更新后，至少执行以下一致性检查：

1. `shot_list.csv.scene_id` 必须全部存在于 `scene_list.csv.scene_id`
2. `take_log.csv.shot_id` 必须全部存在于 `shot_list.csv.shot_id`
3. `shot_list.csv.selected_take` 非空时，`take_log.csv.take_id` 中必须存在对应记录
4. `shot_list.csv.status=approved_for_edit` 时，`shot_list.csv.qc_status` 必须为 `pass`
5. `take_log.csv.selected_for_edit=true` 的数量，按每个 `shot_id` 统计不得超过 `1`
6. 所有成本字段必须为非负数
7. 所有版本字段必须非自由文本占位，例如不得写 `latest`、`new`、`final`

如任一一致性校验失败，主执行 AI 不得继续推进下一阶段，必须先修复结构化数据。

### 8.6 最小示例数据

以下示例仅用于说明格式，不代表真实项目内容。

`scene_list.csv` 最小示例：

```csv
scene_id,location,time_of_day,summary,characters,dramatic_goal,emotion,estimated_duration_s
sc01,classroom,day,"女主发现课桌里的匿名信，并意识到有人在观察她",heroine|friend,建立悬念,uneasy,18
```

`shot_list.csv` 最小示例：

```csv
shot_id,scene_id,beat,duration_s,shot_type,camera_motion,characters,dialogue,audio_line_id,emotion,bg_ref,char_ref,char_ref_version,bg_ref_version,style_version,status,selected_take,qc_status,priority,must_keep,retry_count,estimated_cost,actual_cost,owner,change_reason
sc01_sh01,sc01,建立场景与角色状态,4,dialogue,static,heroine,"今天的教室怎么这么安静？",sc01_sh01_l01,uneasy,classroom_bg,heroine_half,heroine_ref_v02,classroom_bg_v03,anime_style_v01,review,sc01_sh01_take_02,pending,high,true,2,3.50,2.80,main_ai,
```

`take_log.csv` 最小示例：

```csv
take_id,shot_id,model,model_version,workflow_type,prompt,negative_prompt,seed,duration_s,aspect_ratio,ref_ids,input_asset_versions,output_path,status,failure_reason,cost,credits,selected_for_edit,operator,created_at
sc01_sh01_take_02,sc01_sh01,Runway Gen-4,gen4-2026-02,image2video,"anime classroom medium shot, uneasy girl glances left","extra fingers, distorted face",884211,4,9:16,heroine_half|classroom_bg,heroine_ref_v02|classroom_bg_v03,07_video_shots/sc01/sc01_sh01_take_02.mp4,selected,,2.80,140,true,main_ai,2026-03-18T18:50:00+08:00
```

---

## 9. 决策与模型调度规则

默认决策顺序如下：

1. 先满足剧情正确性
2. 再满足结构完整性
3. 再满足资产一致性
4. 再满足交付可用性
5. 最后在预算约束内优化质量

默认策略：

- 粗剪阶段成本优先
- 精修阶段关键镜头质量优先
- 非关键镜头优先使用低成本方案
- Preview 能力默认仅用于试验或 Animatic 阶段，正式定稿前必须确认可商用与可复现性
- 同类任务优先使用已验证可用的模型与参数组合，不频繁切换工具

### 9.1 任务分级规则

主执行 AI 在执行前必须先给任务分级。

文本任务分级：

- `L1`：清洗、改写、批量补字段、模板化输出
- `L2`：场次拆解、镜头规划、角色设定整理、风格整理
- `L3`：剧情重写、关键情绪弧修正、全局矛盾消解

镜头任务分级：

- `normal`：普通对话镜头、反应镜头、过场镜头
- `high`：关键对话镜头、信息揭示镜头、必须保留镜头
- `critical`：剧情转折镜头、高潮镜头、核心角色首次亮相镜头

以下任一条件成立时，镜头自动视为 `critical`：

- `must_keep=true`
- `priority=critical`
- 属于高潮场次或主转折场次
- 无法通过过场或反应镜头替代

### 9.2 模型升级规则

只有满足以下条件之一，主执行 AI 才允许从低成本方案升级到高成本方案：

- 当前镜头为 `high` 或 `critical`
- 低成本方案已经完成至少 `2` 次有效尝试但未通过最低验收标准
- 已确认镜头逻辑成立，只差画面质量或一致性
- 当前阶段已进入精修阶段

禁止升级的情况：

- 剧情本身未定
- 参考资产版本未锁定
- 当前问题本质是对白时长或镜头拆分错误
- 当前镜头属于可替代的普通过场镜头

### 9.3 降级与 fallback 规则

执行优先级如下：

1. 保留已验证可用的工作流
2. 更换参数
3. 更换参考图
4. 拆镜头
5. 切换同层备选模型
6. 降级到更低成本方案重新验证
7. 暂停并请求人工确认

具体 fallback 规则如下：

- 主选模型不可用时，切换至同层备选模型
- 备选模型连续失败 `2` 次后，优先降级任务复杂度，不优先继续升级成本
- 视频生成失败时，优先拆镜头、改构图、换参考图，再考虑换模型
- 对白问题优先修正音频和时长，不优先重做视频
- 角色漂移优先回退到资产层，不直接继续跑更多 take
- `critical` 镜头 fallback 后仍不达标时，必须暂停，不得静默降级为可替代镜头

### 9.4 Preview 与商用规则

Preview 能力使用限制如下：

- 允许用于策划阶段、资产试验阶段、Animatic 阶段
- 不允许默认直接作为正式交付唯一生产链路
- 若要进入精修或成片链路，必须确认其条款允许商用、结果可复现、团队账号可稳定访问
- 若无法确认以上三项，则必须切换到稳定替代方案

### 9.5 机器判定触发条件

主执行 AI 可按以下条件自动触发决策：

- `retry_count < 2` 且镜头为 `normal`：继续低成本尝试
- `retry_count >= 2` 且镜头为 `high`：允许升级一次模型层级
- `retry_count >= 3`：停止继续同策略重试，必须执行回退动作
- `actual_cost > estimated_cost * 1.5`：标记成本偏离并进入人工复核候选
- `actual_cost > shot_budget_cap`：立即暂停该镜头推进
- `status=approved_for_edit` 且 `qc_status=pass`：禁止继续生成新 take，除非有人类批准
- 同一镜头已有 `selected_take`：默认禁止再次替换，除非填写 `change_reason`

---

## 10. 验收、回退与暂停规则

每次执行必须先判断是否达到最小可接受标准。

镜头级最小通过标准：

- 角色识别正确
- 画面主体明确
- 动作或情绪可读
- 时长可接入前后镜头
- 不存在明显破坏叙事的错误

### 10.1 硬失败与软失败

硬失败定义如下。出现任意一项时，当前 take 不得进入 `review` 之后的阶段：

- 主角色身份不可辨认
- 关键道具缺失或错误
- 场景与剧情设定冲突
- 时长无法承载对白
- 动作逻辑断裂到影响叙事理解
- 明显合规风险

软失败定义如下。出现时允许进入 `redo` 或低成本重试，但不得直接视为最终可交付：

- 表情细节一般
- 镜头运动略生硬
- 次要背景细节漂移
- 色调与目标风格轻微偏差
- 口型存在可后期修正的小偏差

### 10.2 重试上限

主执行 AI 必须按镜头类型控制最大重试次数：

- `dialogue`：最多 `3` 次
- `action`：最多 `5` 次
- `transition`：最多 `2` 次
- `reaction`：最多 `3` 次
- `insert`：最多 `3` 次

达到上限后的动作：

- `normal` 镜头：优先降级为更简单镜头语言或替换为过场镜头
- `high` 镜头：允许一次升级模型层级或补充参考图后再尝试
- `critical` 镜头：必须暂停并请求人工确认是否继续投入

### 10.3 验收判定规则

状态更新只按以下规则执行：

- 通过最低标准但未定稿：`status=review`
- 已定稿且可进入正式剪辑：`status=approved_for_edit` 且 `qc_status=pass`
- 不通过但可重做：`status=redo` 且 `qc_status=fail`
- 确认废弃：`status=dropped` 且 `qc_status=fail`

以下情况统一视为“必须停止当前推进并先处理问题”：

- 角色、场景、关键道具或叙事信息错误
- 对白时长与镜头结构不匹配
- 同一镜头连续失败超上限
- 单镜头成本超阈值
- 关键资产版本冲突
- 需要改动冻结后的剧情或镜头结构
- 出现版权、声线或商用风险

### 10.4 阶段级停止条件

主执行 AI 不得在当前里程碑仍存在阻塞项时推进到下一阶段。具体阻塞项以下一章里程碑定义为准。

### 10.5 强制人工确认点

主执行 AI 在以下节点必须请求人工确认：

- 剧本冻结前
- 资产冻结前
- Animatic 冻结前
- 首次使用高成本或新平台前
- 删除 `must_keep=true` 的镜头前
- 放弃 `critical` 镜头前
- 最终交付导出前

### 10.6 冻结点后的变更申请格式

冻结点之后，主执行 AI 不得直接改写主线、关键镜头、关键资产版本或已通过验收的结果。

以下情况统一必须先提变更申请：

- 改已冻结剧本
- 改已进入 `review` 或 `approved_for_edit` 的镜头结构
- 替换 `must_keep=true` 或 `priority=critical` 的镜头
- 更换主角色、主场景、关键道具版本
- 预算超阈值后继续高成本重试
- 更换正式工具链、平台或商用路径

变更申请最小字段如下：

- `change_id`
- `request_time`
- `requester`
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

所有变更申请必须追加记录到 `change_request_log.csv`，不得只保留在对话上下文中。

建议表头如下：

```csv
change_id,request_time,requester,phase,freeze_point,change_type,affected_files,affected_scene_ids,affected_shot_ids,reason,expected_impact,cost_impact,schedule_impact,risk_level,fallback_plan,approval_status,approved_by,approved_at
```

字段约束：

- `change_id` 建议格式为 `cr_YYYYMMDD_01`
- `freeze_point` 只能写 `script_freeze`、`asset_freeze`、`animatic_freeze`、`final_freeze`
- `change_type` 只能写 `script_change`、`shot_change`、`asset_change`、`budget_exception`、`toolchain_change`
- `affected_files` 使用 `|` 分隔
- `risk_level` 只能写 `low`、`medium`、`high`、`critical`
- `approval_status` 默认写 `pending`

推荐输出格式如下：

```text
变更申请
change_id: cr_20260318_01
request_time: 2026-03-18T19:10:00+08:00
requester: main_ai
phase: Animatic
freeze_point: animatic_freeze
change_type: shot_change
affected_files: shot_list.csv|take_log.csv
affected_scene_ids: sc03
affected_shot_ids: sc03_sh04|sc03_sh05
reason: 当前对白时长超出镜头承载能力，原结构无法成立
expected_impact: 需要将一个关键对话镜头拆成两个镜头，影响剪辑节奏与配音编号
cost_impact: 增加
schedule_impact: 可能延后半天
risk_level: medium
fallback_plan: 若不批准拆镜头，则改为旁白加反应镜头方案
approval_status: pending
```

未获批准前，主执行 AI 只能标记风险、暂停相关推进、准备替代方案和更新待确认项；不得覆盖已冻结事实源、批量删除旧记录、静默替换已入选 take，或把预算异常写成默认变动。

---

## 11. 成本控制规则

主执行 AI 必须在执行时持续跟踪预算，而不是只做事后统计。

### 11.1 必须预先设置的预算变量

在正式执行前，主执行 AI 必须先读取或要求提供以下预算变量：

- `project_budget_total`
- `planning_budget_cap`
- `asset_budget_cap`
- `animatic_budget_cap`
- `polish_budget_cap`
- `delivery_budget_cap`
- `shot_budget_cap`
- `critical_shot_budget_cap`

以上变量应统一记录在 `project_config.md` 中，不得只存在于临时对话或口头约定里。

建议最小配置块如下：

```text
project_name: ...
target_platform: ...
target_aspect_ratio: 9:16
target_duration_s: ...
project_budget_total: ...
planning_budget_cap: ...
asset_budget_cap: ...
animatic_budget_cap: ...
polish_budget_cap: ...
delivery_budget_cap: ...
shot_budget_cap: ...
critical_shot_budget_cap: ...
```

若任一变量缺失，主执行 AI 不得进入高成本执行阶段。

预算控制的具体触发阈值和超额后的自动动作，以下文 `11.2` 与 `11.4` 为准。

### 11.2 阈值化预算规则

主执行 AI 必须按如下规则执行预算控制：

- 当阶段累计成本达到对应 `stage_budget_cap` 的 `80%` 时，标记为预警状态
- 当阶段累计成本达到对应 `stage_budget_cap` 的 `100%` 时，禁止新增非关键高成本任务
- 当项目累计成本达到 `project_budget_total` 的 `90%` 时，仅允许推进 `high` 和 `critical` 项
- 当项目累计成本超过 `project_budget_total` 时，必须暂停并请求人工确认
- 当普通镜头成本超过 `shot_budget_cap` 时，不得继续升级模型层级
- 当关键镜头成本超过 `critical_shot_budget_cap` 时，必须触发人工复核

### 11.3 成本记录规则

主执行 AI 必须确保以下字段及时回写：

- 每次执行后更新 `take_log.csv.cost` 或 `take_log.csv.credits`
- 每轮镜头结算后更新 `shot_list.csv.actual_cost`
- 预算阶段应填写 `shot_list.csv.estimated_cost`
- 若成本偏差超过 `50%`，必须在 `shot_list.csv.change_reason` 或补充备注中说明原因

### 11.4 成本优化自动动作

当预算进入预警或超阈值状态时，主执行 AI 应按以下顺序自动优化：

1. 停止普通过场镜头的高质量重制
2. 优先复用已有背景和角色参考资产
3. 用静帧、推拉、反应镜头替代复杂动作镜头
4. 缩减同镜头额外 take 数量
5. 把非关键镜头回退到低成本链路
6. 若仍无法收敛，暂停并请求人工确认

---

## 12. 合规执行规则

主执行 AI 必须把合规检查当成生产步骤，而不是发布前附加动作。

至少在以下节点执行检查：

- 参考资产进入正式生产前
- 声音方案确定前
- 成片导出前

检查内容包括：

- 是否直接模仿知名 IP 角色外观
- 是否未经授权使用真人声音克隆
- 是否使用未经授权的人像或品牌元素
- 是否记录素材来源、模型版本、来源链接与条款时间
- 是否满足目标平台的 AI 披露要求

发现高风险项时：

- 当前任务暂停
- 标记风险类型
- 记录影响范围
- 提出替代方案
- 等待人类确认后继续

---

## 13. 交付规则

主执行 AI 只能交付已通过验收的成片版本。

最低交付物应包括：

- 主母版
- 平台发布版
- 字幕版
- 无字幕版
- 如有要求，横版与竖版分别导出

交付前必须核对：

- 分辨率
- 帧率
- 色彩空间
- 音频响度
- 字幕安全区
- 文件命名
- 合规标记
- `delivery_checklist.md`

---

## 14. AI 标准任务输出模板

建议主执行 AI 每轮任务都按固定格式输出：

- 当前阶段
- 本次目标
- 读取的输入文件
- 计划更新的文件
- 执行动作
- 验收标准
- 风险与待确认项
- 完成后回写内容
- 若失败则回退到的阶段或步骤

---

## 15. 推荐的主执行 AI 输出示例

```text
当前阶段：Animatic
本次目标：完成 sc02 对话镜头初版并确认对白时长可用
读取的输入文件：master_script.md, shot_list.csv, character_bible.md, style_bible.md
计划更新的文件：shot_list.csv, take_log.csv
执行动作：
1. 检查 sc02 对应镜头是否已具备角色和场景参考资产
2. 为每个镜头生成 2 个低成本 take
3. 将生成记录写入 take_log.csv
4. 选择可进入 review 的 take 并回写 shot_list.csv
验收标准：
- 角色一致性基本成立
- 对白时长与镜头长度可对齐
- 前后镜头可接入
风险与待确认项：
- sc02_sh04 缺少场景参考图版本号
- sc02_sh05 对白偏长，可能需要拆镜头
完成后回写内容：
- 更新 5 条 take_log 记录
- 更新 3 条 shot_list 状态为 review
- 更新 2 条 shot_list 状态为 redo
失败回退：
- 若连续失败超过 3 次，则回退到资产锁定阶段检查参考图
```
