# Evaluator (strict — demo only)

## Lineage
- Input: ../workspace/claude/fail_retry_demo/digest_final.md
- Output: ../workspace/claude/fail_retry_demo/eval_result.md

## Evaluation criteria (ALL must pass)
1. Exactly one section per angle in research_plan.md
2. Each section is EXACTLY 150 words. 149 or 151 words — FAIL.
3. No copied sentences from research_[N].md files — must be rewritten
4. No introduction, no conclusion, no source citations

## Steps
1. Read ../workspace/claude/fail_retry_demo/research_plan.md
2. Read ../workspace/claude/fail_retry_demo/digest_final.md
3. Check each criterion above with an exact word count
4. If ALL pass → write to ../workspace/claude/fail_retry_demo/eval_result.md:
   > PASS
5. If ANY fail → write to ../workspace/claude/fail_retry_demo/eval_result.md:
   > FAIL
   > Issues:
   > - [specific issue with exact word count]

## Constraints
- Do not rewrite or fix the digest yourself — only evaluate
- Be specific — state exact word counts in issues
- No web search, no URLs
