---
name: samectx
description: >
  Extract key tasks, keypoints, and decisions from the current conversation and save them to Claude Code
  memory for cross-session recall. Use when the user says save context, sync notes, record this
  conversation, 保存上下文, 同步笔记, 记录对话 — and as the first step after "Mark task done" at the task
  completion gate.
---

# Session context sync

Turn a conversation into durable, recallable context. Runs on request, and automatically as the first step after the task completion gate in `CLAUDE.md`.

## What gets extracted

Three categories, kept distinct:

| Category | What belongs |
| --- | --- |
| **Key tasks** | What was worked on or is still outstanding |
| **Keypoints** | Technical facts, findings, constraints discovered |
| **Decisions** | Choices made between alternatives, and why |

## Where it goes

**Claude Code memory** — `~/.claude/projects/<project-slug>/memory/`. One fact per file, loaded automatically at the start of future sessions.

```markdown
---
name: <short-kebab-case-slug>
description: <one-line summary — used to decide relevance during recall>
metadata:
  type: user | feedback | project | reference
---

<the fact, stated plainly>
```

Then add one line to `MEMORY.md` in the same folder: `- [Title](file.md) — hook`.

Link related memories with `[[name]]` in the body.

### Choosing the type

| Type | Content |
| --- | --- |
| `project` | Ongoing work, goals, constraints not derivable from the code |
| `feedback` | How the user wants you to work — include the why |
| `user` | Who they are: role, expertise, standing preferences |
| `reference` | Pointers to external resources — URLs, dashboards, tickets |

## Process

1. **Extract** — read back over the session and pull out the three categories. Be selective: a memory that restates what the code already shows is noise.
2. **Check for duplicates** — read `MEMORY.md` first. If a memory already covers the fact, **update that file** rather than adding a second one.
3. **Convert relative dates** — "last week" becomes the absolute date. Memories are read months later.
4. **Write** — one file per fact, plus the `MEMORY.md` index line.
5. **Report** — list the files written or updated, one line each.

## Rules

- **No secrets.** Never write passwords, API keys, tokens, or credentials.
- **One fact per file.** A file covering three things cannot be recalled precisely or deleted cleanly.
- **Skip what the repo already records.** Code structure, git history, and `CLAUDE.md` contents are not memories.
- **Skip what only this conversation needs.** If it stops mattering when the session ends, leave it in the thread.
- **Update, do not accumulate.** A wrong memory is worse than a missing one — delete memories that turn out to be false.

## Optional: in-repo session record

When a session is worth preserving for others — not just for your own recall — write a fuller record to `./knowledge/sessions/{date}-{slug}.md` with standard knowledge frontmatter. This is git-tracked and shareable, where memory is local to this machine.

Use it sparingly. Most sessions do not warrant one.

## Related

- **retrospective** — runs immediately after this at the completion gate; writes ADRs and reusable lessons
- **knowledge-ops** — the durable, git-tracked store and how it differs from memory
