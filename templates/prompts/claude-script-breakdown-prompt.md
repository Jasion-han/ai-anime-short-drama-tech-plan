# Claude 剧本拆解提示词

当你需要让 Claude 把原始剧本拆成可生产文档时，使用这份提示词。

```text
你是一个 AI 动漫短剧的前期策划师。

你的任务是把我提供的剧本，转换为可直接进入生产的策划包。

要求：
1. 保留原始故事意图。
2. 以 AI 视频生产为优先，而不是传统编剧理论。
3. 将场次拆成短小、可执行的镜头。
4. 优先设计能在 3-8 秒内生成完成的镜头。
5. 明确标出当前 AI 视频工具难以稳定完成的内容，例如：
   - 多角色复杂动作编排
   - 密集群像
   - 高复杂物理交互
   - 高精度道具连续性
6. 对高风险内容提出低风险替代方案。

严格按以下结构输出：

# 总览
- 一段摘要
- 目标时长预估
- 视觉基调
- 生产风险

# 场次表
使用 Markdown 表格输出：
scene_id | location | time_of_day | summary | characters | dramatic_goal | emotion | estimated_duration_s

# 镜头表
使用 Markdown 表格输出：
shot_id | scene_id | beat | duration_s | shot_type | camera_motion | characters | dialogue | emotion | production_risk | workaround

# 角色设定文档
每个主角包含：
- 剧情职能
- 视觉身份
- 情绪区间
- 必须保持不变的特征
- 声音方向
- 连续性风险

# 风格设定文档
包括：
- 美术方向
- 线条风格
- 色彩方案
- 光影规则
- 镜头语言
- 必须避免的风格漂移

# 制作建议
列出：
- 哪些镜头适合 image-to-video
- 哪些镜头可以直接 text-to-video
- 哪些镜头更适合改成插入镜头或过场镜头

剧本：
{{PASTE_SCRIPT_HERE}}
```
