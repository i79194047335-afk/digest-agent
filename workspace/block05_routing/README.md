# Block 5 — Routing

## Pattern

Routing is the pattern where an orchestrator reads a user request, classifies intent based on keywords, and dispatches execution to one of several specialized sub-pipelines. Instead of a single monolithic agent, the system is split into a classifier (ORCHESTRATOR.md) and mode-specific handlers (quick_digest.md, deep_digest.md). This pattern is useful when the same topic can require very different levels of effort — a quick overview vs. a full analysis — and the right pipeline should be selected automatically rather than left to the user to specify every time.

## What was built

Two parallel instruction sets were created — one for Claude, one for DeepSeek — each implementing the same routing logic independently. Claude's setup adds per-mode agent files under an `agents/` subfolder; DeepSeek's setup relies on ORCHESTRATOR.md referencing them and adds a `settings.json` that enforces web-access restrictions at the harness level. Both agents were run against the topic "blockchain" and their outputs were captured.

```
block05_routing/
├── instruction_files/
│   ├── claude/
│   │   ├── CLAUDE.md               — entry-point rules and output path conventions
│   │   ├── ORCHESTRATOR.md         — classifier: routes to quick or deep pipeline
│   │   └── agents/
│   │       ├── quick_digest.md     — quick pipeline: 3 facts, ≤300 words
│   │       └── deep_digest.md      — deep pipeline: 5-section analysis, ≤1000 words
│   └── deepseek/
│       ├── CLAUDE.md               — entry-point rules (DeepSeek variant)
│       ├── ORCHESTRATOR.md         — same classifier logic as Claude's
│       └── settings.json           — denies WebFetch/WebSearch at permission level
└── results/
    ├── claude/
    │   ├── quick_blockchain.md     — quick pipeline output (Claude)
    │   └── deep_blockchain.md      — deep pipeline output (Claude)
    └── deepseek/
        ├── quick_blockchain.md     — quick pipeline output (DeepSeek)
        └── deep_blockchain.md      — deep pipeline output (DeepSeek)
```

## How it works

1. The user sends a request containing a topic and a mode signal (e.g. "quick overview of blockchain" or "deep analysis of blockchain").
2. The agent starts at CLAUDE.md, which mandates reading ORCHESTRATOR.md first.
3. ORCHESTRATOR.md scans the request for keywords: "quick / brief / short" → quick pipeline; "deep / full / detailed" → deep pipeline; ambiguous → asks the user.
4. The orchestrator writes one line confirming the routing decision: `Routing to [mode] pipeline for topic: [topic]`.
5. Execution transfers to the selected pipeline file (quick_digest.md or deep_digest.md).
6. Each pipeline produces an output file with a required lineage header showing the full chain of files that produced it.
7. The final file is saved to the model-specific workspace folder with the naming convention `quick_[topic].md` or `deep_[topic].md`.

## Key learnings

- **Routing via keyword matching is simple and reliable.** Checking for a small vocabulary of intent signals ("quick", "deep") works well for constrained domains and requires no ML — just pattern matching in natural language instructions.
- **The lineage header is the audit trail.** Requiring every output to begin with `> Routed by: …` makes the decision chain explicit and inspectable without any external logging.
- **Settings.json enforces constraints the instructions alone cannot.** The DeepSeek setup blocks WebFetch/WebSearch at the harness permission layer, not just via instructed rules. This is more robust: the agent cannot accidentally comply with a user request that would break the constraint.
- **Claude and DeepSeek produce structurally different outputs at the same prompt.** Claude's deep digest is tightly structured around the trilemma and practical implications; DeepSeek's covers more breadth (Solana, Move, Celestia, DeFi primitives, CBDCs) with denser domain terminology. Routing logic is identical but the downstream quality differs.
- **Separating the orchestrator from the pipeline files keeps each file focused.** The orchestrator holds only classification logic; pipelines hold only execution logic. Changing the depth of the deep pipeline does not require touching the routing rules.

## Re-run note (2026-06-12 correction)

The original DeepSeek run was invalid: `ORCHESTRATOR.md` dispatched to `agents/quick_digest.md` and `agents/deep_digest.md`, but neither file was ever created for DeepSeek — both task entry points were dangling. The two agent files were added (mirrored from Claude's, with `deepseek/` output paths) and Block 5 was re-run for DeepSeek alone. Outcome:

- **Quick** — compliant: 176 words (≤300), 3 facts, lineage header present.
- **Deep** — the required "What this means in practice" section now appears (it was missing in the original run), confirming that omission was a **configuration/archive gap, not a model failure**. Word count fell from 1319 to 1154 but still exceeds the 800–1000 cap, so DeepSeek's length overshoot **persists even with complete, correct instructions** — a model-level trait rather than a setup issue. Kept as-is to document authentic behavior.

## Files in this block

| Filename | Purpose | Created by |
|---|---|---|
| `instruction_files/claude/CLAUDE.md` | Entry-point rules: output paths, lineage header requirement, action narration | Human |
| `instruction_files/claude/ORCHESTRATOR.md` | Keyword classifier; dispatches to quick or deep pipeline | Human |
| `instruction_files/claude/agents/quick_digest.md` | Quick pipeline: 3 facts, 3 paragraphs, ≤300 words, no intro/conclusion | Human |
| `instruction_files/claude/agents/deep_digest.md` | Deep pipeline: plan → write → enrich, 5 sections, 800–1000 words | Human |
| `instruction_files/deepseek/CLAUDE.md` | Entry-point rules (DeepSeek variant, deepseek-scoped output paths) | Human |
| `instruction_files/deepseek/ORCHESTRATOR.md` | Same classifier logic as Claude's orchestrator | Human |
| `instruction_files/deepseek/settings.json` | Harness-level permission deny for WebFetch, WebSearch, curl, wget | Human |
| `results/claude/quick_blockchain.md` | Quick digest of "blockchain" produced by Claude | Claude |
| `results/claude/deep_blockchain.md` | Deep digest of "blockchain" produced by Claude | Claude |
| `results/deepseek/quick_blockchain.md` | Quick digest of "blockchain" produced by DeepSeek | DeepSeek |
| `results/deepseek/deep_blockchain.md` | Deep digest of "blockchain" produced by DeepSeek | DeepSeek |

## Inherited from previous blocks

The two-agent structure (Claude + DeepSeek running the same instruction set in parallel) was introduced in Block 4 (prompt chaining). Block 5 extends that setup by replacing the linear chain with a branching router, keeping the dual-agent comparison methodology intact.
