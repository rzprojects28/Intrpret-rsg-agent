---
name: retrospective
description: >
  Post-task retrospective — persist lessons into ./adr (decisions) and ./knowledge (reusable
  research/ops/domain notes). Use when a feature or story is done, after a hard bug or architecture
  choice, or when the user says retrospective / lessons learned / 回顾 / 总结经验. Runs as the second
  step of the task completion gate, after samectx.
---

# Retrospective Skill

After completing a task or feature, extract lessons learned and persist them in the project repository as:

1. **ADRs** (`./adr/`) — durable architecture and process **decisions**
2. **Knowledge** (`./knowledge/`) — reusable research conclusions, ops lessons, and domain notes that are **not** themselves a decision record

Create `./adr/` if it does not exist, and follow the conventions in `knowledge/README.md` and `CLAUDE.md`. Do not create a second knowledge tree elsewhere.

This is the **second** step of the task completion gate — **samectx** runs first and handles session context to memory. Keep the split: memory holds what should be recalled automatically; `./adr/` and `./knowledge/` hold what the repository should carry.

## When to Use

- After completing a feature or slice of work
- After resolving a difficult bug
- After making a significant architectural or process decision
- After research that produced durable domain or ops insight
- When the user explicitly requests a retrospective
- After "Mark task done" at the task completion gate — see `CLAUDE.md`

## Process

### Step 1: Review What Happened

Look back at the completed work and identify:

**What went well?**
- Approaches that worked better than expected
- Decisions that proved correct
- Tools or patterns that were effective

**What was difficult?**
- Blockers encountered and how they were resolved
- Approaches that failed before finding the right one
- Unexpected complexity or edge cases

**What would you do differently?**
- Better approaches discovered in hindsight
- Things to avoid next time
- Process improvements

**What new knowledge was learned?**
- Domain facts, API/ops quirks, research conclusions
- Reusable runbooks, glossaries, data-source maps
- Constraints or gotchas that future work should reuse

### Step 2: Split Insights — ADR vs Knowledge

| Persist as | When | Location |
| --- | --- | --- |
| **ADR** | A choice was made among alternatives; non-obvious; durable; team-relevant | `./adr/` |
| **Knowledge** | New reusable understanding (research, ops, domain, how-to) that is not primarily a decision record | `./knowledge/` (e.g. `ops/`, `methods/`, `insights/`) |

**ADR-worthy** — all of:
- **A decision** — a choice was made between alternatives
- **Non-obvious** — not derivable from reading the code or specs alone
- **Durable** — likely still relevant later
- **Team-relevant** — useful for anyone on this project

**Knowledge-worthy** — any of:
- Research conclusions or evidence summaries worth reusing
- Operational lessons (ingest paths, API quirks, env setup gotchas)
- Glossaries, matrices, data-source maps, enrichment notes
- Design-direction notes that inform work but are not a formal ADR

Skip both when:
- Ephemeral task details only
- Already documented accurately in code or product specs
- Obvious best practices with no project-specific twist

One retrospective may produce **zero or more** ADRs and **zero or more** knowledge docs. Prefer quality over quantity.

### Step 3: Determine ADR Number

```bash
ls adr/
```

If `./adr/` does not exist, create it first.

Next number = highest existing `ADR-{NNN}-*.md` + 1 (zero-pad to 3 digits). If none exist, start at `001`.

### Step 4: Write ADRs (if any)

Create `adr/ADR-{NNN}-{short-title}.md`:

```markdown
# ADR-{NNN}: {Title}

## Status
Accepted | Deprecated | Superseded (by ADR-XXX)

## Context
[What situation or problem led to this decision?]

## Decision
[What was decided? Be specific.]

## Rationale
[Why this over alternatives? What alternatives were considered?]

## Consequences
[Results, trade-offs, risks, follow-up actions.]

## Date
{YYYY-MM-DD}
```

Use Chinese or English consistently with the rest of the project's documentation (same language within a single ADR).

### Step 5: Write Knowledge (if any)

#### 5.1 Ensure knowledge home exists

```bash
mkdir -p knowledge
```

Follow the layout in `./knowledge/` (`methods/`, `insights/`, `ops/`, `references/`, `sessions/`). If you add a new topical area, extend the index in `knowledge/README.md`.

For ops-only retrospective notes, prefer `knowledge/ops/{doc-slug}.md`.

#### 5.2 Choose topic folder + filename

- Group by topic area: `knowledge/{topic-area}/{doc-slug}.md`
- Use project-relevant folders only (e.g. `ops/`, `methods/`, `insights/`)
- Reuse an existing topic folder when the subject already belongs there
- Use the project frontmatter schema: `id`, `title`, `tags`, `created`, `updated`, `source`, `related[]`

#### 5.3 Knowledge doc template

```markdown
---
id: {doc-slug}
title: {Short title}
tags: {tag}, {tag}
created: "{YYYY-MM-DD}"
updated: "{YYYY-MM-DD}"
source: retrospective
related:
  - knowledge/{topic}/{other-doc}.md
  - adr/ADR-{NNN}-{short-title}.md
---

# {Title}

## Summary
[One short paragraph: what was learned and why it matters.]

## Evidence
- Sourced facts / user-provided context / what was tried

## Lesson / guidance
[Reusable takeaway for future work.]

## Links
- Related ADRs or knowledge docs
```

Rules:
- Knowledge is **not code truth** — product AC stays in requirements/design docs when those exist; decisions that bind the architecture go in ADRs
- Link ADRs from knowledge (and vice versa) when a lesson led to a decision
- Prefer amending an existing knowledge doc when the topic already exists; do not duplicate

### Step 6: Hand off, do not commit

Present the created and updated paths. **Do not run git commands.** The user manages their own repository state.

If a commit would be useful, give them the command to run rather than running it:

```bash
git add adr/ knowledge/ && git commit -m "docs: retrospective — ADR and knowledge updates"
```

## Output Format

```
## Retrospective Summary

**Completed:** [feature/task name]

**ADRs created:**
- adr/ADR-001-….md — [one-line why]

**Knowledge added/updated:**
- knowledge/{topic}/{doc}.md — [one-line what was learned]

**Skipped (not worth persisting):**
- [anything considered but not saved]
```

If nothing is ADR- or knowledge-worthy, say so clearly — an empty retrospective is valid.

## Important Notes

- Quality over quantity — 1 good ADR or knowledge note beats 5 mediocre ones
- ADRs are immutable records — never edit a past decision; create a new ADR that supersedes it
- Knowledge docs may be updated in place when facts change; bump `as_of` or `updated` and note what changed
- ADRs live in `./adr/`; retrospective knowledge lives in `./knowledge/` — both are committed team assets
- Use Chinese or English consistently with the rest of the project's documentation
