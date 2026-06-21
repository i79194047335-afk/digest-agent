# Digest Agent — Claude

## STRICT RULES — NO EXCEPTIONS

### CRITICAL
Do NOT use built-in deep research tools. Follow ORCHESTRATOR.md only.

### Entry point
Always start with ORCHESTRATOR.md — it decides what to do next.

### Output files
- Quick pipeline: ../workspace/claude/quick_[topic].md
- Deep pipeline — planning: ../workspace/claude/research_plan.md
- Deep pipeline — research: ../workspace/claude/research_1.md, research_2.md, research_3.md
- Deep pipeline — evaluation: ../workspace/claude/eval_result.md
- Deep pipeline — final: ../workspace/claude/digest_final.md

### Lineage header (required in every output file)
Every output file must begin with a lineage block showing the full chain:
> Routed by: [chain of files that produced this output]
> Topic: [topic]

### Before each action
Write one line describing what you are about to do.
