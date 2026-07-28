---
name: research-ops
description: >
  Evidence-first research with explicit evidence boundaries. Use when the user wants current facts,
  comparisons, enrichment, or a recommendation grounded in public evidence plus local context — or says
  research, look up, compare, what's the latest, or is this still true. Separates sourced fact from
  inference so conclusions can be audited.
---

# Research Ops

Use this when the user asks to research current information, compare options, or turn evidence into a recommendation.

The value here is not the searching — it is the **evidence discipline**. Every important claim is labeled by where it came from, so the user can tell which parts of a conclusion rest on sources and which rest on your reasoning.

## Tools

| Tool | Use for |
| --- | --- |
| `Grep` / `Glob` / `Read` over `./knowledge/` | What you already hold — always check first |
| `WebSearch` | Discovering current public information |
| `WebFetch` | Reading a specific source in full once search surfaces it |

Search `./knowledge/` before going to the web. If a stored note already answers the question, say so and cite the path rather than re-researching from scratch.

## When to use

- The user mentions "research", "look up", "compare", or "what's the latest"
- The answer depends on current public information
- The user has provided evidence and wants it folded into a recommendation
- A claim in the knowledge base may have gone stale

## Guardrails

- Do not answer a current-information question from memory when a search is cheap.
- Do not start a heavy research flow when local files or the knowledge base already answer it.
- Always distinguish sourced fact, user-provided context, inference, and recommendation.
- Time-sensitive answers carry concrete dates — both the source's date and the date you searched.

## Workflow

### 1. Start from what the user already gave you

Normalize their material into:
- Facts already supported by evidence
- Claims that need verification
- Open questions

If they have already built part of the model, extend it. Do not re-analyze from scratch.

### 2. Classify the request

Pick the path before searching:
- Quick factual answer
- Comparison or decision memo
- Enrichment of a specific entity
- A question likely to recur

### 3. Take the lightest effective path

1. Check `./knowledge/` with `Grep`.
2. `WebSearch` for discovery — several focused queries beat one broad one.
3. `WebFetch` the sources that matter, rather than trusting search snippets.
4. Escalate to multi-source synthesis only when the question genuinely needs it.

### 4. Report with evidence boundaries

State for each important claim whether it is a sourced fact, user-provided context, inference, or recommendation. Put dates on anything time-sensitive.

### 5. Decide whether it should recur

If the user is likely to ask this again, say so and suggest storing it via `/learn-knowledge`, so the next answer starts from the knowledge base instead of a blank search.

## Output format

```text
Question type
- Factual / Comparative / Enrichment / Recurring

Evidence
- Sourced facts (with source and date)
- User-provided context
- From ./knowledge/ (with path)

Inference
- Conclusions drawn from the above

Recommendation
- Answer or next actions
- Whether this is worth storing
```

## Common pitfalls

- Mixing inference into sourced facts without labeling
- Ignoring evidence the user already provided
- Running a heavy research path for something local files answer
- Giving time-sensitive answers with no dates
- Citing a search snippet without fetching the source

## Validation

Before sending, confirm: important claims are labeled by evidence type, time-sensitive output carries dates, and the recommendation matches the depth of research actually performed.

## Related

- **knowledge-ops** — when results should persist
- `/learn-knowledge` — the full research-to-stored-note cycle
- **product-discovery** — when research feeds opportunity scoping
