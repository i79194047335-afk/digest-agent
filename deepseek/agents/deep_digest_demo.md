# Deep Digest Pipeline (fail-retry demo, strict evaluator)

## Lineage
- Input: ../workspace/deepseek/fail_retry_demo/research_plan.md
- Calls: researcher.md × N → writer.md → evaluator_strict.md → (writer.md retry if FAIL)
- Output: ../workspace/deepseek/fail_retry_demo/digest_final.md

## Steps
1. Write research plan to ../workspace/deepseek/fail_retry_demo/research_plan.md
2. For EACH angle, run agents/researcher.md
   Save outputs to ../workspace/deepseek/fail_retry_demo/research_1.md, research_2.md, research_3.md
3. Run agents/writer.md — but write output to ../workspace/deepseek/fail_retry_demo/digest_final.md
   Before saving, copy current digest_final.md to digest_final_v[iteration].md if it exists
4. Run agents/evaluator_strict.md
5. Copy eval_result.md to eval_result_v[iteration].md (keep a numbered snapshot each time)
6. If FAIL and iteration < 2 → run writer again (iteration 2), then evaluator_strict.md again
7. If PASS → stop

## Constraints
- Maximum 2 writer iterations
- Keep ALL versions: digest_final_v1.md, digest_final_v2.md, eval_result_v1.md, eval_result_v2.md
- No web search, no URLs
