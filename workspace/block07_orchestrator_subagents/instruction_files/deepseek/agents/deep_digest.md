# Deep Digest Pipeline (dynamic researchers)

## Lineage
- Input: ../workspace/deepseek/research_plan.md (written by ORCHESTRATOR.md)
- Calls: agents/researcher.md once per angle — then agents/writer.md
- Output: ../workspace/deepseek/digest_final.md

## Steps
1. Read ../workspace/deepseek/research_plan.md
2. For EACH angle listed in the plan, run agents/researcher.md
   - Pass the angle name and its description as the assignment
   - Number the outputs: research_1.md, research_2.md, research_3.md
   - Run these calls in parallel
3. Wait until one research_[N].md file exists for every angle in the plan
4. Run agents/writer.md
5. Final output: ../workspace/deepseek/digest_final.md

## Constraints
- No web search, no URLs
- Run exactly as many researchers as there are angles in the plan (max 3)
- Do not invent angles — use only what ORCHESTRATOR.md wrote in the plan