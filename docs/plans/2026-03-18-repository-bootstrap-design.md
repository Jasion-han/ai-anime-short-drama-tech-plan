# AI 动漫短剧仓库初始化设计

**目标：** 建立一个以文档和模板为核心的仓库，使其能够立即支持 AI 动漫短剧的前期策划、镜头规划、提示词管理和交付追踪。

**推荐方案：** `docs + 模板文件库`

## 范围

当前仓库应提供：

- 一份稳定的顶层技术方案
- 可复用的场次与镜头 `CSV` 模板
- 面向 `Claude`、`Gemini`、`GPT` 的提示词模板
- 一份用于生产交付的检查清单

当前仓库暂不包含：

- 某个具体剧本的专属资产
- 已生成的图片、视频、音频素材
- 较重的自动化脚本
- 面向具体平台的发布流程

## 结构

```text
.
├── README.md
├── ai-anime-short-drama-tech-plan.md
├── checklists/
│   └── delivery-checklist.md
├── docs/
│   └── plans/
│       └── 2026-03-18-repository-bootstrap-design.md
└── templates/
    ├── csv/
    │   ├── scene_list.csv
    │   └── shot_list.csv
    └── prompts/
        ├── claude-script-breakdown-prompt.md
        ├── gemini-multimodal-qc-prompt.md
        └── gpt-visual-assets-prompt.md
```

## 设计说明

- 仓库保持轻量、以文本为主，便于版本管理与审阅。
- 规划类资产使用 `CSV`，方便在表格、Airtable、脚本之间流转。
- 提示词模板按模型职责分开，减少提示词漂移。
- 检查清单放在 `docs/` 之外，更接近执行手册。

## 确认记录

用户已于 `2026-03-18` 确认采用 `docs + 模板文件库` 方向。
