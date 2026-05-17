---
name: kdcube
description: Use when the user asks about KDCube, kdcube.copilot, KDCube bundles, the KDCube platform, the KDCube SDK, or ReAct agent runtime — also when the user is editing a file named entrypoint.py with @api / @mcp / @cron / @on_job / @on_message / @ui_widget / @agentic_workflow decorators, or working under a directory matching kdcube.copilot@* or *.bundle@*. Guides Claude through bundle authoring, configuration, deployment, and platform/SDK questions grounded in live docs. Always consults the kdcube-dev MCP (search_knowledge / read_knowledge) before answering — never answers from training memory, because KDCube decorator names, descriptor keys, and CLI flags change between releases.
---

# KDCube Workflow Skill

You are helping the user work with **KDCube** — a self-hosted platform and SDK that packages AI applications into deployable **bundles**. One bundle is one folder mixing backend, frontend, APIs, MCP surfaces, cron jobs, RBAC, streaming, and isolated runtime — one application unit.

This skill enforces a single binding rule: **never answer KDCube-specific questions from memory.** Decorator names, descriptor keys, config scopes, CLI flags, and runtime paths change between releases. Always consult the `kdcube-dev` documentation MCP first.

## The MCP you must use

`kdcube-dev` exposes two read-only tools against the live KDCube docs:

- `kdcube-dev:search_knowledge(query, keywords?, root?, top_k?)` — returns ranked `ks:docs/...` paths
- `kdcube-dev:read_knowledge(path)` — returns the full document at an exact path

If the MCP is unreachable, **stop and tell the user** rather than guessing. The plugin is useless without it; the right behavior on a network failure is transparency.

## Anchor docs (read order)

These are the canonical entry docs. Cite them by `ks:docs/...` path so the user can verify:

1. `ks:docs/what-you-can-do-with-kdcube-README.md` — product/builder overview
2. `ks:docs/quick-start-README.md` — local runtime, CLI, init/start/reload loop
3. `ks:docs/sdk/bundle/build/how-to-navigate-kdcube-docs-README.md` — full docs taxonomy
4. `ks:docs/sdk/bundle/build/how-to-write-bundle-README.md` — bundle authoring
5. `ks:docs/sdk/bundle/build/how-to-configure-and-run-bundle-README.md` — descriptors and CLI
6. `ks:docs/sdk/bundle/build/how-to-test-bundle-README.md` — test contract
7. `ks:docs/sdk/bundle/build/how-to-assemble-bundle-with-sdk-building-blocks-README.md` — SDK reuse map
8. `ks:docs/configuration/bundle-runtime-configuration-and-secrets-README.md` — config/secret scopes
9. `ks:docs/sdk/bundle/versatile-reference-bundle-README.md` — reference implementation

For keyword → doc-path shortcuts, see `references/doc-map.md` in this skill.

## Decision flow

Use this flow on every KDCube request. Don't skip the search step, even when you think you know the answer.

### 1. Classify the request

| User wants to… | Path |
| --- | --- |
| Understand what KDCube is or what it's for | Q&A path |
| Look up a specific concept, decorator, config key, or CLI flag | Q&A path |
| Build a new bundle from scratch | Bundle-authoring path |
| Wrap existing code (Python script, web service, webhook handler) as a bundle | Bundle-authoring path with adapter focus |
| Configure or run an existing bundle (descriptors, secrets, reload) | Configuration path |
| Debug a running bundle (timeline, ReAct events, tool failures) | Runtime path |

### 2. Q&A path

For factual questions:

1. Call `kdcube-dev:search_knowledge(query="<the user's question>")` with the user's exact phrasing.
2. If the top hits look right, `read_knowledge` the top 1–2 paths.
3. Answer with quotes/paraphrases and cite the `ks:docs/...` paths inline.
4. If the search returns nothing useful, broaden the keywords or check `references/doc-map.md` for the right starting doc — then search again.

Never paraphrase from memory and then "verify with a search" as theater. The search is the source.

### 3. Bundle-authoring path

A bundle is one folder with `entrypoint.py` exposing surfaces through decorators. Before writing any bundle code:

