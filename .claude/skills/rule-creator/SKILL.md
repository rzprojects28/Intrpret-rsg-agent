---
name: rule-creator
description: >
  Scan the project's skills for principles that recur across several of them and distill those into
  CLAUDE.md rules. Use for periodic rule maintenance, after adding new skills, when CLAUDE.md feels
  incomplete relative to how you actually work, or when the user says distill rules, review my rules,
  or /rule-creator.
---

# Rule Creator

Read the skills, find principles that show up in several of them, and promote those into `CLAUDE.md`.

The premise: a principle stated in one skill belongs in that skill. A principle repeated across three skills is really a rule, and belongs where it applies to everything.

Method is **deterministic collection, then judgment** — enumerate exhaustively with tools, then read the full text and decide. Do not sample.

## When to use

- Periodic maintenance, or after adding skills
- When you notice yourself repeating the same guidance across skills
- When `CLAUDE.md` has drifted from how the project actually works

## Phase 1 — Inventory

```bash
ls .claude/skills/*/SKILL.md
```

Read every `SKILL.md` in full, and read `CLAUDE.md` in full. The whole corpus is small enough to hold at once — no grep pre-filtering, no sampling, no subagents.

Report what was scanned before analyzing: skill count, and the section headings currently in `CLAUDE.md`.

## Phase 2 — Extract and match

### Inclusion criteria — all four must hold

1. **Appears in 2 or more skills.** A principle in one skill stays in that skill.
2. **Actionable.** Expressible as "Do X" or "Do not Y" — not "X is important."
3. **Clear violation risk.** You can state in one sentence what goes wrong when it is ignored.
4. **Not already covered** in `CLAUDE.md` — including the same idea under different wording.

### Verdicts

| Verdict | Meaning |
| --- | --- |
| **Append** | Add to an existing `CLAUDE.md` section |
| **Revise** | Existing rule text is wrong or insufficient — propose a fix |
| **New section** | Warrants its own section |
| **Already covered** | `CLAUDE.md` handles it, wording aside |
| **Too specific** | Keep it at skill level |

### The budget guardrail

`CLAUDE.md` is always-on context. Every line costs tokens on every single turn, forever.

So this skill has a second job beyond adding: **when proposing anything substantial, also propose what to cut.** Look for text that has gone stale, duplicates another section, or states something the model does reliably anyway. A candidate that only adds is a weaker candidate than one that adds and removes.

If `CLAUDE.md` has grown past roughly 200 lines, treat trimming as the primary task and additions as secondary.

### Per-candidate output

```json
{
  "principle": "1-2 sentences, 'Do X' / 'Do not Y' form",
  "evidence": ["skill-name: §section", "skill-name: §section"],
  "violation_risk": "one sentence",
  "verdict": "Append | Revise | New section | Already covered | Too specific",
  "target": "CLAUDE.md §section",
  "confidence": "high | medium | low",
  "draft": "exact text to insert",
  "offset": "what to cut in exchange, or 'none — net addition'"
}
```

## Phase 3 — Review and apply

Present a summary table, then per-candidate detail:

```
# Rule Creator Report

Skills scanned: {N} | CLAUDE.md: {M} lines, {K} sections | Candidates: {J}

| # | Principle | Verdict | Target | Confidence |
|---|-----------|---------|--------|------------|
| 1 | ... | Append | §Task completion gate | high |
| 2 | ... | Revise | §Writing style | medium |
| 3 | ... | Too specific | — | — |
```

The user approves, modifies, or skips **by number**.

**Never edit `CLAUDE.md` without explicit approval.** It governs every turn — a bad rule silently degrades all future work, and the damage is hard to trace back.

## Quality bar

Good:

> **Append** to `CLAUDE.md` §Knowledge conventions:
> "Search the knowledge base before creating a note; update an existing note rather than adding a near-duplicate."
> Evidence: `knowledge-ops` §Deduplicate, `retrospective` §5.3, `/learn-knowledge` step 5.
> Violation risk: the knowledge base accumulates near-duplicates and stops being trustworthy as a single source.
> Offset: none — net addition of 1 line.

Bad:

> Append to CLAUDE.md: add knowledge management principles

## Design principles

- **What, not how.** Principles go in rules; procedures and examples stay in skills.
- **Link back.** Draft text can cite `See skill: <name>` so the detail remains findable.
- **Anti-abstraction guard.** The three filters — 2+ skills, actionable, violation risk — exist to keep vague aspirations out of always-on context.
- **Subtraction is a valid outcome.** A run that only removes stale rules did useful work.
