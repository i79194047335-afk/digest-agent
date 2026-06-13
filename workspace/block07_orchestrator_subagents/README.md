# Block 07 — Orchestrator + Subagents

Demonstrates a multi-agent digest pipeline where a central orchestrator classifies user requests and delegates work to specialized subagents. Both Claude and DeepSeek implementations are provided side by side.

## What it does

The agent reads a topic request, picks one of two pipelines, and produces a structured markdown digest:

- **Quick pipeline** — three key-fact paragraphs, ≤ 300 words, single output file.
- **Deep pipeline** — orchestrator writes a research plan, spawns up to three parallel researcher subagents (one per angle), then a writer subagent synthesizes the results into a final digest.

Every output file carries a lineage header that traces the exact chain of instruction files that produced it.

## Directory layout

```
block07_orchestrator_subagents/
├── instruction_files/
│   ├── claude/
│   │   ├── CLAUDE.md          # Entry-point rules and output paths
│   │   ├── ORCHESTRATOR.md    # Classifier: routes to quick or deep pipeline
│   │   └── agents/
│   │       ├── quick_digest.md   # Quick pipeline
│   │       ├── deep_digest.md    # Deep pipeline coordinator
│   │       ├── researcher.md     # Single-angle research subagent (spawned ×N)
│   │       └── writer.md         # Synthesis subagent
│   └── deepseek/              # Parallel implementation for DeepSeek
│       └── ...                # Same structure as claude/
└── results/
    ├── claude/                # Sample outputs from a Claude run
    │   ├── research_plan.md
    │   ├── research_1.md
    │   ├── research_2.md
    │   ├── research_3.md
    │   └── digest_final.md
    └── deepseek/              # Sample outputs from a DeepSeek run
        └── ...
```

## Pipeline flow

```
User request
    │
    ▼
ORCHESTRATOR.md
    ├─ "quick" / "brief" ──► quick_digest.md ──► quick_[topic].md
    │
    └─ "deep" / "detailed" ──► writes research_plan.md
                                    │
                                    ▼
                              deep_digest.md
                                    │
                         ┌──────────┼──────────┐
                         ▼          ▼          ▼
                   researcher   researcher  researcher
                   (angle 1)    (angle 2)   (angle 3)   ← run in parallel
                         │          │          │
                         └──────────┼──────────┘
                                    ▼
                               writer.md
                                    │
                                    ▼
                             digest_final.md
```

## Key design patterns

**Orchestrator-as-classifier** — `ORCHESTRATOR.md` does no research itself. Its only jobs are to classify the request, write the research plan, and hand off to the appropriate agent file.

**Plan-before-spawn** — The orchestrator writes `research_plan.md` before launching any subagents. This makes the decomposition explicit and auditable, and prevents researchers from drifting into overlapping angles.

**Parallel researcher subagents** — `deep_digest.md` spawns one `researcher.md` instance per angle and runs them concurrently. Each researcher is scoped to exactly one angle and writes to a numbered file (`research_1.md`, `research_2.md`, `research_3.md`).

**Lineage headers** — Every output file begins with a `> Routed by:` block that records the full instruction-file chain. This makes it straightforward to audit how any output was produced.

**No built-in research tools** — Agents synthesize from parametric knowledge only. No web search, no URLs. This keeps the pipeline deterministic and provider-portable.

## Sample run

The `results/` directory contains a completed deep-pipeline run on the topic **"Arbitrage on DEXs"**:

| File | Contents |
|---|---|
| `research_plan.md` | Three research angles: AMM mechanics, execution strategies, risk/profitability economics |
| `research_1.md` | 5 points on AMM price formation |
| `research_2.md` | 5 points on arbitrage execution infrastructure |
| `research_3.md` | 5 points on MEV competition and profit margins |
| `digest_final.md` | Three 150-word sections synthesized from the research files |

## Running the pipeline

Point your Claude Code or DeepSeek agent session at the appropriate `CLAUDE.md` as the project instruction file, then send a topic request:

```
# Quick run
Give me a quick digest on proof-of-stake consensus

# Deep run
Give me a deep analysis of liquid restaking
```

The agent will route automatically based on the keywords in your request.
