# AI Anime Short Drama Docs

本仓库分为两层：

- 根目录：治理层文档，用来定义执行规则
- `project/`：运行层目录，用来承载项目事实源、结构化表格和后续产出

同时存在两种使用口径：

- 项目试跑口径：默认口径。主执行 AI 直接读取根目录治理文档和 `project/` 运行文件
- 内部系统建设口径：开发内部生产系统时，额外参考 `docs/architecture/`。该口径下数据库是真相，`project/` 是镜像 / 导出层

默认读取顺序如下：

1. `README.md`
2. `ai-anime-short-drama-execution-spec.md`
3. `ai-anime-short-drama-tech-plan.md`
4. `project/` 下运行时文件

若文档存在冲突，以 `ai-anime-short-drama-execution-spec.md` 为准。

## Repo Layout

```text
.
├── README.md
├── ai-anime-short-drama-execution-spec.md
├── ai-anime-short-drama-tech-plan.md
└── project/
    ├── 00_docs/
    ├── 01_script/
    ├── 02_storyboard/
    ├── 03_characters/
    ├── 04_backgrounds/
    ├── 05_props/
    ├── 06_audio/
    ├── 07_video_shots/
    ├── 08_edit/
    ├── 09_exports/
    └── 10_qc/
```

规则如下：

- 运行时事实源只允许写入 `project/`
- 不要在仓库根目录新增 `master_script.md`、`scene_list.csv`、`shot_list.csv` 一类执行文件
- `project/` 下当前内容是 `demo draft`，用于试跑，不代表已冻结版本

## Runtime Map

核心运行文件按职责分组如下：

- 项目配置与约束
  - `project/00_docs/project_config.md`
  - `project/00_docs/character_bible.md`
  - `project/00_docs/style_bible.md`
  - `project/00_docs/prompt_library.md`
  - `project/00_docs/change_request_log.csv`
  - `project/00_docs/asset_provenance_log.csv`
  - `project/00_docs/delivery_checklist.md`

- 剧情与执行事实源
  - `project/01_script/master_script.md`
  - `project/01_script/scene_list.csv`
  - `project/01_script/shot_list.csv`
  - `project/01_script/take_log.csv`

- 产物目录
  - `project/02_storyboard/`
  - `project/03_characters/`
  - `project/04_backgrounds/`
  - `project/05_props/`
  - `project/06_audio/`
  - `project/07_video_shots/`
  - `project/08_edit/`
  - `project/09_exports/`
  - `project/10_qc/`

执行流转如下：

```text
master_script.md
  -> scene_list.csv
  -> shot_list.csv

character_bible.md / style_bible.md / prompt_library.md
  -> 约束 shot_list.csv 中的角色、风格和提示词

shot_list.csv
  -> 驱动镜头执行
  -> 生成记录写入 take_log.csv

take_log.csv
  -> 回写 selected_take / status / qc_status 到 shot_list.csv

asset_provenance_log.csv
  -> 记录资产来源、版本和商用留痕

change_request_log.csv
  -> 冻结后先登记变更，再修改事实源
```

关键理解如下：

- `master_script.md` 是剧情主事实源
- `shot_list.csv` 是执行中枢
- `take_log.csv` 是执行日志，不替代 `shot_list.csv`
- `change_request_log.csv` 是冻结后改动的闸门
- 正式产物应回收到 `project/` 对应目录

## Relationship To Architecture Docs

`docs/architecture/` 不是主执行 AI 试跑时的默认输入，而是“内部生产系统架构层”文档。

关系如下：

- `ai-anime-short-drama-tech-plan.md`：解释生产方法、工具链和成本逻辑
- `ai-anime-short-drama-execution-spec.md`：约束主执行 AI 的输入输出、状态机、schema 和暂停规则
- `docs/architecture/`：定义如果公司要把这套执行方式做成内部生产系统，系统应如何设计和实现

口径区别如下：

- 当前仓库试跑：AI 直接在 `project/` 中读写运行时事实源
- 未来内部系统：数据库是真相，`project/` 主要作为镜像、导出和审阅层

## Main Executor AI Startup Prompt

把下面这段作为主执行 AI 的启动指令使用：

```text
你是本项目的主执行 AI。

工作边界：
- 根目录文档是治理层，project/ 是唯一运行工作区
- 当前 project/ 下内容为 demo draft，可用于试跑，但默认未冻结
- 不得把运行时事实源写回仓库根目录
- docs/architecture/ 用于内部系统设计，不作为当前试跑轮次的默认执行输入

读取顺序：
1. README.md
2. ai-anime-short-drama-execution-spec.md
3. ai-anime-short-drama-tech-plan.md
4. project/00_docs/project_config.md
5. project/01_script/master_script.md
6. project/01_script/scene_list.csv
7. project/01_script/shot_list.csv
8. project/01_script/take_log.csv
9. project/00_docs/character_bible.md
10. project/00_docs/style_bible.md
11. project/00_docs/prompt_library.md
12. project/00_docs/change_request_log.csv
13. project/00_docs/asset_provenance_log.csv
14. project/00_docs/delivery_checklist.md

执行要求：
- 若规则冲突，以 ai-anime-short-drama-execution-spec.md 为准
- 严格遵守事实源优先级、状态机、schema、暂停条件和预算规则
- 未满足启动输入时，只允许输出待确认项和建议动作
- 冻结点后的修改必须先写 change_request_log.csv

每轮输出必须包含：
- 当前阶段
- 本次目标
- 读取的输入文件
- 计划更新的文件
- 执行动作
- 验收标准
- 风险与待确认项
- 完成后回写内容
- 失败回退

默认第一轮任务：
- 校验 master_script.md、scene_list.csv、shot_list.csv 是否一致
- 判断当前 demo draft 是否达到 M1 剧本冻结前的最小准备状态
- 输出缺失项、风险项和下一步建议
- 除非明确获批，否则不要直接进入高成本生产
```

## Recommended First Run

建议先让主执行 AI 执行这一轮：

```text
本轮目标：
完成启动检查，并判断当前项目是否可以从 Planning 推进到 M1 审核前状态。

本轮允许更新的文件：
- project/00_docs/project_config.md
- project/01_script/master_script.md
- project/01_script/scene_list.csv
- project/01_script/shot_list.csv

本轮禁止：
- 修改项目目标、预算上限、平台和画幅
- 跳过待确认项直接冻结
- 在根目录创建新的执行文件
```

## Notes

- `change_request_log.csv` 当前只有表头，表示还没有冻结点后的变更申请
- `take_log.csv` 当前只有表头，表示还没有进入正式镜头生成记录阶段
- 目录中的 `.gitkeep` 仅用于保留层级结构
