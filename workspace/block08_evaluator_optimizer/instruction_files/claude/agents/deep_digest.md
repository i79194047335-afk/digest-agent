# Deep Digest Pipeline (with evaluator-optimizer loop)

## Lineage
- Input: ../workspace/claude/research_plan.md (written by ORCHESTRATOR.md)
- Calls: researcher.md × N → writer.md → evaluator.md → (writer.md retry if FAIL)
- Output: ../workspace/claude/digest_final.md

## Steps
1. Read ../workspace/claude/research_plan.md
2. For EACH angle in the plan, run agents/researcher.md in parallel
   Number outputs: research_1.md, research_2.md, research_3.md
3. Run agents/writer.md (iteration 1)
4. Run agents/evaluator.md
5. Read ../workspace/claude/eval_result.md:
   - If PASS → done, final output is digest_final.md
   - If FAIL and iteration < 2 → run agents/writer.md again (iteration 2), then agents/evaluator.md again
   - If FAIL after iteration 2 → stop, report final eval_result.md as-is

## Constraints
- Maximum 2 writer iterations total
- Maximum 1 evaluator call per writer iteration
- No web search, no URLs
- Do not invent angles — use only what ORCHESTRATOR.md wrote in the plan
