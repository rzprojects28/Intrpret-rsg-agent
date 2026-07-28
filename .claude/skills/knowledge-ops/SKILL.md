---
name: knowledge-ops
description: >
  Knowledge base management — ingesting, classifying, deduplicating, and retrieving durable knowledge
  in ./knowledge/ and Claude Code memory. Use when the user wants to save something to the KB, organize
  or deduplicate existing notes, or asks "what do I know about X" — or says save this, ingest this,
  update the knowledge base, sync knowledge.
---

# Knowledge Operations

Manage durable knowledge so it can be found again and trusted when found.

Two stores, and they are not interchangeable.

## The two layers

### `./knowledge/` — durable, git-tracked, shareable

The canonical store. Curated markdown, reviewed like code, committed to the repo.

| Folder | Contents |
| --- | --- |
| `methods/` | Frameworks, playbooks, procedures |
| `insights/` | Synthesized findings, dated |
| `ops/` | Process and agent-ops lessons |
| `references/` | External source summaries |
| `sessions/` | Session records worth keeping |

Frontmatter on every file: `id`, `title`, `tags`, `created`, `updated`, `source`, `related[]`. Filenames lowercase-kebab-case, stable once created.

Access with `Grep`, `Glob`, `Read`, `Write`, `Edit` — plain files, no tooling in between.

### Claude Code memory — quick recall, cross-session

Path: `~/.claude/projects/<project-slug>/memory/`. One fact per file with frontmatter (`name`, `description`, `metadata.type` of user / feedback / project / reference), plus a `MEMORY.md` index line per file. Loaded automatically at session start.

Use it for what should be recalled *without being asked*: user preferences, working agreements, project constraints not derivable from the code.

### Choosing between them

| Content | Goes to |
| --- | --- |
| Research findings, methods, domain notes | `./knowledge/` |
| A decision made between alternatives | `./adr/` (see **retrospective**) |
| How the user wants to work; standing constraints | Memory |
| Something only this conversation needs | Neither — leave it in the thread |

When something belongs in both, `./knowledge/` holds the full note and memory holds a one-line pointer to it.

## Ingestion workflow

### 1. Classify

Decide the store and folder before writing anything. If it does not fit an existing folder, prefer the closest match over inventing a new one.

### 2. Deduplicate — always, before creating

```
Grep -i "<key terms>" ./knowledge/
```

Search on several phrasings, not one. If a note on the topic exists, **update it** — bump `updated`, note what changed. Do not create a near-duplicate under a different filename. One canonical home per fact set.

### 3. Store

Write the file with complete frontmatter. Keep the body focused: what was learned, the evidence behind it, and the reusable takeaway.

### 4. Index

Update the index table in `knowledge/README.md` when the note is significant. Add `related[]` links both ways when a note connects to an existing one or to an ADR.

## Best practices

- Search before you create. This is the rule that prevents the KB rotting into duplicates.
- Keep notes concise. Split a note that has grown to cover two topics.
- Never write secrets — API keys, passwords, tokens, personal data — into `./knowledge/`. It is git-tracked.
- Update in place when facts change; bump `updated` and say what changed rather than appending indefinitely.
- Tag consistently. Reuse existing tags before coining new ones.
- Knowledge is not code truth. Product requirements belong in requirement docs; binding decisions belong in ADRs.

## Quality gate

Before finishing any knowledge operation, confirm:

- No duplicate created — you searched first
- Frontmatter complete and valid
- No sensitive data in anything git-tracked
- `knowledge/README.md` index updated if warranted
- Cross-references added where relevant

## Related

- **retrospective** — writes ADRs and lessons after a major task
- **samectx** — session context to memory
- `/learn-knowledge` — research that ends in a stored note
