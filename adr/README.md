# Architecture Decision Records

Durable records of decisions made between alternatives. Written by the **retrospective** skill after a major task, or by hand.

## What belongs here

All four must hold:

- **A decision was made** — a choice between real alternatives, not a default
- **Non-obvious** — not derivable from reading the code
- **Durable** — likely still relevant months from now
- **Relevant beyond the moment** — useful to anyone picking up this project

Reusable knowledge that is *not* a decision goes to [`../knowledge/`](../knowledge/) instead.

## Naming

`ADR-{NNN}-{short-title}.md`, zero-padded to three digits, starting at `001`.

## Template

```markdown
# ADR-{NNN}: {Title}

## Status
Accepted | Deprecated | Superseded (by ADR-XXX)

## Context
What situation or problem led to this decision?

## Decision
What was decided. Be specific.

## Rationale
Why this over the alternatives? Which alternatives were considered?

## Consequences
Results, trade-offs, risks, follow-up actions.

## Date
YYYY-MM-DD
```

## Rule

ADRs are immutable. Never edit a past decision — write a new ADR that supersedes it, and update the old one's status.

## Index

| ADR | Decision | Date |
| --- | --- | --- |
| [ADR-001](ADR-001-domain-neutral-by-default.md) | Domain-neutral skills; specificity comes from `agent/context.md` | 2026-07-29 |
| [ADR-002](ADR-002-slash-commands-replace-workflow-engine.md) | Two slash commands replace the workflow engine and its 16 prompts | 2026-07-29 |
