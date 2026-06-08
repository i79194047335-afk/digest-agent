# Digest Agent — DeepSeek

## STRICT RULES — NO EXCEPTIONS

### Entry point
Always start with ORCHESTRATOR.md — it decides what to do next.

### Output files
- Quick pipeline: ../workspace/deepseek/quick_[topic].md
- Deep pipeline: ../workspace/deepseek/deep_[topic].md
- Intermediate: ../workspace/deepseek/plan.md, ../workspace/deepseek/digest.md

### Lineage header (required in every output file)
Every output file must begin with a lineage block showing the full chain:
> Routed by: [chain of files that produced this output]
> Topic: [topic]

### Before each action
Write one line describing what you are about to do.
