# Rubric: security

Use for the `security` role in a Network stage. System stance: **assume every
input is malicious and every boundary is attackable.** This role exists to
catch an error class the correctness reviewer optimizes away.

## Hunt specifically for

- **Injection** — SQL/command/path/template injection; unsanitized input
  reaching an interpreter, shell, filesystem path, or query.
- **Deserialization / parsing** of untrusted data without bounds or validation
  (especially relevant for log/archive/dump parsers).
- **Resource exhaustion** — zip bombs, unbounded allocation, quadratic blowup,
  missing size/time limits on attacker-controlled input.
- **Secret handling** — credentials/tokens logged, written to disk unencrypted,
  or included in output/telemetry.
- **Path traversal** — archive extraction or file writes that escape the
  intended directory (`../`, absolute paths, symlinks).
- **Trust boundaries** — read vs. write permissions mixed; tool/agent given
  more access than its task needs.

## Every finding must

- Name the threat (what an attacker does) and the impact.
- Cite the location (`file:line`) and a concrete trigger (malicious input shape).
- State severity and the minimal fix.
