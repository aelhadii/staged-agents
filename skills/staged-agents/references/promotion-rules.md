# Promotion rules — when to climb a stage

The stages are a sequence, not a prestige ladder. You climb one rung only when
the rung below is the **binding constraint** — i.e. it is failing for a reason
the next stage specifically fixes. These rules make that decision explicit.

## The five rules

1. **Start cheapest.** Loop < Chain < Network < Graph in cost and complexity.
   Begin at the lowest stage that could plausibly work (usually Loop).
2. **Measure before promoting.** Establish a baseline for the current stage and
   measure the failure rate the next stage would address. If that failure rate
   is **below ~5%**, the next stage's cost almost certainly exceeds its benefit
   — do not climb.
3. **Match control to risk.** High-stakes work (security-sensitive code,
   irreversible actions) → prefer predictable stages (Chain, evaluation Loops)
   with explicit gates. Low-stakes work can tolerate Network/Planning
   unpredictability.
4. **Count tokens, not agents.** Cost is proportional to tokens, not to the
   number of conceptual agents. Three agents at 20k tokens each cost the same as
   one at 60k. Adding a role is only worth it if it catches an error class the
   others demonstrably miss.
5. **The graph earns itself.** Add Stage 4 only when the same entity or fact is
   queried by more than one agent or across more than one session.

## The promotion check, as a predicate

Climb from the current stage to the next **iff**:

```
failure_rate(current) > 0.05
  AND next_stage_addresses(dominant_failure_of(current))
  AND cost_per_unit_lift(next) < budget_threshold
```

If any clause is false: **stop**, and report the current stage's result.

## What to report after every run

- which stage you ran,
- the measured failure rate (or "not measured" + why),
- the decision: stopped here, or climbed to stage N because <reason>,
- token cost if available.

## Worked thresholds (starting points — tune per project)

| Situation | Decision |
|-----------|----------|
| Loop output passes rubric >95% of the time | stop at Loop |
| Loop misses a whole category of issue (e.g. security) | → Network, add that role |
| Steps are fixed and a mid-pipeline failure corrupts later ones | → Chain with gates |
| Same finding re-derived across multiple runs/files | → Graph |
| One well-prompted call already works | stay zero-shot; don't invoke stages |
