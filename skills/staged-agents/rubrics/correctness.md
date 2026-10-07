# Rubric: correctness

Use for the general correctness reviewer (Loop critique step, or the
`correctness` role in a Network).

## The output must

- Do what the task/spec actually asked — compare against the stated task, not a
  reinterpretation of it.
- Handle edge cases: empty input, nulls/None, boundary values, large input,
  concurrent/duplicate input where relevant.
- Be consistent with the project's existing conventions and invariants (from
  CLAUDE.md / AGENTS.md if present).
- Pass the project's verify command (from Step 0) with no failure that was not
  in its baseline — cite the actual output. With no automated verify (or "verify
  unavailable"), cite the manual check you ran; never claim tests passed.

## Hunt specifically for

- Off-by-one and boundary errors.
- Unhandled error paths (unwrap/expect, unchecked results, swallowed
  exceptions).
- Resource issues: leaks, unbounded growth, missing cleanup.
- Logic that contradicts a stated requirement.
- Silent data loss or truncation.

## Every finding must

- Cite a concrete location (`file:line`) and the rubric item or requirement it
  violates.
- Include evidence (test output, a failing input) — not a hypothetical.
- State severity on the scale in `references/contract.md` (blocking /
  should-fix / nit).

A critique with no cited location and no evidence is not `satisfied`-worthy —
send it back.
