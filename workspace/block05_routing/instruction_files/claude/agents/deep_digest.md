# Deep Digest Pipeline

## Lineage
- Input: topic from user request (passed by ORCHESTRATOR.md)
- Output: ../workspace/claude/deep_[topic].md

## Steps
1. Execute step1_plan.md → ../workspace/claude/plan.md
2. Execute step2_write.md → ../workspace/claude/digest.md
3. Read ../workspace/claude/digest.md
4. Add section "What this means in practice" (100-150 words)
5. At the top of the output file write:
   > Routed by: ORCHESTRATOR.md → deep_digest.md → step1_plan.md → step2_write.md
   > Topic: [topic]
6. Save final result to ../workspace/claude/deep_[topic].md

## Constraints
- 800-1000 words total
- No web search, no URL requests
- Exact output filename only
