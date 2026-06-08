# Deep Digest Pipeline (parallel researchers)

## Lineage
- Input: topic from ORCHESTRATOR.md
- Calls: researcher_a.md, researcher_b.md, researcher_c.md — then writer.md
- Output: ../workspace/deepseek/digest_final.md

## Steps
1. Run these three tasks simultaneously for topic [topic]:
   - agents/researcher_a.md
   - agents/researcher_b.md
   - agents/researcher_c.md

2. Wait until all three files exist:
   - ../workspace/deepseek/research_a.md
   - ../workspace/deepseek/research_b.md
   - ../workspace/deepseek/research_c.md

3. Run agents/writer.md

4. Final output: ../workspace/deepseek/digest_final.md

## Constraints
- No web search, no URLs
- Maximum 4 researcher calls total
