# AI 动漫短剧技术方案

更新日期：2026-03-18

## 1. 目标定义

本方案面向通过 `Claude / Gemini / GPT` 等 AI 工具协同完成一部 AI 动漫短剧的完整生产流程，目标是提供一套可落地、可复用、可控成本、可规模化返工的技术路线。

核心原则不是“把整部剧直接交给一个模型生成”，而是采用工程化流水线：

```text
剧本结构化 -> 角色/场景资产锁定 -> 分镜级镜头生产 -> 配音/音效 -> 后期剪辑合成 -> 质检返工
```

适用范围：

- 个人创作者
- 2 到 5 人的小团队
- 短视频平台竖屏短剧
- 横屏叙事短片
- 以二次元、日漫、anime look 为主的风格化内容

---

## 2. 总体制作思路

AI 动漫短剧最稳的制作方式是多模型分层协作，而不是单模型端到端生成。

建议将工具链拆成 5 层：

| 层级 | 作用 | 推荐主力 |
|---|---|---|
| 文本中台 | 拆剧本、写分镜、角色设定、统一版本、生成提示词 | Claude / Gemini / GPT |
| 视觉设定 | 角色立绘、表情包、场景图、道具图、风格锁定 | GPT Image / Imagen / Runway References |
| 视频生成 | 将关键帧或参考图生成镜头 | Flow + Veo / Runway / Sora |
| 语音音频 | 配音、旁白、口播、多语种版本 | ElevenLabs / Gemini TTS |
| 后期装配 | 剪辑、字幕、混音、转场、导出 | DaVinci Resolve / Premiere / CapCut |

默认推荐的协作方式：

- `Claude` 负责剧本拆解、场次整理、人物关系、情绪弧线、对白润色
- `Gemini` 负责多模态审查、看图纠错、镜头连续性检查、粗剪分析
- `GPT` 负责提示词压缩、视觉资产生成、图像局部编辑、风格统一
- `Flow + Veo / Runway / Sora` 负责视频镜头生成
- `ElevenLabs` 负责角色声线设计与配音
- `DaVinci Resolve` 负责最终成片

---

## 3. 关键生产原则

### 3.1 按镜头生产，不按整场生成

每个镜头建议控制在 `3-8 秒`：

- 对话镜头：`3-5 秒`
- 动作镜头：`4-8 秒`
- 过场镜头：`2-4 秒`

不要直接生成 20 秒以上的复杂段落，否则容易出现：

- 角色漂移
- 构图失控
- 动作错乱
- 口型和对白无法对齐
- 成本快速上升

### 3.2 动漫短剧优先采用“图生视频”

对于对话、情绪、特写镜头，推荐先产出：

- 角色参考图
- 表情图
- 场景图
- 构图参考图

再用视频模型做轻动作、镜头运动和表情变化。  
这比直接用纯文生视频更稳，尤其适合动漫风格。

### 3.3 先做 Animatic，再做 Final

推荐先做一版低成本粗剪：

- 用占位图
- 用低成本视频模型
- 用临时配音
- 用占位音效

先验证：

- 节奏是否成立
- 镜头顺序是否顺
- 对白时长是否匹配
- 情绪推进是否有效

确认后，再升级关键镜头质量。

### 3.4 所有改动只维护一个主版本

无论你用多少 AI 工具，必须只保留一个：

- `master_script.md`

所有场次、镜头、提示词、配音、字幕都以它为唯一来源，否则后面会出现：

- 台词版本冲突
- 分镜与对白不一致
- 字幕时间轴失效
- 配音返工成本增加

---

## 4. 项目标准文件结构

建议建立如下目录：

```text
project/
  00_docs/
  01_script/
    master_script.md
    scene_list.csv
    shot_list.csv
  02_storyboard/
  03_characters/
    hero/
    heroine/
    villain/
  04_backgrounds/
  05_props/
  06_audio/
    voices/
    dialogue/
    sfx/
    music/
  07_video_shots/
    sc01/
    sc02/
  08_edit/
  09_exports/
  10_qc/
```

建议同时维护以下文档：

- `master_script.md`
- `scene_list.csv`
- `shot_list.csv`
- `character_bible.md`
- `style_bible.md`
- `prompt_library.md`
- `asset_provenance_log.csv`
- `delivery_checklist.md`

