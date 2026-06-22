# Block 8 — Evaluator-Optimizer

## Pattern

The **evaluator-optimizer** pattern pairs a *generator* agent with a separate *evaluator* agent in a feedback loop. The generator produces output; the evaluator checks it against explicit, objective criteria and emits a verdict (`PASS` / `FAIL` with specific issues). If the verdict is `FAIL`, the generator runs again — but this time it reads the evaluator's issue list and fixes *exactly* those problems, rather than rewriting blindly. The loop repeats up to a bounded number of iterations. Use this pattern when "good enough" can be expressed as checkable rules (length, structure, formatting, originality) and when a single pass cannot reliably hit them. The key insight is the *separation of roles*: the evaluator never fixes the work, and the generator never judges its own work — each does one job well, and the bounded loop converges output toward the spec.

## What was built

This block extends the Block 7 deep-digest pipeline (orchestrator + parallel researchers + writer) by inserting an **evaluator** agent and a **retry loop** after the writer. Two parallel implementations were built — one driven by Claude instruction files, one by DeepSeek instruction files — and each was run against the same topic (*decentralized finance*) so the loop's behavior could be compared across models.

The block contains two scenarios:

1. **Main pipeline** (`instruction_files/` + `results/`) — the production loop with a *lenient* evaluator (sections of ~150 words, 130–170 acceptable). Both models passed on the first iteration, so no retry was triggered.
2. **fail_retry_demo** (`fail_retry_demo/`) — the same loop wired to a *strict* evaluator that demands **exactly 150 words** per section (149 or 151 = FAIL). This deliberately forces the FAIL→retry cycle so the optimizer half of the pattern can be observed. Every intermediate version is preserved as a numbered snapshot.

Key files:

- `instruction_files/{claude,deepseek}/agents/deep_digest.md` — the orchestrating pipeline that runs researchers → writer → evaluator and decides whether to retry.
- `instruction_files/{claude,deepseek}/agents/writer.md` — the generator; on a retry it reads `eval_result.md` and fixes only the listed issues.
- `instruction_files/{claude,deepseek}/agents/evaluator.md` — the lenient evaluator (criteria: one section per angle, ~150 words, no copied sentences, no intro/conclusion/citations).
- `fail_retry_demo/instruction_files/{claude,deepseek}/agents/evaluator_strict.md` — the strict evaluator (exactly 150 words) used only in the demo.
- `fail_retry_demo/instruction_files/{claude,deepseek}/agents/deep_digest_demo.md` — demo pipeline that snapshots every iteration (`digest_final_v1.md`, `eval_result_v1.md`, …).
- `results/` and `fail_retry_demo/results/` — the actual generated digests and evaluation verdicts for each model.

## How it works

1. **Plan in.** The pipeline reads `research_plan.md` (written upstream by the orchestrator), which lists the angles to cover — here three: *Core Protocols*, *Governance & Tokenomics*, *Risks & Challenges*.
2. **Research.** One researcher agent runs per angle, in parallel, writing `research_1.md`, `research_2.md`, `research_3.md`.
3. **Write (iteration 1).** The writer reads the plan and all research files and produces `digest_final.md` — one section per angle, targeting 150 words each, rewritten in its own words (no copying), with a lineage header recording the route and iteration number.
4. **Evaluate.** The evaluator reads the plan (to learn the expected angle count) and the digest, checks every criterion, and writes a verdict to `eval_result.md`: either `PASS`, or `FAIL` followed by a specific issue list (e.g. *"Section 2: 161 words. Expected exactly 150."*).
5. **Decide.**
   - `PASS` → done; `digest_final.md` is the final output.
   - `FAIL` and iteration < 2 → the writer runs again (iteration 2), this time reading the issue list and fixing only those problems, then the evaluator runs once more.
   - `FAIL` after iteration 2 → stop and report the last `eval_result.md` as-is.
6. **Bounds.** At most **2 writer iterations** and **1 evaluator call per iteration**. In the demo pipeline, each iteration's digest and verdict are copied to numbered snapshots (`_v1`, `_v2`) before being overwritten, so the full history is auditable.

## Observed runs

**Main pipeline (lenient evaluator) — PASS on first iteration for both models.**

| Model | Iteration 1 verdict | Retry? | Final |
|-------|--------------------|--------|-------|
| Claude | PASS | No | `results/claude/digest_final.md` (Iteration 1) |
| DeepSeek | PASS | No | `results/deepseek/digest_final.md` (Iteration 1) |

With the ±20-word tolerance, both writers hit the target on the first try, so the loop short-circuited and the optimizer never fired. This is the happy path: the evaluator's job was simply to confirm quality.

**fail_retry_demo (strict evaluator, exactly 150 words) — the FAIL→retry cycle.**

| Model | Iteration 1 | Iteration 2 | Final |
|-------|-------------|-------------|-------|
| Claude | **PASS** (`eval_result_v1.md`) | not needed | passed first try |
| DeepSeek | **FAIL** (`eval_result_v1.md`): sections of 161, 153, 156 words | **PASS** (`eval_result_v2.md`) | corrected on iteration 2 |

- **Claude** hit exactly 150 words on every section the first time, so the strict evaluator returned `PASS` immediately — only one snapshot (`_v1`) exists.
- **DeepSeek** overshot on all three sections (161 / 153 / 156 words). The strict evaluator returned `FAIL` with the exact counts. The writer then ran a second time, read that issue list, trimmed each section to exactly 150 words, and the evaluator returned `PASS` on iteration 2. Both `digest_final_v1.md` (the failing draft) and `digest_final_v2.md` (the corrected version, also promoted to `digest_final.md`) are preserved, alongside both numbered verdicts.

