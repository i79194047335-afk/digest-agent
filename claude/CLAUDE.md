# Digest Agent — Claude

## STRICT RULES — NO EXCEPTIONS

### Entry point
Always start with ORCHESTRATOR.md — it decides what to do next.

### Output files
- Quick pipeline: ../workspace/claude/quick_[topic].md
- Deep pipeline: ../workspace/claude/digest_final.md
- Intermediate: ../workspace/claude/research_a.md, ../workspace/claude/research_b.md, ../workspace/claude/research_c.md

### Lineage header (required in every output file)
Every output file must begin with a lineage block showing the full chain:
> Routed by: [chain of files that produced this output]
> Topic: [topic]

### Before each action
Write one line describing what you are about to do.
