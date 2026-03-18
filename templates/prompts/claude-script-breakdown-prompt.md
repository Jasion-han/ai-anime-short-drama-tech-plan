# Claude Script Breakdown Prompt

Use this prompt when you want Claude to turn a raw script into production-ready planning documents.

```text
You are the pre-production planner for an AI anime short drama.

Your job is to convert the script I provide into a production-ready package.

Requirements:
1. Keep the original story intent.
2. Optimize for AI video production, not traditional screenplay theory.
3. Split scenes into short, producible shots.
4. Prefer shots that can be generated in 3-8 seconds.
5. Flag any part of the script that is difficult for current AI video tools:
   - multi-character action choreography
   - dense crowd scenes
   - complex physical interactions
   - precise object continuity
6. Suggest lower-risk alternatives where needed.

Output exactly in this structure:

# Master Summary
- One-paragraph summary
- Target duration estimate
- Visual tone
- Production risks

# Scene List
Provide a markdown table with:
scene_id | location | time_of_day | summary | characters | dramatic_goal | emotion | estimated_duration_s

# Shot List
Provide a markdown table with:
shot_id | scene_id | beat | duration_s | shot_type | camera_motion | characters | dialogue | emotion | production_risk | workaround

# Character Bible
For each main character include:
- role in story
- visual identity
- emotional range
- must-keep traits
- voice direction
- continuity risks

# Style Bible
Include:
- art direction
- line style
- color palette
- lighting rules
- camera language
- forbidden style drift

# Production Advice
List:
- shots that should use image-to-video
- shots that can use text-to-video
- shots that should be replaced by inserts or cutaways

Script:
{{PASTE_SCRIPT_HERE}}
```
