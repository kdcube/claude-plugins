# Changelog

All notable changes to this marketplace and its plugins are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.0] - 2026-05-17

### Added
- Initial release of the `kdcube` marketplace and the `kdcube-docs`
  plugin.
- `kdcube-dev` HTTP MCP server pointing at the stable docs alias
  `https://kdcube.tech/mcp/docs` (CloudFront 308 redirect to the
  current `kdcube.copilot` bundle). Exposes read-only
  `search_knowledge` and `read_knowledge` tools against the live
  KDCube docs.
- `kdcube` workflow skill that requires Claude to consult the docs
  MCP before answering any KDCube-specific question, with a full
  keyword → `ks:docs/...` map under `references/doc-map.md`.
- `/kdcube-docs:kdcube-scaffold <bundle-name>` slash command for
  generating a new KDCube bundle skeleton.
- `/kdcube-docs:kdcube-doc <question>` slash command for fast,
  citation-backed doc lookup.
- Marketplace manifest under `.claude-plugin/marketplace.json`
  (relative-string source so the same manifest works for both
  `file://` and GitHub installs).
- Naming convention documented in the root README: kebab-case with a
  `kdcube-` prefix for any future per-capability plugin
  (`kdcube-runtime`, `kdcube-bundles`, …).
