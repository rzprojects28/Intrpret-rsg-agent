# ADR-001: Domain-neutral by default, with agent/context.md as the specificity mechanism

## Status
Accepted

## Context

The upstream Cursor scaffold hardcoded a single vertical into its skills. `product-discovery` assumed a Singapore FMCG market — customer segments, channel assumptions, and example framings all pointed at that one domain. Any user outside it had to read past the wrong context to find the reusable structure underneath.

bogart is a solo-founder co-pilot with no fixed industry or product domain. The lock was inherited, not chosen.

## Decision

Strip the domain lock from the skills entirely. `product-discovery` and the rest are written domain-neutral.

Specificity is supplied from one place instead: `agent/context.md`, a user-filled file describing situation, domain, stage, working preferences, and constraints. It is design context, deliberately not loaded on every turn.

Until that file is filled in, `CLAUDE.md` instructs the agent to ask when domain context matters rather than assume one.

## Rationale

Three alternatives were available:

1. **Keep the vertical lock.** Immediately useful to one user in one market, wrong for everyone else. The project has no fixed domain, so this optimizes for a case that may never recur.
2. **Parameterize each skill.** Every skill grows its own domain variables. The domain assumptions then live in twelve places and drift apart.
3. **Neutral skills plus one context file.** One canonical home for domain context; skills stay about method.

Option 3 was chosen. It matches the project's existing split — always-on rules stay minimal, conditional detail lives elsewhere — and keeps a single place to update when the situation changes.

The cost is accepted deliberately: a neutral agent asking a question is less immediately impressive than a specific agent guessing correctly, but a specific agent guessing *incorrectly* is worse than both.

## Consequences

- Skills are reusable across any domain; `product-discovery` no longer misleads users outside FMCG.
- Quality now depends on `agent/context.md` being filled. As of 2026-07-29 it is still the shipped template, every field a bracketed placeholder. Filling it is the highest-leverage open item in the project.
- While it stays empty, the "ask, do not assume" instruction in `CLAUDE.md` is load-bearing rather than a safety net. If that instruction is ever trimmed for token budget, this decision silently degrades into guessing.
- A stale `agent/context.md` is worse than an empty one, because the agent will act on it confidently. The file says so itself; it needs revisiting when the situation changes.

## Date
2026-07-29
