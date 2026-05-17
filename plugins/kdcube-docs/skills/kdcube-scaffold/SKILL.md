---
name: kdcube-scaffold
description: Scaffold a new KDCube bundle with the right shape for the surfaces you need (chat, API, widget, MCP, cron)
argument-hint: <bundle-name>
allowed-tools: [Read, Write, Bash, Glob, Grep]
---

# KDCube Bundle Scaffolder

User invocation: `/kdcube-docs:kdcube-scaffold <bundle-name>`

This is a user-invoked slash command that generates a new KDCube bundle scaffold grounded in the **live KDCube docs**, not from memory.

## Arguments

- `$ARGUMENTS` — the bundle name the user wants (e.g. `task-tracker.bundle@0-1`). If empty or missing, ask the user.

## Workflow

### 1. Gather requirements (3 short questions max)

Ask the user:

1. **What surfaces does this bundle need?** Multi-select: `chat / API (operations) / API (public webhook) / widget / full UI / MCP endpoint / cron / background jobs`.
2. **What language is the business logic in?** Default: `Python`. If they say something else, note that the KDCube bundle adapter is Python; the business logic can live in any process invoked from the adapter (subprocess, HTTP, container).
3. **Is this wrapping existing code, or starting fresh?** This changes the scaffold — wrapping means a thinner adapter and an explicit "where does the existing code go" note.

Use plain text questions; do not require a structured-input runtime. If `AskUserQuestion` is available, prefer it.

### 2. Pull the live docs

**Mandatory.** Before writing any file:

```
kdcube-dev:read_knowledge(path="ks:docs/quick-start-README.md")
kdcube-dev:read_knowledge(path="ks:docs/sdk/bundle/build/how-to-write-bundle-README.md")
kdcube-dev:read_knowledge(path="ks:docs/sdk/bundle/build/how-to-configure-and-run-bundle-README.md")
```

If the MCP is unreachable, stop and tell the user. Do **not** scaffold from training data — decorator names and signatures change, and a scaffold authored from memory teaches the user to hallucinate.

Also fetch the nearest reference pattern based on the chosen surfaces:

- chat / agentic workflow → `kdcube-dev:read_knowledge(path="ks:docs/sdk/bundle/versatile-reference-bundle-README.md")`
- MCP endpoint → search: `kdcube-dev:search_knowledge(query="bundle-served MCP endpoint")` and read the top result
- cron + on_job → search: `kdcube-dev:search_knowledge(query="cron and on_job")` and read the top result

### 3. Generate the bundle layout

Standard layout (confirm against the live `how-to-write-bundle-README.md` — adjust if the docs have moved on):

```
<bundle-name>/
├── entrypoint.py                          # decorated surfaces
├── tools_descriptor.py                    # bundle-local agent tools (omit if no @on_message/@agentic_workflow)
├── skills_descriptor.py                   # bundle-local agent skills (omit if no @on_message/@agentic_workflow)
├── config/
│   ├── bundles.template.yaml              # non-secret props template
│   └── bundles.secrets.template.yaml      # secrets template
├── interface/
│   └── README.md                          # public routes, widgets, MCP, jobs, config — for operators
├── tests/
│   ├── test_imports.py                    # module loads cleanly
│   └── test_descriptors.py                # tools_descriptor and skills_descriptor enumerate
└── README.md                              # bundle-level docs for the human installing it
```

### 4. Inject decorators based on selected surfaces

Pull the canonical decorator signatures from `how-to-write-bundle-README.md` and adapt the bundle's `entrypoint.py` to use the surfaces the user picked. **Do not paste decorator signatures from memory** — copy from the live doc each time.

Common surface → decorator map (verify in docs):

| Surface | Decorator |
| --- | --- |
| Chat-driven message handler | `@on_message` or `@agentic_workflow` |
| Authenticated operation API | `@api(route="operations", ...)` |
| Public webhook | `@api(route="public", public_auth="bundle", ...)` |
| MCP server | `@mcp(alias=..., route=..., transport=...)` |
| UI widget | `@ui_widget(...)` |
| Scheduled scan | `@cron(alias=..., cron_expression=..., span=...)` |
| Background job worker | `@on_job` |

Be explicit about `async def` requirements (see `how-to-write-bundle-README.md` for the current rule — many decorated methods must be async).

### 5. Wire up the config templates

- `config/bundles.template.yaml` — non-secret props. Include placeholders for what the user said they need.
- `config/bundles.secrets.template.yaml` — explicit list of secrets, with `null` values and inline comments explaining what to fill.

Never include real secret values. The user must provide them through their own descriptor set.

### 6. Document and verify

- Write `interface/README.md` listing every public route, widget, MCP alias, cron alias, and on_job handler, plus required config keys.
- Write the top-level `README.md` with: install path (local-path or git registration), required descriptor entries, local-test commands (`kdcube init`, `kdcube start`, `kdcube reload <bundle_id>`).
- Suggest the user run the shared bundle test suite as documented in `ks:docs/sdk/bundle/build/how-to-test-bundle-README.md`.

### 7. Hand off

End with:

1. The exact path the bundle was written to.
2. The CLI commands needed to register and reload it (pulled verbatim from `quick-start-README.md` section 5).
3. The Tier-1 reading list, in case the user wants to learn the shape before iterating.

## Anti-patterns

- Scaffolding without first reading the live docs.
- Inventing decorator keyword arguments. Copy from the doc, don't paraphrase.
- Mixing secrets and non-secrets in one template.
- Calling `kdcube start` before `kdcube init` in any example output.
- Generating a `skills/` subdirectory inside the bundle without explaining that **bundle skills are not Claude Code skills** — they are domain-instruction units for the ReAct agent.
