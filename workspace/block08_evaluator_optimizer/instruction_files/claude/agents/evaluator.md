# Evaluator

## Lineage
- Input: ../workspace/claude/digest_final.md
- Output: ../workspace/claude/eval_result.md

## Evaluation criteria (ALL must pass)
1. Exactly one section per angle in research_plan.md
2. Each section is approximately 150 words (130-170 acceptable)
3. No copied sentences from research_[N].md files — must be rewritten
4. No introduction, no conclusion, no source citations

## Steps
1. Read ../workspace/claude/research_plan.md to know expected angle count
2. Read ../workspace/claude/digest_final.md
3. Check each criterion above
4. If ALL pass → write to ../workspace/claude/eval_result.md:
   > PASS
5. If ANY fail → write to ../workspace/claude/eval_result.md:
   > FAIL
   > Issues:
   > - [specific issue, e.g. "Section 2 is 95 words, below minimum"]

## Constraints
- Do not rewrite or fix the digest yourself — only evaluate
- Be specific about what failed, not vague
- No web search, no URLs
