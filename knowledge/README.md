# Knowledge base

Git-tracked domain and method knowledge for bogart. Read and written as plain markdown — no server, no index to rebuild.

Conventions: [`../agent/architecture.md`](../agent/architecture.md) and the knowledge section of `../CLAUDE.md`.

## Frontmatter

Every note carries YAML frontmatter:

```yaml
---
id: kebab-case-id
title: Short title
tags: tag-one, tag-two
created: "YYYY-MM-DD"
updated: "YYYY-MM-DD"
source: where this came from
related:
  - other-note-id
---
```

Filenames lowercase-kebab-case, stable once created.

## Layout

| Path | Purpose |
| --- | --- |
| `methods/` | Frameworks, playbooks, procedural notes |
| `insights/` | Synthesized research findings, dated |
| `ops/` | Process and agent-ops lessons, often from retrospectives |
| `references/` | External source summaries |
| `sessions/` | Session records worth keeping |

Architecture decisions live in [`../adr/`](../adr/), not here.

## Working with it

Search before creating — `Grep` across this folder. If a note on the topic exists, update it and bump `updated` rather than adding a near-duplicate. One canonical home per fact set.

Never write secrets, API keys, or credentials here. It is git-tracked.

`/learn-knowledge` runs the full cycle: research, confirm, store.

## Index

| Doc | Topic | Updated |
| --- | --- | --- |
| [methods/getting-started.md](methods/getting-started.md) | How to use bogart | 2026-07-28 |
| [methods/2026-07-27-ideo-design-thinking-five-modes.md](methods/2026-07-27-ideo-design-thinking-five-modes.md) | Design thinking — five modes | 2026-07-27 |
| [ops/continuous-learning-gate.md](ops/continuous-learning-gate.md) | Why the completion gate is built the way it is | 2026-07-28 |
