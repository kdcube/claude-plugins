# KDCube Docs Keyword Map

Use this map to pick the right `ks:docs/...` starting path when the user's question doesn't obviously match an anchor doc. Then call `kdcube-dev:read_knowledge(path=...)` on the chosen path. If a topic isn't listed, fall back to `kdcube-dev:search_knowledge(query=...)` with the user's phrasing.

The map is intentionally lossy — it surfaces the most-used starting points, not every doc. The MCP search index is the source of truth for the full set.

## Top-level orientation

| User asks about… | Start here |
| --- | --- |
| "what is KDCube" / product overview | `ks:docs/what-you-can-do-with-kdcube-README.md` |
| local run / first install / CLI bootstrap | `ks:docs/quick-start-README.md` |
| how the docs are organized | `ks:docs/sdk/bundle/build/how-to-navigate-kdcube-docs-README.md` |
| docs hub / index | `ks:docs/README.md` |

## Bundle authoring

| User asks about… | Start here |
| --- | --- |
| writing a new bundle | `ks:docs/sdk/bundle/build/how-to-write-bundle-README.md` |
| configuring and running a bundle | `ks:docs/sdk/bundle/build/how-to-configure-and-run-bundle-README.md` |
| testing a bundle | `ks:docs/sdk/bundle/build/how-to-test-bundle-README.md` |
| reusable SDK building blocks | `ks:docs/sdk/bundle/build/how-to-assemble-bundle-with-sdk-building-blocks-README.md` |
| releasing bundle content | `ks:docs/sdk/bundle/build/how-to-release-bundle-content-README.md` |
| reference implementation | `ks:docs/sdk/bundle/versatile-reference-bundle-README.md` |
| bundle developer guide | `ks:docs/sdk/bundle/bundle-developer-guide-README.md` |

## Platform integration & runtime

| User asks about… | Start here |
| --- | --- |
| platform integration surface | `ks:docs/sdk/bundle/bundle-platform-integration-README.md` |
| agent integration | `ks:docs/sdk/bundle/bundle-agent-integration-README.md` |
| widget integration | `ks:docs/sdk/bundle/bundle-widget-integration-README.md` |
| bundle runtime semantics | `ks:docs/sdk/bundle/bundle-runtime-README.md` |
| bundle storage & cache | `ks:docs/sdk/bundle/bundle-storage-and-cache-README.md` |

## Configuration & secrets

| User asks about… | Start here |
| --- | --- |
| assembly.yaml (tenant/project, ports, infra, auth) | `ks:docs/configuration/assembly-descriptor-README.md` |
| bundles.yaml (registry, props) | `ks:docs/configuration/bundles-descriptor-README.md` |
| bundles.secrets.yaml | `ks:docs/configuration/bundles-secrets-descriptor-README.md` |
| secrets.yaml (platform-level) | `ks:docs/configuration/secrets-descriptor-README.md` |
| runtime config & secrets store | `ks:docs/configuration/runtime-configuration-and-secrets-store-README.md` |
| bundle config + secret scopes (the split rule) | `ks:docs/configuration/bundle-runtime-configuration-and-secrets-README.md` |

## CLI & deployment

| User asks about… | Start here |
| --- | --- |
| CLI commands (init, start, stop, info, reload, bundle) | `ks:docs/service/cicd/cli-README.md` |
| CLI as control plane | `ks:docs/service/cicd/design/cli--as-control-plane-README.md` |
| Docker Compose environment setup | `ks:docs/service/environment/setup-for-dockercompose-README.md` |
| public HTTPS via ngrok (for webhooks/callbacks) | `ks:docs/service/cicd/ngrok-README.md` |

## ReAct agent runtime

| User asks about… | Start here |
| --- | --- |
| why ReAct over plain tool calling | `ks:docs/sdk/agents/react/why/why-not-simply-tool-calling-README.md` |
| ANNOUNCE block | `ks:docs/sdk/agents/react/react-announce-README.md` |
| timeline, followup, steer | `ks:docs/sdk/agents/react/shared-timeline-event-bus-steer-followup-README.md` |

## Tools, skills, streaming

| User asks about… | Start here |
| --- | --- |
| SDK tools surface | `ks:docs/sdk/tools/sdk-tools-README.md` |
| SDK skills (bundle-level skills, NOT Claude Code skills) | `ks:docs/sdk/skills/skills-README.md` |
| LLM streaming | `ks:docs/sdk/streaming/llm-streaming-README.md` |

## Execution runtime

| User asks about… | Start here |
| --- | --- |
| ISO runtime overview | `ks:docs/exec/README-iso-runtime.md` |
| distributed / Fargate exec | `ks:docs/exec/distributed-exec-README.md` |

## Integrations

| User asks about… | Start here |
| --- | --- |
| Telegram bundles / Mini Apps | `ks:docs/sdk/integrations/telegram/telegram-README.md` |
| Telegram external prerequisites | `ks:docs/sdk/integrations/telegram/telegram-external-prereq-README.md` |

## Reference copilot

| User asks about… | Start here |
| --- | --- |
| kdcube.copilot bundle (the reference copilot itself) | search: `kdcube-dev:search_knowledge(query="kdcube copilot bundle")` |

## When in doubt

Use `kdcube-dev:search_knowledge(query="<user's exact phrasing>", top_k=10)` and read the top 2 results. The search index is current; this map is a convenience layer over it.
