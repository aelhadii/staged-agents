# The Step contract

Every agent in every stage takes the same `Input` and returns the same
`Output`. This uniformity is what lets the four stages wrap each other freely
(a Graph of a Network of Loops is just function composition).

## Input

```json
{
  "task": "what to produce, stated once and kept immutable",
  "context": ["prior results", "discovered project facts", "graph priors"],
  "rubric": "the explicit criteria the output is judged against"
}
```

- `task` is **immutable** across a Loop's iterations — never let the critic
  rewrite the goal (that is "rubric drift").

## Output

```json
{
  "result": "the actual content produced",
  "evidence": [
    "concrete grounding: test output, file:line, a graph edge, a source doc"
  ],
  "satisfied": true,
  "confidence": 0.8,
  "provenance": {
    "task": "the task this answers",
    "inputs": ["what went in"],
    "revision": "the git commit the inputs were read at",
    "run_id": "unique id for this execution"
  }
}
```

## Field rules

- **evidence** — must cite something real. "might not handle edge cases" is not
  evidence; "line 12 crashes on an empty list, violating rubric item 3" is.
- **satisfied** — the Loop's stopping signal. Set by the critic step, not the
  generator (separate the two roles). `true` only when there is **no blocking
  finding** and **no verify failure that was not in the baseline**.
- **confidence** — informational, 0.0–1.0. It may rank findings; it **never
  overrides verification**.
- **provenance** — what makes any output traceable back to its cause. This is
  the field that, at Stage 4, becomes graph edges. `revision` tells a later run
  whether a stored finding may be stale.

## Severity — one scale for every rubric and role

| Severity | Means |
|----------|-------|
| `blocking` | wrong, breaks a stated requirement, adds a new verify failure, or exploitable |
| `should-fix` | a real defect that need not block — e.g. pre-existing, or outside the task |
| `nit` | style or clarity |

## Findings (review roles)

A review role returns `result` as an array of findings:

```json
{ "location": "src/x.rs:12", "claim": "unwraps a None on empty input",
  "trigger": "parse(\"\")", "severity": "blocking", "status": "unverified" }
```

`trigger` is the input or command that shows the defect. `status` stays
`unverified` until the Network's verify step marks it `confirmed` or
`refuted`. Add any field the rubric asks for (e.g. `evidence`, `fix`).

## Why typed, not prose

Prose between stages forces every downstream agent to re-parse and re-interpret,
and lets errors pass silently. A typed artifact can be gate-checked at a stage
boundary and merged without a transcript — which is exactly what prevents the
"conversational bottleneck" and "silent corruption" failure modes.
