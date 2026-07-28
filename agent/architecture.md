# How bogart is put together

bogart is a **configuration, not a program**. There is no server, no build step, nothing to install. It is a set of markdown files that Claude Code reads.

Scope and intent: [context.md](context.md).

## The pieces

| Piece | Path | Loaded |
| --- | --- | --- |
| Rules | `CLAUDE.md` | Every turn, automatically |
| Skills | `.claude/skills/<name>/SKILL.md` | On demand, when the description matches the task |
| Commands | `.claude/commands/<name>.md` | When you type `/<name>` |
| Knowledge | `knowledge/**/*.md` | Read as files when relevant |
| Decisions | `adr/ADR-NNN-*.md` | Read as files when relevant |

That is the whole system.

## Why the split matters

`CLAUDE.md` costs tokens on every turn, so it holds only what must always apply: identity, the completion gate, writing style, working discipline. Everything conditional lives in a skill, which costs nothing until it triggers.

This is the main design constraint. When adding behavior, the first question is whether it belongs in always-on or on-demand context. The answer is usually on-demand.

## The completion gate

The one genuinely opinionated mechanism. After a major task, bogart stops, asks whether to close it, and only then writes what it learned back into `knowledge/`, `adr/`, and Claude Code memory.

The point is compounding: work done today should make work next month cheaper. Without a forced gate, the lessons evaporate when the session ends.

Defined in `CLAUDE.md`; carried out by the **samectx** and **retrospective** skills.

## Knowledge

`knowledge/` is the canonical store — git-tracked markdown with YAML frontmatter, organized into `methods/`, `insights/`, `ops/`, `references/`, `sessions/`.

Claude Code memory (`~/.claude/projects/<slug>/memory/`) is the second store, for facts that should surface without being asked for. It is local to your machine and not shared.

Rule of thumb: `knowledge/` is what the project knows; memory is what bogart remembers about you.

## What is deliberately not here

| Not built | Why |
| --- | --- |
| MCP server for knowledge and workflow tools | Claude Code reads and writes files natively. A server would add a layer without adding capability. |
| Workflow engine | The two real workflows are interview scripts. Slash commands already do that. |
| Web chat UI | No second client, so nothing needs a shared core to serve it. |
| Vector search over `knowledge/` | `Grep` is sufficient at this size. Revisit past a few hundred notes. |
| Hooks enforcing the completion gate | The `CLAUDE.md` instruction is being tried first. Add a `Stop` hook in `.claude/settings.json` if it proves unreliable. |

The upstream project specified all of these in detail and built none of them. They were removed rather than left as aspirational documentation, which is worse than none.

## Extending it

| Want to | Do |
| --- | --- |
| Add a capability | `/build-skill`, or write `.claude/skills/<name>/SKILL.md` by hand |
| Add a repeatable procedure | Add `.claude/commands/<name>.md` |
| Change how bogart always behaves | Edit `CLAUDE.md` — sparingly, it is always-on |
| Promote a recurring pattern into a rule | Run the **rule-creator** skill |
| Teach it something | `/learn-knowledge` |

Skills and commands take effect in new sessions; restart to pick them up.
