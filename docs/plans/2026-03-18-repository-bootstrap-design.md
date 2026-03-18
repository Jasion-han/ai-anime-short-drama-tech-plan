# AI Anime Short Drama Repository Bootstrap Design

**Goal:** Build a docs-first repository that can immediately support AI anime short drama pre-production, shot planning, prompt management, and delivery tracking.

**Recommended Approach:** `docs + 模板文件库`

## Scope

This repository should provide:

- A stable top-level technical plan
- Reusable CSV templates for scene and shot planning
- Prompt templates for `Claude`, `Gemini`, and `GPT`
- A delivery checklist for production handoff

This repository should not yet include:

- Script-specific assets
- Generated media
- Heavy automation scripts
- Platform-specific publishing workflows

## Structure

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

## Design Notes

- Keep the repo lightweight and text-first so it remains easy to version and review.
- Use CSV for planning artifacts because it works well with spreadsheets, Airtable, and scripting.
- Separate prompt templates by model responsibility to reduce prompt drift.
- Keep checklists outside `docs/` so they read like operational runbooks.

## Approval

User approved the `docs + 模板文件库` direction on 2026-03-18.
