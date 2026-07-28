---
description: Research a topic end-to-end and store it as a durable note in ./knowledge/
argument-hint: [topic]
---

# Learn Knowledge

Interactive research workflow: clarify → research → confirm → store → close.

Topic (may be empty): **$ARGUMENTS**

Run the steps in order. This is a multi-turn workflow — do not try to complete it in a single turn, and do not skip a confirmation because you can guess the answer.

---

## Step 1 — Clarify the topic

If no topic was given above, ask in chat:

> What knowledge do you want to learn? Describe the topic, scope, and any constraints (market, geography, timeframe).

Wait for the reply before researching. Treat everything the user adds in this thread as **user-provided evidence** for the research step.

## Step 2 — Research

Follow the **research-ops** skill. Use `WebSearch` and `WebFetch` for current public evidence, and `Grep` / `Read` over `./knowledge/` for what you already hold.

Search `./knowledge/` **first** — if the topic already has a note, you are updating it, not creating one.

## Step 3 — Summarize and ask for confirmation

Present the summary in the research-ops output format:

```text
Question type
- Factual / Comparative / Enrichment / Monitoring

Evidence
- Sourced facts (with dates where time-sensitive)
- User-provided context

Inference
- Conclusions drawn from the evidence

Recommendation
- Answer or next actions
```

Then ask exactly:

> Are you satisfied with the research result? Say **yes** to confirm, or give me feedback.

Do **not** write to `./knowledge/` until the user confirms.

**This yes means "proceed to store" only.** It does not mark the task done and does not authorize samectx or retrospective — see the task completion gate in `CLAUDE.md`.

## Step 4 — Revision loop

While the user has not confirmed:

1. Fold their feedback and any new messages into the evidence set.
2. Run **research-ops** again — lighter or deeper as the feedback warrants. Do not skip fresh verification when the feedback changes the question.
3. Return to Step 3 with an updated summary and the same question.

Repeat until confirmed. Only then continue.

## Step 5 — Store

Follow the **knowledge-ops** skill:

1. Search `./knowledge/` again to dedupe — update an existing note if the topic is already covered.
2. Synthesize one durable markdown note with frontmatter: `id`, `title`, `tags`, `created`, `updated`, `source`, `related[]`.
3. Write to `./knowledge/insights/{date}-{slug}.md`, or `./knowledge/methods/` if the content is procedural.
4. Update the index table in `knowledge/README.md` if the note is significant.
5. Tell the user the file path and a one-line summary of what was stored.

## Step 6 — Task completion gate

Storage is complete, which is **not** the same as the task being done. Now run the gate defined in `CLAUDE.md`:

Call **AskUserQuestion**:

> Close this major task? (Separate from the research confirmation you already gave.)

| Option | Meaning |
| --- | --- |
| **Mark task done** | Run samectx and retrospective |
| **Not yet** | More work on this task |
| **Pause here** | Stop without sync or retrospective |

Only **Mark task done** authorizes the next two steps.

## Step 7 — After "Mark task done" only

1. **samectx** skill — extract key tasks, keypoints, and decisions; write to Claude Code memory. No secrets.
2. **retrospective** skill — ADRs to `./adr/`, lessons to `./knowledge/`; present the Retrospective Summary.

Do not commit anything to git unless the user explicitly asks.
