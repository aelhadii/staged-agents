# Promotion rules — when to climb a stage

The stages are ordered by cost — not a prestige ladder, and not a mandatory
sequence. Add the cheapest stage that specifically fixes the **binding
constraint** — the current stage's measured failure — and skip rungs that do
not. These rules make that decision explicit.

## The five rules

1. **Start cheapest.** Loop < Chain < Network < Graph in cost and complexity.
   Begin at the lowest stage that could plausibly work (usually Loop).
2. **Measure before promoting.** Establish a baseline for the current stage and
   count its failures as SKILL.md's Promotion check defines them. If that rate
   is **below ~5%**, the target stage's cost almost certainly exceeds its
   benefit — do not climb. High-stakes work (rule 3) has no floor: one
   confirmed blocking miss is enough.
3. **Match control to risk.** High-stakes work (security-sensitive code,
   irreversible actions) → prefer predictable stages (Chain, evaluation Loops)
   with explicit gates. Low-stakes work can tolerate Network unpredictability.
4. **Count tokens, not agents.** Cost is proportional to tokens, not to the
   number of conceptual agents. Three agents at 20k tokens each cost the same as
   one at 60k. Adding a role is only worth it if it catches an error class the
   others demonstrably miss.
5. **The graph earns itself.** Add Stage 4 only when the same entity or fact is
   queried by more than one agent or across more than one session — a
   persistence trigger, not a failure.

## Measuring

Report the failure rate as **k/n** over repeated runs; a single run is 0/1 or
1/1. A pass counts only on the verify command or an independent check — a run
the Loop marked `satisfied` with neither is "not measured" and stays out of n.
A stage cannot see an error class its rubric does not check: probe for it by
running that role's rubric once over the output.

## The promotion check, as a predicate

```
s = the cheapest stage not yet in use
    with addresses(s, dominant_failure_of(current))

Add s iff:
failure_rate(current) > floor(risk)
  AND s exists
  AND extra_cost(s) fits the budget

# failure_rate: k/n failed runs, as SKILL.md's Promotion check defines them
# floor: ~0.05; high-stakes work: one confirmed blocking miss is enough
# budget: the user's limit; none stated → ask before more than doubling
#   the agent calls
# Graph: a persistence trigger replaces the failure_rate clause
```

If any clause is false: **stop**, and report the current stage's result.

## What to report after every run

- which stage you ran,
- the measured failure rate as k/n (or "not measured" + why),
- the decision: stopped here, or added stage N because <reason>,
- agent calls made, and token cost if available.

## Worked thresholds (starting points — tune per project)

| Situation | Decision |
|-----------|----------|
| Loop output passes verify or an independent check >95% of the time | stop at Loop |
| Loop misses a whole category of issue (e.g. security) | → Network, add that role |
| Steps are fixed and a mid-pipeline failure corrupts later ones | → Chain with gates |
| Same finding re-derived across runs/files (any stage) | → add Graph around the current stage |
| One well-prompted call already works | stay zero-shot; don't invoke stages |
