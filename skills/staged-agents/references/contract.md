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
  "confidence": 0.0,
  "provenance": {
    "task": "the task this answers",
    "inputs": ["what went in"],
    "run_id": "unique id for this execution"
  }
}
```

## Field rules

- **evidence** — must cite something real. "might not handle edge cases" is not
  evidence; "line 12 crashes on an empty list, violating rubric item 3" is.
- **satisfied** — the Loop's stopping signal. Set by the critic step, not the
  generator (separate the two roles).
- **confidence** — used by Network's merge and by the Promotion check.
- **provenance** — what makes any output traceable back to its cause. This is
  the field that, at Stage 4, becomes graph edges.

## Why typed, not prose

Prose between stages forces every downstream agent to re-parse and re-interpret,
and lets errors pass silently. A typed artifact can be gate-checked at a stage
boundary and merged without a transcript — which is exactly what prevents the
"conversational bottleneck" and "silent corruption" failure modes.
