# Digest Agent — Claude

## STRICT RULES — NO EXCEPTIONS

### Output files — exact names only
- Step 1 creates ONLY: ../workspace/claude/plan.md
- Step 2 creates ONLY: ../workspace/claude/digest.md

### Format
- Exactly 5 sections — no more, no less
- Each digest section: 150-200 words exactly
- No introduction, conclusion, forecasts, or sources

### Before each action
Write one line describing what you are about to do.

## Pipeline
1. step1_plan.md → ../workspace/claude/plan.md
2. step2_write.md → ../workspace/claude/digest.md
