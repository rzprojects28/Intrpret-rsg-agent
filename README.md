# bogart

A solo-founder co-pilot for Claude Code. Discover, research, remember, build, ship.

bogart is a **configuration, not a program** — markdown files that Claude Code reads. Nothing to install, no build step, no dependencies.

## Use it

Open this folder in Claude Code. That is the whole setup.

```
/learn-knowledge <topic>    research a topic and store what you learn
/build-skill                interview yourself into a new skill
```

Or just ask for work. Skills trigger on their own when they match the task.

**Worth doing first:** fill in [`agent/context.md`](agent/context.md) with your actual situation. Until you do, bogart works from general principles and asks when domain matters — safe, but less useful than it could be.

## What's in here

| Path | Holds |
| --- | --- |
| `CLAUDE.md` | Always-on rules: identity, completion gate, writing style, working discipline |
| `.claude/skills/` | 12 skills, loaded on demand |
| `.claude/commands/` | Slash commands |
| `knowledge/` | Durable, git-tracked knowledge — the canonical store |
| `agent/` | Design docs: who bogart works for, how it is assembled |
| `adr/` | Decision records (created on first retrospective) |

### The skills

**Working:** `product-discovery`, `research-ops`, `knowledge-ops`, `retrospective`, `samectx`, `agent-builder`, `skill-creator`, `rule-creator`, `fullstack`, `frontend-design`, `rag-implementation`

**Needs setup:** `skill-lookup` — requires the prompts.chat MCP server, not configured here. The skill says so and declines rather than inventing results.

## The completion gate

The one opinionated mechanism. After a substantial task, bogart stops and asks whether to close it — separately from any confirmation you gave along the way. Only on **Mark task done** does it save session context to memory and write lessons into `knowledge/` and `adr/`.

The intent is compounding: today's work should make next month's cheaper. Why it is built this way, including what failed before: [knowledge/ops/continuous-learning-gate.md](knowledge/ops/continuous-learning-gate.md).

## Deliberately not built

No MCP server, no workflow engine, no web UI, no vector search. Claude Code reads and writes files natively, so each of those would add a layer without adding capability. Reasoning in [agent/architecture.md](agent/architecture.md).

## Origin

Adapted from [ethanhuangcst/build-your-agent](https://github.com/ethanhuangcst/build-your-agent), a Cursor-targeted "Work Agent" scaffold. This version is ported to Claude Code, stripped of a Singapore/FMCG domain lock, and pruned of documentation for components that were specified but never built. The upstream originals remain in git history at commit `e10a815`.
