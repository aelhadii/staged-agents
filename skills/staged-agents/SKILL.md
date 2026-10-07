---
name: staged-agents
description: >-
  Build or run a reliable multi-agent task using the loop→chain→network→graph
  ladder. Use when the user wants to add reflection/self-review to an AI step,
  set up a review or analysis pipeline, coordinate specialist agents, or give
  agents shared persistent memory. Starts at the cheapest stage and climbs only
  when a measured failure justifies it.
---

# Staged Agents — Loops to Graphs

A procedure for running a task at the right level of agentic complexity. Four
stages, each adds exactly one capability. The governing rule: **never start
higher than you must, and only climb when the stage below is failing for a
reason you can name and measure.**

The four stages and what each externalizes (moves out of a single prompt and
into structure):

| Stage | Externalizes | Add it when |
|-------|--------------|-------------|
| 1 · Loop | revision | one task, judgeable quality |
| 2 · Chain | order | fixed, predictable steps |
| 3 · Network | roles | different roles catch different errors |
| 4 · Graph | shared state / memory | facts must persist across runs |

---

## Step 0 — Discover the project (ALWAYS do this first)

This is what lets the skill work in any repo. Before running any stage, detect
and record:

- **Verify command** — the project's way to check work:
  `Cargo.toml` → `cargo test` (or `cargo check` for fast iteration);
  `pyproject.toml`/`setup.py` → `pytest`; `package.json` → `npm test`;
  `go.mod` → `go test ./...`. If none, note "no automated verify."
- **Review rules** — read `CLAUDE.md` / `AGENTS.md` if present; treat their
  constraints as hard rubric items (e.g. output-format invariants).
- **Persistence store** (only needed for Stage 4):
  prefer a persistent memory/knowledge MCP server if one is available
  (scope it by project), else fall back to a local
  `.staged-agents/findings.json` at the repo root.

State what you found in one line before proceeding.

---

## The contract (every agent returns THIS, never free prose)

```json
{
  "result": "the actual content",
  "evidence": ["test output / file:line / graph edge that grounds it"],
  "satisfied": true,
  "confidence": 0.0,
  "provenance": { "task": "...", "inputs": ["..."], "run_id": "..." }
}
```

`evidence`, `satisfied`, and `provenance` are mandatory — they are what make
each stage controllable (stopping, gating, tracing) instead of a black box.
Full schema: `references/contract.md`.

---

## Stage 1 — Loop  (the default; start here)

One agent improves its own work until it meets a written rubric.

1. **Draft** the output for the task.
2. **Critique** it against an explicit rubric — use one in `rubrics/`, or write
   one first. "Improve this" is not a rubric; name the qualities required and
   the defects to hunt. Keep critique and rewrite as *separate* steps.
3. **Revise** using the critique.
4. Repeat from 2 until `satisfied == true` **or 3 iterations**, whichever first.

Return the contract. **Stop here** unless the Promotion check says to climb.

---

## Stage 2 — Chain

Use only when the task splits into clear, fixed, ordered steps.

- Run the steps in a hardcoded order.
- After each step, run a **gate** that checks its output meets the next step's
  input contract.
- On gate failure, route to a fallback and report — **never pass bad data
  forward** (silent corruption is the failure this stage prevents).

---

## Stage 3 — Network

Use only when a single reviewer demonstrably misses an error class.

- Run role-specialist agents **in parallel**, each with its own rubric
  (e.g. `correctness`, `security`, project `invariants`).
- Merge their **typed artifacts**, not their conversation transcripts (feeding
  an orchestrator full transcripts is the "conversational bottleneck").
- Test for adding a role: *does it catch an error class the others miss?*
  If not, don't add it — identical agents multiply cost, not signal.

---

## Stage 4 — Graph

Use only when facts must persist across runs or be shared by several agents.

- **Before** running: query the store for prior findings on the touched
  entities (files, patterns). Feed them in as context.
- **After** running: write results back **additively** — a new version linked
  to the old with a `supersedes` edge, never an overwrite. Every fact carries
  provenance (its source).
- Schema (nodes, edges, provenance): `references/graph-schema.md`.

---

## Promotion check (the rule that decides whether to climb)

After finishing a stage, climb exactly one rung **only if all three hold**:

1. the current stage's failure rate on this task is **> ~5%**, and
2. the next stage **specifically addresses** that dominant failure, and
3. the extra **token cost is justified** by the expected lift.

Otherwise stop. Always report: which stage you ran, the measured failure, and
why you did or didn't climb. Details and worked thresholds:
`references/promotion-rules.md`.

---

## Guardrails — refuse these anti-patterns

- **Infinite loop** — any loop without an iteration cap.
- **Echo chamber** — multiple agents with identical rubrics/evidence.
- **Phantom graph** — writing to a store nothing ever queries.
- **Conversational bottleneck** — an orchestrator fed full transcripts.
- **Missing baseline** — no zero-shot result to measure lift against.
- **Premature agent** — a multi-agent build for what one good call handles.

---

## Optional: heavy parallel execution

For large fan-outs (many files / many findings), the Network and Graph stages
can be run as a background `Workflow` script instead of inline agents, using
the same contract and stages. Keep it for when inline fan-out is too slow;
inline is fine for most tasks.
