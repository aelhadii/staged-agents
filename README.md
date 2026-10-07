# staged-agents

A Claude Code **skill** for building or running a reliable multi-agent task using the
**loop → chain → network → graph** ladder. It starts at the cheapest stage and climbs
only when a measured failure justifies it.

Use it when you want to:

- add reflection / self-review to an AI step,
- set up a review or analysis pipeline,
- coordinate specialist agents, or
- give agents shared persistent memory.

This repo is both a **plugin** and a **plugin marketplace**, so you can install it the
easy way (via `/plugin`) or just drop the skill folder into place manually.

> **Current version: v0.1.1** — the commands below always fetch the latest release.

---

## Install as a plugin (recommended)

```bash
# 1. Add this repo as a marketplace
claude plugin marketplace add aelhadii/staged-agents

# 2. Install the plugin
claude plugin install staged-agents@staged-agents
```

Or from inside a Claude Code session:

```
/plugin marketplace add aelhadii/staged-agents
/plugin install staged-agents@staged-agents
```

Update later with `claude plugin marketplace update staged-agents` followed by
`claude plugin update staged-agents@staged-agents`, then restart Claude Code to apply it.

## Install as a plain skill (manual)

The skill lives at [`skills/staged-agents/`](skills/staged-agents). Copy it into your
Claude Code skills directory:

```bash
git clone https://github.com/aelhadii/staged-agents ~/src/staged-agents
mkdir -p ~/.claude/skills
cp -R ~/src/staged-agents/skills/staged-agents ~/.claude/skills/
```

To update, run `git -C ~/src/staged-agents pull` and the `cp` line again. (Or symlink
the skill folder instead of copying it, and `git pull` to update.) Keep the clone out of
`/tmp`, which macOS and many Linux setups clean up. Claude Code auto-discovers any skill
in `~/.claude/skills/`; if that directory did not exist when your session started,
restart Claude Code. Use one install method, not both, or the skill is loaded twice.

---

## What's inside

```
.
├── .claude-plugin/
│   ├── plugin.json         # plugin manifest
│   └── marketplace.json    # marketplace manifest (source "./")
└── skills/
    └── staged-agents/
        ├── SKILL.md        # the procedure
        ├── references/     # contract, graph schema, promotion rules
        └── rubrics/        # correctness, security review rubrics
```

## License

MIT — see [LICENSE](LICENSE).
