---
id: getting-started
title: Getting started with bogart
tags: meta, onboarding
created: "2026-07-27"
updated: "2026-07-28"
source: repo seed
related:
  - continuous-learning-gate
---

# Getting started

This note lives in `./knowledge/`, bogart's canonical store. It is a plain file — bogart reads it with `Read` and searches the folder with `Grep`. There is no server or index involved.

## First things to try

1. **Ask what it knows.** "What's in the knowledge base about design thinking?" — bogart greps this folder and answers from what it finds.
2. **Teach it something.** `/learn-knowledge <topic>` researches, shows you the evidence, waits for your confirmation, then stores a note here.
3. **Give it a capability.** `/build-skill` interviews you and writes a new skill into `.claude/skills/`.
4. **Do real work.** Ask for a discovery brief, a research memo, or a feature. Skills trigger on their own when they match.

## What makes bogart different

The **task completion gate**. When a substantial piece of work finishes, bogart stops and asks whether to close it — separately from any "yes" you gave along the way. Only after you choose **Mark task done** does it save the session context and write lessons back into `knowledge/` and `adr/`.

The intent is compounding: today's work should make next month's cheaper. Answer honestly at the gate; "Not yet" is a normal answer.

## Setup

Fill in [`agent/context.md`](../../agent/context.md). Until you do, bogart works from general principles and asks when domain matters — safe, but less useful than it could be.

## Where things live

| What | Where |
| --- | --- |
| Always-on rules | `CLAUDE.md` |
| Skills | `.claude/skills/` |
| Commands | `.claude/commands/` |
| Knowledge | `knowledge/` |
| Decisions | `adr/` |

New skills and commands load in fresh sessions — restart to pick them up.
