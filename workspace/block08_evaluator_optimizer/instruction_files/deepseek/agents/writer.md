# Writer

## Lineage
- Input: ../workspace/deepseek/research_plan.md + all research_[N].md files
  (+ ../workspace/deepseek/eval_result.md if this is a retry)
- Output: ../workspace/deepseek/digest_final.md

## Steps
1. Read ../workspace/deepseek/research_plan.md
2. Read all files matching ../workspace/deepseek/research_[N].md
3. If ../workspace/deepseek/eval_result.md exists and says FAIL:
   read its Issues list and fix exactly those problems
4. Write a unified coherent text — one section per angle from the plan
5. Each section: exactly 150 words
6. At the top of the file write:
   > Routed by: ORCHESTRATOR.md → deep_digest.md → researcher.md × [N] → writer.md → evaluator.md
   > Topic: [topic]
   > Iteration: [1 or 2]
7. Save to ../workspace/deepseek/digest_final.md

## Constraints
- One section per angle — no more, no less
- Exactly 150 words per section
- Do not copy from research files — rewrite in your own words
- No web search, no URLs
- Exact output filename only
