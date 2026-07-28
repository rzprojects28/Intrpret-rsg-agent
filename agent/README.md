# agent/ — design docs

Human-facing design notes for bogart. Nothing here is loaded at runtime.

| File | Purpose |
| --- | --- |
| [context.md](context.md) | Who bogart works for and what for — **fill this in** |
| [architecture.md](architecture.md) | How the pieces fit together, and what is deliberately absent |

Runtime assets live elsewhere:

| Asset | Path |
| --- | --- |
| Rules | `CLAUDE.md` (repo root) |
| Skills | `.claude/skills/` |
| Commands | `.claude/commands/` |
| Knowledge | `knowledge/` |
| Decisions | `adr/` |

There is no draft-versus-production split. Edit runtime assets directly — `.claude/` is the live location, not a staging area.
