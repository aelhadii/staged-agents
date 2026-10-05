# Graph schema (Stage 4)

A minimal, additive knowledge graph. The point is not storage — it is
**persistence + provenance**: facts survive context-window flushes and every
fact can be traced to its source and its prior versions.

## Nodes

| Node | Meaning | Example |
|------|---------|---------|
| `Entity` | a thing in the domain | a file, crate, malware family, person |
| `Claim` / `Finding` | a statement that may be supported or contradicted | "line 12 unwraps a None" |
| `Source` | what grounds a claim | a test result, a doc, an API response |
| `Artifact` | a produced thing | a plan, draft, patch, report |
| `Run` | one execution record | run_id, stage, timestamp, token cost |

## Edges

| Edge | From → To | Meaning |
|------|-----------|---------|
| `found_in` | Finding → Entity | where it applies |
| `supports` | Source → Claim | evidence for |
| `contradicts` | Source → Claim | evidence against |
| `derived_from` | Artifact → Source | provenance |
| `supersedes` | Claim(vN) → Claim(vN-1) | a new version replaces an old one |
| `similar_to` | Finding → Finding | recurring pattern across entities |

## Write rules (non-negotiable)

- **Additive only.** Never overwrite a claim. Create a new version and link it
  with `supersedes`. This is what lets you answer "why did this result change?"
- **Provenance on every edge.** A claim with no `Source` is unverified; mark it
  as such rather than treating it as fact.
- **The graph earns itself.** Only write what some agent or future run will
  query. A store nothing reads back is a phantom graph — overhead, not value.

## Query rules (Stage 4, before running)

- Look up prior `Finding`s `found_in` the entities the current task touches.
- If a new task touches an entity with a past finding, surface the
  `similar_to` pattern before re-deriving it.

## Store backends

- **`kr` MCP** (preferred when present): nodes → entries, edges → links,
  scope by project. Durable and cross-session by default.
- **Local JSON** (`.staged-agents/findings.json`): a single array of Finding
  objects with `supersedes` by id. Good enough to start; the paper's own advice
  is "start simple: a shared JSON file, graduate to a graph when needed."
