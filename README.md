# AI Anime Short Drama Docs

本目录的使用顺序如下：

1. 先读 `ai-anime-short-drama-execution-spec.md`
2. 再读 `ai-anime-short-drama-tech-plan.md`

文档职责如下：

- `ai-anime-short-drama-execution-spec.md`
  用于执行。定义主执行 AI 的输入、输出、状态机、验收标准、暂停条件、预算与变更规则。

- `ai-anime-short-drama-tech-plan.md`
  用于理解方案。解释整体生产思路、模型与工具调度策略、成本逻辑、合规边界与落地原则。

执行约定如下：

- 若两份文档存在冲突，以 `ai-anime-short-drama-execution-spec.md` 为准。
- 主执行 AI 不应只读技术方案而跳过执行规范。
- 启动前应确认 `project_config.md` 和 `change_request_log.csv` 的记录方式。
