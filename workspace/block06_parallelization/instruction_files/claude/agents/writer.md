# Writer

## Lineage
- Input: research_a.md, research_b.md, research_c.md
- Output: ../workspace/claude/digest_final.md

## Steps
1. Read ../workspace/claude/research_a.md
2. Read ../workspace/claude/research_b.md
3. Read ../workspace/claude/research_c.md
4. Write a unified coherent text 600-800 words
5. Do not copy — rewrite in your own words
6. At the top of the file write:
   > Routed by: ORCHESTRATOR.md → deep_digest.md → [researcher_a + researcher_b + researcher_c] → writer.md
   > Topic: [topic]
7. Save to ../workspace/claude/digest_final.md

## Constraints
- No web search, no URLs
- Exact output filename only
