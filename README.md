# KDCube Claude Plugins

Official [Claude Code](https://code.claude.com) plugins for [KDCube](https://kdcube.tech) — the self-hosted platform and SDK for packaging AI applications into deployable bundles.

## Why this marketplace exists

KDCube's decorator names, descriptor keys, CLI flags, and runtime paths change between releases. A coding agent that answers KDCube questions from training-time memory will teach you to hallucinate. The plugins in this marketplace solve that by shipping the live KDCube documentation MCP alongside the skills and slash commands that use it — so every KDCube answer Claude gives is grounded in current docs and cites a verifiable `ks:docs/...` path.

## Install

Add the marketplace, then install the plugin:

```
/plugin marketplace add https://github.com/kdcube/claude-plugins
/plugin install kdcube-docs@kdcube
```

After install, ask Claude a KDCube question (e.g. "what's in `ks:docs/quick-start-README.md`?") and confirm it consults the `kdcube-dev` MCP rather than answering from memory.

## Plugins in this marketplace

| Plugin | What it ships |
| --- | --- |
| [`kdcube-docs`](plugins/kdcube-docs/README.md) | The `kdcube-dev` documentation MCP, a workflow skill that requires Claude to consult the docs before answering, and two slash commands (`/kdcube-docs:scaffold` and `/kdcube-docs:search`). |

Future per-capability plugins (e.g. `kdcube-runtime`, `kdcube-bundles`, `kdcube-chat`) may be added once usage data shows which subsets people actually want.

## Naming convention

Plugins in this marketplace follow a single rule:

> **`kdcube-<capability>`** — kebab-case, lowercase, hyphen-separated, prefixed with `kdcube-`.

The prefix groups the marketplace's plugins visually in `/help` and the plugin selector; the kebab-case rest reads as one capability domain.

**Why not dot notation** (e.g. `kdcube.docs`, `kdcube.runtime`)?

- The marketplace validator (`claude plugin validate`) *does* accept dots, so a dotted name would install fine today.
- But dots flow into downstream namespaces that are stricter. In particular, Anthropic's tool-name regex is `^[a-zA-Z0-9_-]{1,64}$` — **no dots**. If the runtime ever derives an MCP tool name from a dotted plugin name, the tools silently disappear from the model's tool list.
- Dotted names also look like domain names in `/help` listings (`/kdcube.docs:scaffold` reads visually like a URL), which is friction for users.
- Anthropic's own marketplace uses kebab-case prefixes (`agent-sdk-dev`, `claude-code-search`, …); matching that pattern keeps tooling, search, and IDE listings consistent.

**Plugin namespace, not marketplace namespace.** The marketplace itself stays named `kdcube` (the install pattern is `<plugin>@<marketplace>`, so `kdcube-docs@kdcube`). The marketplace name is the install address; the plugin name is the working handle inside Claude Code.

**What stays unchanged across plugins:**

| Layer | Naming | Example |
| --- | --- | --- |
| Marketplace | `kdcube` (fixed) | `/plugin marketplace add … kdcube` |
| Plugin | `kdcube-<capability>` | `kdcube-docs`, future `kdcube-runtime` |
| Skill / slash command inside a plugin | free-form kebab-case, no redundant prefix | `scaffold`, `search` (inside `kdcube-docs`) |
| MCP server inside a plugin | `kdcube-<surface>` | `kdcube-dev` (docs MCP) |

So when you see `/kdcube-docs:scaffold` in `/help`, that's `<plugin>:<skill-name>`. Future plugins follow the same shape — e.g. a `kdcube-runtime` plugin might ship `/kdcube-runtime:deploy` and `/kdcube-runtime:reload`.

## Contributing

Pull requests welcome. Two non-negotiable rules for any plugin in this marketplace:

1. **Docs-first.** Every KDCube fact in a skill, README, or generated artifact must come from the live KDCube docs MCP, not training memory. If you can't verify a claim by reading the linked `ks:docs/...` path, don't ship it.
2. **Match the canonical schema.** The plugin layout in this repo follows [`anthropics/claude-plugins-official/plugins/example-plugin`](https://github.com/anthropics/claude-plugins-official/tree/main/plugins/example-plugin) — keep it that way unless the upstream example changes.

KDCube's main contributing docs and developer guide live in the [`kdcube-ai-app`](https://github.com/kdcube/kdcube-ai-app) repository.

## License

MIT — see [LICENSE](LICENSE).