This contrast is the whole point of the demo: under identical instructions, one model satisfied a hard constraint immediately while the other needed the corrective loop — and the loop did its job, converging to PASS within the iteration budget.

## Key learnings

- **Separation of roles is what makes the loop work.** The evaluator only judges and the writer only writes; because the evaluator never patches the text, its `FAIL` message must be *specific* (exact word counts, exact section), and that specificity is precisely what lets the writer fix the right thing on retry.
- **Evaluator strictness determines whether the optimizer ever fires.** The same writer output that *passes* a lenient ±20-word check *fails* an exactly-150 check. The demo had to tighten the criterion to surface the retry path at all — a reminder that the threshold, not just the generator, controls loop behavior.
- **Model differences show up under tight constraints.** Claude and DeepSeek both produced strong digests, but only the strict word-count constraint exposed that DeepSeek needed a correction pass while Claude did not. Hard, measurable criteria are the best way to compare models objectively.
- **Bounded iterations prevent runaway loops.** Capping at 2 writer passes guarantees termination even if a model can never satisfy the evaluator; the pipeline reports the last verdict rather than spinning forever.
- **Snapshotting every version makes the loop auditable.** Keeping `digest_final_v1/v2.md` and `eval_result_v1/v2.md` turns an opaque "it eventually passed" into a readable trail of *what failed, why, and how it was fixed* — invaluable for debugging the pattern.

## Files in this block

| Filename | Purpose | Created by |
|----------|---------|------------|
| `instruction_files/claude/CLAUDE.md` | Claude entry rules, output paths, lineage requirements | Block 8 setup |
| `instruction_files/claude/agents/deep_digest.md` | Main pipeline: researchers → writer → evaluator → retry-on-FAIL | Block 8 setup |
| `instruction_files/claude/agents/writer.md` | Generator; on retry fixes the evaluator's listed issues | Block 8 setup |
| `instruction_files/claude/agents/evaluator.md` | Lenient evaluator (~150 words, 130–170 OK) | Block 8 setup |
| `instruction_files/deepseek/CLAUDE.md` | DeepSeek entry rules, output paths, lineage requirements | Block 8 setup |
| `instruction_files/deepseek/agents/deep_digest.md` | DeepSeek main pipeline (mirror of Claude's) | Block 8 setup |
| `instruction_files/deepseek/agents/writer.md` | DeepSeek generator | Block 8 setup |
| `instruction_files/deepseek/agents/evaluator.md` | DeepSeek lenient evaluator | Block 8 setup |
| `results/claude/digest_final.md` | Claude main-pipeline digest (PASS, iteration 1) | Claude run |
| `results/claude/eval_result.md` | Claude main-pipeline verdict: PASS | Claude run |
| `results/deepseek/digest_final.md` | DeepSeek main-pipeline digest (PASS, iteration 1) | DeepSeek run |
| `results/deepseek/eval_result.md` | DeepSeek main-pipeline verdict: PASS | DeepSeek run |
| `fail_retry_demo/instruction_files/claude/agents/deep_digest_demo.md` | Demo pipeline that snapshots every iteration | Block 8 demo setup |
| `fail_retry_demo/instruction_files/claude/agents/evaluator_strict.md` | Strict evaluator (exactly 150 words) | Block 8 demo setup |
| `fail_retry_demo/instruction_files/deepseek/agents/deep_digest_demo.md` | DeepSeek demo pipeline (mirror) | Block 8 demo setup |
| `fail_retry_demo/instruction_files/deepseek/agents/evaluator_strict.md` | DeepSeek strict evaluator | Block 8 demo setup |
| `fail_retry_demo/results/claude/research_plan.md` | Demo plan: 3 angles for *decentralized finance* | Claude demo run |
| `fail_retry_demo/results/claude/research_1..3.md` | Per-angle research notes | Claude demo run |
| `fail_retry_demo/results/claude/digest_final.md` | Claude demo digest (PASS, iteration 1) | Claude demo run |
| `fail_retry_demo/results/claude/eval_result_v1.md` / `eval_result.md` | Claude demo verdict: PASS first try | Claude demo run |
| `fail_retry_demo/results/deepseek/digest_final_v1.md` | DeepSeek failing draft (161/153/156 words) | DeepSeek demo run |
| `fail_retry_demo/results/deepseek/eval_result_v1.md` | DeepSeek verdict: FAIL with exact word counts | DeepSeek demo run |
| `fail_retry_demo/results/deepseek/digest_final_v2.md` / `digest_final.md` | DeepSeek corrected digest (exactly 150 words, iteration 2) | DeepSeek demo run |
| `fail_retry_demo/results/deepseek/eval_result_v2.md` / `eval_result.md` | DeepSeek verdict: PASS on iteration 2 | DeepSeek demo run |
| `fail_retry_demo/results/deepseek/research_plan.md`, `research_1..3.md` | DeepSeek demo plan and research notes | DeepSeek demo run |

## Inherited from previous blocks

This block builds directly on the **Block 7 orchestrator + subagents** pipeline:

- The orchestrator-driven entry pattern and `CLAUDE.md` strict-rules / lineage-header conventions.
- The deep-digest structure: a `research_plan.md` of angles, parallel `researcher.md` agents producing `research_[N].md`, and a `writer.md` that fuses them into `digest_final.md`.

What's new in Block 8 is the **evaluator** agent and the **bounded retry loop** wrapped around the writer — i.e. the evaluator-optimizer pattern itself.
