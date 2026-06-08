# Block 6 — Parallelization

## Pattern

Parallelization splits a single task into independent subtasks that run simultaneously, then merges their outputs in a final step. Use it when the work can be cleanly divided by angle or dimension (facts / context / examples), when each subtask is independent of the others, and when the bottleneck is breadth of coverage rather than depth of a single line of reasoning. The pattern trades sequential simplicity for throughput: three researchers working in parallel produce richer raw material in the same wall-clock time as one.

## What was built

A two-pipeline digest agent with a routing layer. The orchestrator classifies each user request as `quick` or `deep`. The quick pipeline is a single-agent pass producing a short factual summary. The deep pipeline fans out to three specialized researchers in parallel, waits for all three to finish, then passes their combined output to a writer who synthesizes a 600–800-word coherent essay. The entire setup was replicated for two model providers: Claude and DeepSeek.

```
block06_parallelization/
├── instruction_files/
│   ├── claude/
│   │   ├── CLAUDE.md          — entry-point rules and output paths
│   │   ├── ORCHESTRATOR.md    — classifies quick vs. deep
│   │   └── agents/
│   │       ├── quick_digest.md    — single-agent fast path
│   │       ├── deep_digest.md     — fan-out coordinator
│   │       ├── researcher_a.md    — facts specialist
│   │       ├── researcher_b.md    — context / origins specialist
│   │       ├── researcher_c.md    — real-world examples specialist
│   │       └── writer.md          — synthesis agent
│   └── deepseek/              — identical structure, deepseek-specific paths
│       ├── CLAUDE.md
│       ├── ORCHESTRATOR.md
│       ├── settings.json      — web access denied via hooks
│       └── agents/ (same six files)
└── results/
    ├── claude/                — research_a/b/c.md + digest_final.md
    └── deepseek/              — research_a/b/c.md + digest_final.md
```

## How it works

1. The user sends a request with a topic and a signal word (`quick` / `deep` / `detailed`, etc.).
2. `ORCHESTRATOR.md` reads the signal word and routes to either `quick_digest.md` or `deep_digest.md`, logging one line: `"Routing to [mode] pipeline for topic: [topic]"`.
3. **Quick path**: `quick_digest.md` identifies three key facts, writes one paragraph each, and saves to `quick_[topic].md`. Done.
4. **Deep path**:
   - `deep_digest.md` launches `researcher_a`, `researcher_b`, and `researcher_c` simultaneously.
   - Researcher A writes five dated/numbered facts → `research_a.md`.
   - Researcher B writes origins, key players, and problems solved → `research_b.md`.
   - Researcher C writes three concrete real-world examples (who / what / result) → `research_c.md`.
   - `deep_digest.md` waits until all three output files exist.
   - `writer.md` reads all three files, rewrites — not copies — their content into a unified 600–800-word essay, and saves to `digest_final.md`.
5. Every output file begins with a lineage header showing the full chain of files that produced it and the topic, making the provenance of any output traceable.

## Key learnings

- **Fan-out then join is the core primitive.** Three independent agents covering different dimensions (facts, context, practice) produce more varied raw material than one agent covering all three sequentially, without any added coordination cost.
- **The barrier between parallel and sequential is explicit wait logic.** `deep_digest.md` does one thing that matters: it lists the three output files and asserts they must exist before calling the writer. That single constraint is what turns parallelism into a correct pipeline.
- **Roles must be orthogonal, not overlapping.** Researcher A (facts with numbers), B (narrative/context), and C (concrete examples) were designed to not repeat each other. Overlap wastes the parallelism and forces the writer to deduplicate rather than synthesize.
- **Lineage headers make multi-agent output auditable.** When a final file is the product of four agents, knowing which file instructed which agent — and in what order — is essential for debugging divergences between providers.
- **Web access denial via hooks enforces the no-search constraint.** The DeepSeek `settings.json` uses a `PreToolUse` hook to block `WebFetch` and `WebSearch` at the harness level, not just in the prompt. This is a stronger guarantee than instructions alone.

## Files in this block

| Filename | Purpose | Created by |
|---|---|---|
| `instruction_files/claude/CLAUDE.md` | Entry-point rules, output paths, lineage requirement (Claude) | Block 6 |
| `instruction_files/claude/ORCHESTRATOR.md` | Classifies request as quick or deep, routes to pipeline | Block 6 |
| `instruction_files/claude/agents/quick_digest.md` | Single-agent fast-path pipeline (≤300 words) | Block 6 |
| `instruction_files/claude/agents/deep_digest.md` | Fan-out coordinator: spawns 3 researchers, waits, calls writer | Block 6 |
| `instruction_files/claude/agents/researcher_a.md` | Specialist: 5 facts with numbers and dates | Block 6 |
| `instruction_files/claude/agents/researcher_b.md` | Specialist: origins, players, problems solved | Block 6 |
| `instruction_files/claude/agents/researcher_c.md` | Specialist: 3 real-world examples (who/what/result) | Block 6 |
| `instruction_files/claude/agents/writer.md` | Synthesis agent: reads all three research files, writes final essay | Block 6 |
| `instruction_files/deepseek/CLAUDE.md` | Same as Claude version, DeepSeek-specific paths | Block 6 |
| `instruction_files/deepseek/ORCHESTRATOR.md` | Same routing logic, DeepSeek context | Block 6 |
| `instruction_files/deepseek/agents/*.md` | Mirror of Claude agents for DeepSeek | Block 6 |
| `instruction_files/deepseek/settings.json` | Denies web access via permissions + PreToolUse hook | Block 6 |
| `results/claude/research_a.md` | Facts output produced by researcher_a (topic: DeFi) | Claude agent |
| `results/claude/research_b.md` | Context output produced by researcher_b | Claude agent |
| `results/claude/research_c.md` | Examples output produced by researcher_c | Claude agent |
| `results/claude/digest_final.md` | Final synthesized essay by writer (Claude) | Claude agent |
| `results/deepseek/research_a.md` | Facts output (DeepSeek run) | DeepSeek agent |
| `results/deepseek/research_b.md` | Context output (DeepSeek run) | DeepSeek agent |
| `results/deepseek/research_c.md` | Examples output (DeepSeek run) | DeepSeek agent |
| `results/deepseek/digest_final.md` | Final synthesized essay (DeepSeek run) | DeepSeek agent |

## Inherited from previous blocks

| File | Source block | Role in this block |
|---|---|---|
| `CLAUDE.md` structure (entry-point + output paths + lineage) | Block 4 (prompt chaining) | Carried forward as the base configuration pattern |
| `ORCHESTRATOR.md` routing logic | Block 5 (routing) | Extended — routing now leads into a parallel pipeline rather than a single agent |
| Lineage header convention | Block 4 | Required in every output file; extended to show the parallel branch in brackets: `[researcher_a + researcher_b + researcher_c]` |