---

## 5. 前期策划阶段

### 5.1 开工前必须锁定的 4 个决策

1. 成片长度
2. 画幅规格
3. 视觉风格
4. 配音方案

参考：

- 短视频平台优先：`9:16`
- 横屏叙事优先：`16:9`
- 时长建议：
  - 爆款短内容：`45-90 秒`
  - 完整剧情短片：`2-5 分钟`

### 5.2 剧本结构化拆解

用 `Claude` 或 `GPT` 将剧本拆成：

- 场次
- 情绪节点
- 冲突点
- 视觉重点
- 镜头需求
- 对白时长预估

产出物建议如下。

#### `scene_list.csv`

至少包含：

```csv
scene_id,location,time_of_day,summary,characters,dramatic_goal,emotion,estimated_duration_s
```

#### `shot_list.csv`

至少包含：

```csv
shot_id,scene_id,beat,duration_s,shot_type,camera_motion,characters,dialogue,emotion,bg_ref,char_ref,prompt,status,model,seed,take,final_path
```

#### `character_bible.md`

每个角色建议写清：

- 姓名
- 年龄感
- 性格关键词
- 服装设定
- 发型与发色
- 眼型
- 体型
- 色彩主调
- 常用表情
- 声线特征
- 禁止偏离项

#### `style_bible.md`

至少包含：

- 目标风格
- 线条粗细
- 上色方式
- 光影逻辑
- 镜头语言
- 色板
- 背景复杂度
- 人物与场景融合关系
- 禁止元素

---

## 6. 美术资产阶段

这一阶段不要急着做视频，先锁定可复用资产。

### 6.1 角色资产

每个主角建议准备：

- `8-12` 张角色参考图
- `6-10` 组表情变化
- 至少 `3` 个镜头距离版本：
  - 全身
  - 半身
  - 近景

建议固定的维度：

- 发型
- 发色
- 服装层级
- 饰品
- 年龄感
- 身高比例
- 眼型
- 肤色
- 主色系

### 6.2 场景资产

每个主场景建议准备：

- `3-6` 张环境图
- 日景 / 夜景版本
- 空镜版本
- 带角色站位预构图版本

重点包括：

- 教室
- 屋顶
- 街道
- 卧室
- 医院
- 地铁
- 公园
- 幻想空间

### 6.3 道具资产

关键道具应独立出图，例如：

- 手机
- 项链
- 武器
- 书本
- 雨伞
- 校徽
- 信件

原因是关键道具在镜头里常常承担叙事信息，不能每次随机生成。

---

## 7. 分镜与镜头生产方案

推荐把镜头分成 3 类处理。

### 7.1 对话镜头

适合：

- 站桩对话
- 情绪戏
- 心理戏
- 特写戏

推荐流程：

```text
角色/场景参考图 -> 构图图 -> 轻动作视频 -> 单独配音 -> 口型同步 -> 合成
```

优势：

- 人物稳定
- 表情更可控
- 返工更低
- 更适合二次元风格

### 7.2 动作镜头

适合：

- 奔跑
- 追逐
- 转身
- 打斗
- 爆发
- 跳跃
- 镜头绕拍

建议：

- 每个动作拆成短镜头
- 不要一次生成多角色复杂交互
- 高动作戏优先做参考帧
- 多做几个 take 再筛选

### 7.3 过场镜头

这是提升完成度的重要手段，用于连接、遮丑、补节奏。

常见类型：

- 天空
- 城市
- 校园走廊
- 风吹窗帘
- 手部特写
- 门把手
- 手机屏幕
- 鞋子停下
- 角色回头

很多 AI 动漫片的流畅感，其实依赖的是过场镜头安排得足够聪明。

---

## 8. 推荐的 AI 分工方式

### 8.1 Claude 的职责

适合处理：

- 长剧本拆解
- 场次结构整理
- 人物关系梳理
- 情绪曲线分析
- 对白重写
- 镜头表初稿生成

### 8.2 Gemini 的职责

适合处理：

- 多模态看图审查
- 审查角色一致性
- 分析镜头顺序
- 检查场景连续性
- 对粗剪进行“看片式”反馈

### 8.3 GPT 的职责

适合处理：