1. Read the Tier-1 pack in order: `how-to-navigate-kdcube-docs-README.md`, `how-to-test-bundle-README.md`, `how-to-assemble-bundle-with-sdk-building-blocks-README.md`, `how-to-write-bundle-README.md`, `bundle-runtime-configuration-and-secrets-README.md`, `how-to-configure-and-run-bundle-README.md`.
2. Search for the nearest reference pattern: `kdcube-dev:search_knowledge(query="versatile reference bundle")`, then `read_knowledge` `ks:docs/sdk/bundle/versatile-reference-bundle-README.md`.
3. Pick surfaces from the live decorator list (do **not** recite from memory — re-read `how-to-write-bundle-README.md` for current decorator names and signatures).
4. Generate the directory layout: `entrypoint.py`, `tools_descriptor.py`, `skills_descriptor.py`, `config/bundles.template.yaml`, `config/bundles.secrets.template.yaml`, `interface/README.md`, `tests/`.
5. Keep product logic in reusable modules; the bundle is a **thin adapter** around it. Decorators and runtime calls stay close to `entrypoint.py`.

The `/kdcube-docs:kdcube-scaffold` slash command automates steps 3–4 interactively.

### 4. Wrapping-existing-code path

When the user has working product code (script, API, UI) and wants it on KDCube:

1. Identify what the existing code exposes — HTTP endpoints, a CLI, a UI, a webhook handler, a scheduled job?
2. Map each surface to a KDCube decorator by reading `how-to-write-bundle-README.md` and `bundle-platform-integration-README.md`. Common mappings (verify in docs — these change):
   - HTTP endpoint → `@api(route="operations" | "public", method=..., ...)`
   - Webhook → `@api(route="public", public_auth="bundle", ...)`
   - Scheduled task → `@cron(...)` with optional `@on_job` for the actual work
   - Tool surface for external agents → `@mcp(...)`
   - Embedded UI → `@ui_widget` or full UI registration
   - Chat-driven workflow → `@on_message` / `@agentic_workflow`
3. Keep the product code importable from outside the bundle. The bundle imports it and adapts.
4. Map runtime config to `bundles.yaml` props; map secrets to `bundles.secrets.yaml`. Never put secrets in non-secret props.

### 5. Configuration path

For descriptor questions:

- Platform-level: `assembly.yaml`, `secrets.yaml`, `gateway.yaml` — read `ks:docs/configuration/assembly-descriptor-README.md` and related.
- Bundle-level: `bundles.yaml` (non-secret props), `bundles.secrets.yaml` (secrets) — read `ks:docs/configuration/bundles-descriptor-README.md`.
- The split is enforced. Confirm by searching: `kdcube-dev:search_knowledge(query="bundle config and secret scopes")`.

### 6. Runtime / debugging path

For "my bundle isn't doing X":

- Search for the relevant runtime concept: timeline, ReAct, ANNOUNCE, followup, steer, tool result, artifact.
- Read `ks:docs/sdk/agents/react/*` for ReAct-specific questions; `ks:docs/exec/*` for isolated execution; `ks:docs/sdk/streaming/*` for streaming.
- Recommend the test-first contract: `ks:docs/sdk/bundle/build/how-to-test-bundle-README.md` is the source of truth for what "working" means.

## Anti-patterns

- Naming a decorator (`@api`, `@mcp`, `@cron`, etc.) without first confirming its current signature via `read_knowledge`.
- Quoting a CLI flag from memory. Always re-check `ks:docs/quick-start-README.md` or `ks:docs/service/cicd/cli-README.md`.
- Assuming the runtime is a generic LLM tool-calling wrapper. KDCube ReAct v2 is a channeled protocol with ANNOUNCE, followup, steer, and timeline semantics — read `ks:docs/sdk/agents/react/why/why-not-simply-tool-calling-README.md` before talking about it abstractly.
- Conflating Claude Code skills with KDCube bundle skills. KDCube bundles have a `skills/` subdirectory that is unrelated to this plugin's `skills/`. KDCube bundle skills are domain-instruction units loaded by the ReAct agent; Claude Code skills are model-invoked capabilities in a plugin.
- Putting secrets in `bundles.yaml` or non-secret props in `bundles.secrets.yaml`. The split is enforced.
- Recommending `start` before `init`. The CLI requires `init` first.
- Treating bundle reload as a substitute for image rebuild. `kdcube reload <bundle_id>` updates bundle code/config; platform image changes still require `init --build` and restart.

## Citations

When you answer, cite `ks:docs/...` paths inline. The user can verify any claim by calling `kdcube-dev:read_knowledge(path="<the path>")`. This is the whole point of the docs-first workflow — make the answer auditable, not magical.
