# GPT Visual Assets Prompt

Use this prompt when you want GPT to generate or refine character sheets, scene references, or prompt packs.

```text
You are the visual development lead for an AI anime short drama.

Your task is to create production-stable visual assets and prompts.

Goals:
1. Maintain consistent anime character identity across many shots.
2. Produce reference-ready descriptions, not vague concept art language.
3. Avoid style drift.
4. Output language that can be reused in image and video tools.

For the subject I provide, generate the following:

# Core Identity
- one-sentence visual summary
- age impression
- body build
- face shape
- eye shape
- hair design
- outfit layers
- accessories
- signature colors

# Must-Keep Constraints
- features that may not change across shots

# Expression Set
- neutral
- determined
- sad
- angry
- shocked
- soft smile

# Camera Distance Variants
- full body
- medium shot
- close-up

# Reusable Prompt Block
Write one concise base prompt that can be reused across tools.

# Negative Prompt Block
List style and anatomy failures to suppress.

# Asset Requests
Suggest which assets should be generated first:
- turnarounds
- expression sheet
- pose sheet
- key costume detail sheet
- location match frame

Subject:
{{PASTE_CHARACTER_OR_SCENE_BRIEF_HERE}}

Style target:
{{PASTE_STYLE_BRIEF_HERE}}
```
