# ADR-002: Two slash commands replace the workflow engine and its 16 prompt files

## Status
Accepted

## Context

The upstream scaffold defined a YAML workflow engine and never built it. What existed was the specification plus its data:

- 3 workflow YAML files — `learn-knowledge`, `build-skill`, and an `example/hello`
- 16 numbered prompt files — 10 steps for `build-skill`, 5 for `learn-knowledge`, and 1 shared `_shared/task-completion-gate.md`
- A second, byte-identical copy of the same material under `agent/define-workflows/`, kept as a draft/production mirror

No runner existed to execute any of it. The numbered steps were sequencing instructions for a program that was never written.

## Decision

Collapse the whole structure into two slash commands:

- `.claude/commands/learn-knowledge.md`
- `.claude/commands/build-skill.md`

Delete the engine specification, the 16 numbered prompt files, the `example/hello` workflow, and the `agent/define-workflows/` mirror. The shared completion-gate prompt moves into `CLAUDE.md` as an always-on section.

## Rationale

Alternatives considered:

1. **Build the workflow engine.** It would add a scheduler and a step format on top of a client that already reads markdown and calls tools. The layer adds no capability the runtime lacks.
2. **Port the 16 prompts one-to-one as 16 commands.** This preserves granularity nobody asked for. Most numbered steps were a single question apiece; as commands they would each require the user to know which step they are on, which is the engine's job — and there is no engine.
3. **Collapse to one command per real workflow.** Chosen.

The deciding observation is that Claude Code loads skills on description match, so per-step prompt files were duplicating work the skill descriptions already do. Only two of the three workflows were real, and both are interview scripts: ask, confirm, store. That fits a single command file each.

Moving the completion gate out of `_shared/` and into `CLAUDE.md` was a scope change, not just relocation. In the workflow model the gate only ran inside a workflow; as an always-on rule it applies to every major task, including work that never invokes a command.

## Consequences

- Step-level granularity is gone. Resuming a `build-skill` interview at step 7 is no longer addressable; the command runs as one flow.
- The two command files are longer than any individual prompt file was. This is the intended trade: one file to read instead of ten to sequence.
- The draft/production mirror is gone. Edits now happen in one place, removing the possibility of the two copies diverging — they were byte-identical, so the split was providing no isolation in practice.
- The completion gate now applies more broadly than before. That is the intent, and it is why `CLAUDE.md` carries the checklist for what counts as a major task.
- Reversing this decision means writing the runner first. The specification is recoverable from git history at commit `e10a815`.

## Date
2026-07-29
