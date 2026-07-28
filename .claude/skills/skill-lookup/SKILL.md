---
name: skill-lookup
description: >
  Search, retrieve, and install Agent Skills from the prompts.chat registry via MCP (search_skills,
  get_skill). Use when the user wants to find skills, browse catalogs, install a skill, extend agent
  capabilities, or asks "is there a skill for X". Search before authoring a new skill from scratch.
  Requires the prompts.chat MCP server to be connected.
license: MIT
---

# Skill Lookup

## Prerequisites — read first

This skill calls `search_skills` and `get_skill`, which come from the **prompts.chat MCP server**. That server is **not configured in this project**.

Check whether it is available before doing anything else. If the tools are not there, tell the user plainly:

> The skill registry needs the prompts.chat MCP server connected. It is not set up for this project yet. Add it to `.mcp.json` (or via `claude mcp add` in an interactive session), then this skill will work.

Then stop, or offer the **skill-creator** skill instead to author one locally. **Do not invent search results** — a fabricated skill listing is worse than no answer.

## Workflow

1. Search with `search_skills` for the user's request.
2. Present title, description, author, files, category and tags, and link.
3. On selection, call `get_skill` for the full file contents.
4. Install under `.claude/skills/{slug}/` for this project, or `~/.claude/skills/{slug}/` to make it available everywhere.
5. Confirm the install and summarize what the skill does and when it triggers.

## Example

```
search_skills({"query": "code review", "limit": 5, "category": "coding"})
get_skill({"id": "abc123"})
```

## MCP tools

- `search_skills` — keyword search; optional `limit`, `category`, `tag`
- `get_skill` — metadata and all files, by `id`

## Install layout

**This project:**

```
.claude/skills/{slug}/SKILL.md
.claude/skills/{slug}/...   # references, scripts, assets
```

**Global, across all projects:**

```
~/.claude/skills/{slug}/SKILL.md
~/.claude/skills/{slug}/...
```

Prefer project-local unless the user wants the skill everywhere. After installing, read back the `SKILL.md` frontmatter (`name`, `description`) to confirm the triggering text survived intact.

New skills load in fresh sessions — the current one may need a restart to see them.

## Guidelines

- Search the registry before recommending a custom-authored skill
- Show readable results with file counts
- Explain activation — the `description` field drives when an agent loads the skill
- Never fabricate registry results when the MCP server is unavailable

## Related

- **skill-creator** — author a skill locally when the registry has nothing suitable
- `/build-skill` — guided interview that ends in a new skill
