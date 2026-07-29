---
id: skill-system-fails-silently
title: A skill system has no referential integrity — broken skills fail silently
tags: agent-ops, skills, verification, claude-code
created: "2026-07-29"
updated: "2026-07-29"
source: retrospective — porting the upstream Cursor scaffold to Claude Code
related:
  - continuous-learning-gate
  - adr/ADR-002-slash-commands-replace-workflow-engine.md
---

# A skill system has no referential integrity — broken skills fail silently

## Summary

Nothing validates a skill directory. A `SKILL.md` that omits frontmatter, names a skill that does not exist, or sits in a wrongly-named folder produces no error at any point — not at load, not at match time, not at invocation. The agent simply does less than the files claim, and the gap is invisible unless someone reads every file and checks each reference by hand.

Porting the upstream scaffold surfaced four distinct failure modes across twelve skills. All four were silent.

## Evidence

Found in the pre-port tree at commit `e10a815`:

| Failure | Instance | Effect |
| --- | --- | --- |
| No YAML frontmatter | `samectx/SKILL.md` began at `# samectx` with no `name` or `description` | Never registered as a skill at all. It could not trigger, and nothing reported this. |
| Routes to nonexistent skills | `research-ops` instructed the agent to bring in `exa-search`, `deep-research`, `market-research`, `lead-intelligence` | None of the four existed in the tree. The skill described a stack that was not there. |
| Directory naming | `Frontend Design/` and `Fullstack/` — spaces and capitals | Inconsistent with every other skill's kebab-case; a discovery hazard rather than a hard failure. |
| Dangling rule references | Skills citing rules that were never written | Instructions pointing at nothing. |

Seven dangling references in total were resolved during the port. The `research-ops` case is the clearest: its opening line positioned the skill as "an operational wrapper around the repo research stack" and named three of the four missing skills as the things it wrapped. The wrapper was real; the stack was not.

A second pattern compounded it: `knowledge-ops` documented six storage layers when two existed. Specification had drifted from implementation with nothing forcing them back together.

## Lesson / guidance

Treat the skill tree as unverified until checked. After any change to skills, commands, or rules, confirm three things explicitly — none are checked for you:

1. **Frontmatter present and well-formed.** Every `SKILL.md` needs `name` and `description`. Missing frontmatter is the worst failure of the four because the skill is absent rather than wrong, so it never appears in any listing that might expose the problem.
2. **Directory names are kebab-case** and match the `name` field.
3. **Every cross-reference resolves.** Grep each skill for names of other skills, rules, tools, and commands, then confirm each target exists. This catches both dangling references and documentation describing capability that was specified but never built.

The general form: an agent's capability surface is defined by prose that nothing type-checks. Documentation drift here does not degrade gracefully — it produces an agent that confidently follows instructions to use things that are not there.

Cheapest guard is a periodic manual sweep. If skills start changing often, this is the natural candidate for a validation script or a `SessionStart` hook.

## Links

- [ADR-002](../../adr/ADR-002-slash-commands-replace-workflow-engine.md) — the workflow engine was the largest instance of specification without implementation
- [continuous-learning-gate](continuous-learning-gate.md) — the related lesson that an always-on rule states an expectation but does not guarantee the behavior
