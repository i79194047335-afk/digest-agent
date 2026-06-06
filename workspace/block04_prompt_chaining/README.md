# Block 04 — Prompt Chaining

## Pattern

Prompt chaining breaks a complex task into a fixed sequence of steps where the output of each step becomes the input for the next. Instead of asking the model to do everything at once, you guide it through discrete, verifiable stages — each with its own instructions and a concrete file artifact. This pattern is ideal when the task has a natural decomposition (plan → execute), when intermediate outputs need to be inspectable or reusable, and when strict format constraints must be enforced at each stage independently.

## What was built

A two-step digest pipeline implemented for two different models (Claude and DeepSeek). Given a topic, the agent first produces a structured 5-point plan, then uses that plan as its sole source to write a formatted digest. Both models share the same pipeline logic but have separate instruction sets and output directories. The DeepSeek variant adds an enforced `settings.json` to block web access at the harness level.

Key files:

- `instruction_files/claude/CLAUDE.md` — system rules, output path constraints, and pipeline definition for Claude
- `instruction_files/claude/step1_plan.md` — prompt for Step 1: produce a 5-point plan
- `instruction_files/claude/step2_write.md` — prompt for Step 2: write a digest from the plan
- `instruction_files/deepseek/CLAUDE.md` — same rules adapted for DeepSeek output paths
- `instruction_files/deepseek/settings.json` — harness-level permission deny + PreToolUse hook blocking WebFetch, WebSearch, curl, wget
- `instruction_files/deepseek/step1_plan.md` — Step 1 prompt for DeepSeek
- `instruction_files/deepseek/step2_write.md` — Step 2 prompt for DeepSeek
- `results/claude/plan.md` — 5-point plan produced by Claude
- `results/claude/digest.md` — final digest produced by Claude (5 sections, 150–200 words each)
- `results/deepseek/plan.md` — 5-point plan produced by DeepSeek
- `results/deepseek/digest.md` — final digest produced by DeepSeek (5 sections, 150–200 words each)

## How it works

1. The user provides a topic (e.g., "trading and financial markets — from forex to crypto").
2. The agent reads `step1_plan.md` and creates a plan with exactly 5 numbered points, saved to `results/<model>/plan.md`. No web access, no other output files permitted.
3. The agent reads `step2_write.md`, then reads the plan it just saved. It writes a digest with exactly 5 sections matching the plan's points, each 150–200 words, saved to `results/<model>/digest.md`. No extra sections, no introduction, no conclusion, no sources.
4. The pipeline stops. The two files — `plan.md` and `digest.md` — are the complete output.

## Key learnings

- **Intermediate file artifacts make chains inspectable.** Writing `plan.md` before `digest.md` means the plan can be reviewed or corrected between steps without re-running everything.
- **Instruction-level restrictions vs. harness-level restrictions behave differently.** Claude is instructed to avoid web search via CLAUDE.md alone. DeepSeek requires both CLAUDE.md instructions *and* a `settings.json` that enforces the ban at the tool-permission and hook layer — a stronger guarantee.
- **Strict format constraints must be specified in every step prompt.** Word counts, section counts, and prohibited content (introduction, conclusion, forecasts) are repeated in each step file, not just in the system prompt, because each step is a separate instruction context.
- **Same pipeline, divergent outputs.** Claude produced an English-language digest organized around market structure and asset classes. DeepSeek produced a Russian-language digest organized around analytical methodology and psychology. The topic was identical; the planning decomposition was not.
- **One-step-one-file discipline reduces hallucination surface.** Naming the exact output file in each step prompt (`../workspace/claude/plan.md`) prevents the model from inventing alternate paths or combining steps.

## Files in this block

| Filename | Purpose | Created by |
|---|---|---|
| `instruction_files/claude/CLAUDE.md` | System rules, output paths, pipeline order for Claude | Developer |
| `instruction_files/claude/step1_plan.md` | Step 1 prompt: produce a 5-point plan | Developer |
| `instruction_files/claude/step2_write.md` | Step 2 prompt: write digest from plan | Developer |
| `instruction_files/deepseek/CLAUDE.md` | System rules adapted for DeepSeek output paths | Developer |
| `instruction_files/deepseek/settings.json` | Harness-level web access block (deny + PreToolUse hook) | Developer |
| `instruction_files/deepseek/step1_plan.md` | Step 1 prompt for DeepSeek | Developer |
| `instruction_files/deepseek/step2_write.md` | Step 2 prompt for DeepSeek | Developer |
| `results/claude/plan.md` | 5-point digest plan (English) | Claude agent |
| `results/claude/digest.md` | Full digest, 5 sections × 150–200 words (English) | Claude agent |
| `results/deepseek/plan.md` | 5-point digest plan (Russian) | DeepSeek agent |
| `results/deepseek/digest.md` | Full digest, 5 sections × 150–200 words (Russian) | DeepSeek agent |

## Inherited from previous blocks

None. This block introduces the prompt chaining pattern from scratch with a self-contained instruction and results structure.