- 视觉提示词压缩
- 角色图和场景图生成
- 局部图像修改
- 提示词模板维护
- 风格统一

### 8.4 最优协作链

建议工作链路如下：

```text
Claude 出结构 -> GPT 出视觉资产 -> Gemini 审图/审片 -> 人工定稿
```

不建议让多个模型同时持续修改剧本主版本。

---

## 9. 视频生成技术策略

### 9.1 工具选型建议

#### 方案 A：质量优先

- 文本：`Claude Sonnet / Opus`
- 图像：`GPT Image`
- 视频：`Flow + Veo 3.1`
- 配音：`ElevenLabs`
- 后期：`DaVinci Resolve`

适合：

- 追求镜头质感
- 需要更强电影感
- 有一定预算

#### 方案 B：成本优先

- 文本：`Gemini 2.5 Pro + GPT`
- 图像：`Gemini / GPT Image`
- 视频：`Runway Gen-4 Turbo`
- 配音：`ElevenLabs / Gemini TTS`
- 后期：`CapCut / Resolve`

适合：

- 快速试错
- 先做 MVP
- 预算有限

#### 方案 C：OpenAI 生态优先

- 文本：`GPT`
- 图像：`GPT Image`
- 视频：`Sora 2`
- 配音：`ElevenLabs`
- 后期：`Resolve`

适合：

- 已有 OpenAI 使用习惯
- 已具备 Sora 权限

但要注意：

- 地区和权限可能有限制
- 多人复杂戏的稳定性仍需保守拆镜

### 9.2 镜头生成参数建议

每个镜头记录这些参数：

- 模型
- 版本
- Prompt
- Negative Prompt
- Seed
- Duration
- Aspect Ratio
- 参考图 ID
- Take 编号
- 是否入选

建议对每个镜头至少保留：

- `take_01`
- `take_02`
- `selected_take`

---

## 10. 声音与配音方案

声音质量直接决定成片完成度。

### 10.1 推荐的音频流程

```text
角色声线设计 -> 逐句对白生成/录制 -> 口型同步 -> SFX -> BGM -> 混音
```

### 10.2 音频工程规范

建议统一：

- 采样率：`48kHz`
- 主项目音频格式：`WAV`
- 每句对白单独编号
- 字幕时间轴与对白编号一一对应

### 10.3 为什么不要依赖视频模型的最终对白

原因：

- 改词困难
- 情绪不稳定
- 语速不可控
- 多语言版本复用差
- 后期精修空间有限

因此建议：

- 最终对白统一走独立音轨
- 口型作为后续匹配工序
- 便于多版本导出

---

## 11. 后期剪辑与合成

推荐使用：

- `DaVinci Resolve`
- `Premiere Pro`
- `CapCut`

### 11.1 后期流程建议

```text
导入镜头 -> 按 shot_id 排列 -> 铺对白 -> 对齐动作 -> 补空镜 -> 上字幕 -> 加 SFX -> 铺 BGM -> 调色统一 -> 导出
```

### 11.2 后期重点

- 统一镜头节奏
- 用空镜和反应镜头遮蔽镜头接缝
- 统一调色减少模型差异
- 字幕统一字重、描边、位置
- 保证对白优先级高于音乐

### 11.3 推荐导出版本

- `Master 1080p`
- `9:16 发布版`
- `16:9 横版`
- `Clean 无字幕版`
- `Caption 带字幕版`

---

## 12. 成本控制方案

AI 动漫项目中，预算大头通常是视频生成，不是 LLM 文本。

### 12.1 预算结构参考

- 文本规划：小于总预算的 `5%`
- 图像资产：约 `10%-20%`
- 视频生成：约 `60%-80%`
- 配音与后期：约 `10%-20%`

### 12.2 粗估方法

例如一部 `3 分钟` 短剧：

- 假设 `60` 个镜头
- 平均每个镜头 `5` 秒
- 总成片约 `300` 秒
- 每个镜头先跑 `2` 个 take

则总生成量约：

```text
60 x 5 x 2 = 600 秒生成素材
```

### 12.3 成本优化原则

- 粗剪用低价模型
- 关键镜头再升高价模型
- 不要全片都用最高质量模型
- 先验证对白和节奏，再升级画面
- 多做静帧和过场，减少高成本动作镜头占比

---

## 13. 最容易翻车的 10 个问题

