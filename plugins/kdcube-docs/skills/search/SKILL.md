---
name: search
description: Search the KDCube docs and answer a specific question with citations
argument-hint: <question>
allowed-tools: [Read]
---

# KDCube Doc Lookup

User invocation: `/kdcube-docs:search <question>`

This is a user-invoked slash command for fast, citation-backed lookups against the KDCube docs. It is a thin wrapper that biases Claude toward the docs-first workflow.

## Arguments

- `$ARGUMENTS` — the user's free-form question. If empty, ask for one.

## Workflow

1. Call `kdcube-dev:search_knowledge(query="$ARGUMENTS", top_k=10)`.
2. If the top hit looks relevant, `kdcube-dev:read_knowledge(path=<top hit path>)`.
3. If the answer needs cross-referencing, read the second hit too.
4. Answer the user's question concisely, quoting or paraphrasing from the docs.
5. **Cite every claim** with the `ks:docs/...` path inline, e.g. `(ks:docs/quick-start-README.md)`.
6. If `search_knowledge` returns nothing useful, broaden the keywords once. If still nothing, tell the user the doc set doesn't appear to cover their question — do not invent an answer.

## Anti-patterns

- Answering from training memory and then "verifying" with a search. The search must be the source.
- Reading more than 2–3 docs for a single question. If you need more, stop and recommend the user invoke the full `kdcube` workflow skill instead.
- Citing a `ks:docs/...` path you didn't actually read in this turn.
- Returning a "based on my knowledge of KDCube…" answer if the MCP is unreachable. Stop and tell the user the MCP is down.

## When this skill is the wrong tool

If the user is **building** something (a bundle, a wrapper, a config), invoke the broader `kdcube` workflow skill — the lookup command is for read-only questions.
