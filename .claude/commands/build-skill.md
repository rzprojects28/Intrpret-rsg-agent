---
description: Interview the user, compose a skill brief, and author a new skill under .claude/skills/
argument-hint: [skill-name-hint]
---

# Build Skill

Interactive workflow: gather five inputs → compose a brief → name it → author the skill → close.

Skill name hint (may be empty): **$ARGUMENTS**

Run the steps in order. Ask **one question at a time** and wait for the answer — do not batch the five questions into one message, and do not invent answers to move faster.

Keep a running **Skill build brief** in the thread as you go; you will assemble it in Step 6.

---

## Steps 1–5 — Gather the five inputs

Ask each in chat, in order, waiting for a reply each time.

**Step 1 — Context.** Ask:

> What context or background should this skill assume? Write it in first person, as yourself.
>
> Example: *I run weekly pricing reviews for a two-person SaaS product and keep the results in a spreadsheet nobody else reads.*

**Step 2 — Role.** Ask:

> What role should the AI play when this skill runs? Include seniority, domain, and relevant expertise.
>
> Example: *You are a pricing strategist who has worked on early-stage B2B SaaS and is comfortable with unit economics.*

**Step 3 — Knowledge.** Ask:

> What knowledge should this skill use? List `./knowledge/` areas, topics, file paths, or external sources it should search.

**Step 4 — Behavior.** Ask:

> What specific behavior do you expect when this skill is active? Outputs, steps, tone, tools to use, workflows to call.

**Step 5 — Rules.** Ask:

> What rules must the AI follow in its results? Format, citations, language, compliance, things to avoid.

Record each answer in the brief as **Q1 Context**, **Q2 Role**, **Q3 Knowledge**, **Q4 Behavior**, **Q5 Rules**.

## Step 6 — Compose the skill brief

Produce this block and show it to the user:

```markdown
# Situation
<!-- Q1 in 2-4 sentences, converted from first person to third person -->

# Role
<!-- Q2 -->

# Knowledge
<!-- Q3: paths and search strategy -->

# Task
<!-- The default task below, plus Q4 behavior -->

# Rule
<!-- Q5 as bullet rules -->
```

**Default task** — always include, adapting the wording only if the user's answers contradict it:

> Based on the situation, play the specified role. Search `./knowledge/` for the knowledge you need. If it is missing or stale, run `/learn-knowledge` (or the **research-ops** skill) to add it before answering. Then execute the specific behaviors described above.

Then call **AskUserQuestion**:

> Does this skill brief look correct before we name it and author the skill?

| Option | Meaning |
| --- | --- |
| **Approve** | Continue to naming |
| **Revise** | User will specify changes in chat |

On **Revise**, update the block and ask again.

## Step 7 — Name the skill

Ask in chat:

> Choose a skill folder name — kebab-case, 2–32 characters, letters, numbers and hyphens only. This becomes `.claude/skills/<name>/`.

If a hint was passed as an argument, offer it as the default.

Validate against `^[a-z0-9][a-z0-9-]{0,30}[a-z0-9]$`. Reject and re-ask if it does not match, or if `.claude/skills/<name>/` already exists.

## Step 8 — Author the skill

Confirm in chat: the slug, and that the brief is ready.

Then follow the **skill-creator** skill to author `.claude/skills/<slug>/SKILL.md`, using the composed brief as the body outline.

Frontmatter must include `name` and `description`. Make the `description` explicit about *when to trigger* — that field is the primary mechanism deciding whether the skill ever gets used.

## Step 9 — Verify it registers

1. Confirm `.claude/skills/<slug>/SKILL.md` exists and its frontmatter parses.
2. Tell the user the skill loads in new sessions; the current session may need a restart to see it.

There is no deploy step — `.claude/skills/` is the live location, not a staging one.

## Step 10 — Task completion gate

Call **AskUserQuestion**:

> Close this major task?

| Option | Meaning |
| --- | --- |
| **Mark task done** | Run samectx and retrospective |
| **Not yet** | More work on this skill |
| **Pause here** | Stop without sync or retrospective |

## Step 11 — After "Mark task done" only

1. **samectx** skill — key tasks, keypoints, decisions to Claude Code memory. No secrets.
2. **retrospective** skill — ADRs to `./adr/`, lessons to `./knowledge/`; present the Retrospective Summary.

Do not commit anything to git unless the user explicitly asks.