1. 角色脸漂  
   解决：使用固定 reference pack，统一角色设定图

2. 服装漂移  
   解决：服装写成硬约束，不要模糊描述

3. 场景不一致  
   解决：主场景先建背景资产，不临时随机生成

4. 对白节奏不对  
   解决：先做音频 animatic，再做镜头

5. 口型不自然  
   解决：最终口型单独处理，不依赖随机开口

6. 打斗画面崩坏  
   解决：动作戏拆短镜头，降低复杂交互

7. 成本失控  
   解决：先低价试错，再高价升镜头

8. 剧情节奏拖沓  
   解决：短剧保持高信息密度，减少冗长停顿

9. 整片风格不统一  
   解决：提前建立 `style_bible.md` 和 `prompt_library.md`

10. 法务风险  
    解决：避免直接模仿已有知名 IP、声线、品牌或角色形象

---

## 14. 合规与版权要求

正式发布前，建议重点检查：

- 是否直接模仿某知名动漫 IP 的角色设计
- 是否未经授权使用真人声音克隆
- 是否使用未经授权的人像作为角色参考
- 是否保留素材来源与模型版本记录
- 是否包含平台要求披露的 AI 生成标记

建议维护：

- `asset_provenance_log.csv`

至少记录：

- 资产 ID
- 生成日期
- 工具名称
- 模型版本
- Prompt
- Seed
- 参考图来源
- 使用范围
- 是否商用

---

## 15. 标准执行流程

建议按如下顺序开工：

1. 将剧本整理成纯文本版
2. 生成 `scene list`
3. 生成 `shot list`
4. 建立 `character bible`
5. 建立 `style bible`
6. 先做角色和场景资产
7. 生成低成本 animatic
8. 锁节奏后重做关键镜头
9. 统一配音、音效、字幕
10. 后期剪辑、混音、导出

---

## 16. 推荐的实际落地打法

如果你想以最低风险开始，建议采用下面这套默认打法：

### 阶段一：策划

- 用 `Claude` 拆剧本
- 产出 `scene_list.csv` 和 `shot_list.csv`
- 人工确认节奏和时长

### 阶段二：美术锁定

- 用 `GPT` 或其他图像模型生成角色设定图
- 固定表情、服装、发型、颜色
- 制作主场景背景图

### 阶段三：粗剪

- 用低成本模型跑关键镜头草稿
- 临时配音
- 形成第一版 animatic

### 阶段四：精修

- 升级关键镜头模型质量
- 重做动作镜头
- 统一口型和声音

### 阶段五：成片

- 加字幕
- 加音效
- 混音
- 调色
- 导出多个平台版本

---

## 17. 你现在可以直接开始的动作

如果现在就开工，建议立刻做这 3 件事：

1. 把剧本整理成可复制文本
2. 明确成片时长、画幅、风格
3. 建立 `master_script.md / scene_list.csv / shot_list.csv`

接下来就可以把项目送入生产链路。

---

## 18. 参考来源

- Anthropic 模型总览  
  https://platform.claude.com/docs/en/about-claude/models/overview

- Gemini 模型总览  
  https://ai.google.dev/gemini-api/docs/models

- Gemini 定价  
  https://ai.google.dev/gemini-api/docs/pricing

- Google Flow 官方介绍  
  https://blog.google/innovation-and-ai/products/google-flow-veo-ai-filmmaking-tool/

- Veo 3 扩展说明  
  https://blog.google/products-and-platforms/products/gemini/veo-3-expansion-mobile/

- OpenAI 图像生成文档  
  https://developers.openai.com/api/docs/guides/image-generation

- OpenAI Sora 2  
  https://openai.com/index/sora-2/

- Sora 使用说明  
  https://help.openai.com/en/articles/12456897-getting-started-with-the-sora-app

- Runway Gen-4 视频说明  
  https://help.runwayml.com/hc/en-us/articles/37327109429011-Creating-with-Gen-4-Video

- Runway API 定价  
  https://docs.dev.runwayml.com/guides/pricing/

- ElevenLabs Voice Design  
  https://elevenlabs.io/docs/eleven-creative/voices/voice-design

- ElevenLabs Dubbing  
  https://elevenlabs.io/docs/eleven-creative/services/productions/dubbing

