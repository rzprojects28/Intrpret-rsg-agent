---
id: continuous-learning-gate
title: Why the task completion gate is built the way it is
tags: continuous-learning, completion-gate, agent-ops
created: "2026-07-27"
updated: "2026-07-28"
source: retrospective; carried forward from the upstream Cursor project
related:
  - getting-started
---

# Why the task completion gate is built the way it is

## Summary

An always-on rule saying "run a retrospective when the task is done" **did not work**. In the original Cursor project, a completed research run stored its note to `./knowledge/` and stopped. No session sync, no retrospective.

The diagnosis: the user had said **yes** earlier in that session — but to *"is this research good enough to store?"*. The agent treated that yes as covering task completion too. One confirmation silently absorbed another.

This is the origin of the gate's central rule: **research yes is not task-done yes.**

## Lessons

1. **A separate, structured confirmation is required.** Not a plain chat "can I mark this done?", which collides with every other yes/no in the thread. It must be a distinct options prompt — **Mark task done** / **Not yet** / **Pause here** — and only the first option authorizes anything.

2. **Rules alone do not invoke tools.** An always-on instruction states an expectation; it does not guarantee the model acts on it at the right moment. If the gate gets skipped, it is because the turn ended without the question being asked.

3. **Prompts must name the distinction explicitly.** Every step that asks for a confirmation should say what that confirmation does and does not authorize.

4. **Split the outputs.** Session context goes to memory; decisions go to `./adr/`; reusable lessons go to `./knowledge/` (usually `ops/`). Three destinations, chosen by what kind of thing was learned.

## Port to Claude Code (2026-07-28)

The gate moved from Cursor to Claude Code with the rest of the project. What changed:

| Was | Now |
| --- | --- |
| `continuous-learning.mdc` + `99-task-completion-gate.mdc` | One section in `CLAUDE.md` |
| `AskQuestion` tool | `AskUserQuestion` |
| YAML workflow steps loading the gate | Steps in `.claude/commands/learn-knowledge.md` and `build-skill.md` |
| `.cursor/hooks.json` enforcing it mechanically | **Not ported** — see below |
| `samectx sync` CLI | The rebuilt **samectx** skill, writing to Claude Code memory |

The reasoning above survives the port unchanged, because it was about how confirmations get conflated, not about any particular IDE.

## Open question: hooks

The Cursor version added `hooks.json` — counting file edits, injecting a follow-up on stop — precisely because rules alone proved unreliable. That mechanism was **not** carried over. Claude Code's equivalent is a `Stop` hook in `.claude/settings.json`, and the original hook scripts are bash, which needs rewriting for Windows.

The current bet is that the always-on `CLAUDE.md` instruction is enough on its own. **If the gate starts getting skipped in practice, that bet was wrong** — add the `Stop` hook then. Worth watching rather than assuming.
