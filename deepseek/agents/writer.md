# Writer

## Lineage
- Input: ../workspace/deepseek/research_plan.md + all research_[N].md files
- Output: ../workspace/deepseek/digest_final.md

## Steps
1. Read ../workspace/deepseek/research_plan.md
2. Read all files matching ../workspace/deepseek/research_[N].md
3. Write a unified coherent text — one section per angle from the plan
4. Each section: exactly 150 words
5. At the top of the file write:
   > Routed by: ORCHESTRATOR.md → deep_digest.md → researcher.md × [N] → writer.md
   > Topic: [topic]
6. Save to ../workspace/deepseek/digest_final.md

## Constraints
- One section per angle — no more, no less
- Exactly 150 words per section
- Do not copy from research files — rewrite in your own words
- No introduction, no conclusion, no sources
- No web search, no URLs
- Exact output filename only