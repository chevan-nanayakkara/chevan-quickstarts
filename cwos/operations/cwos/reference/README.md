# operations/cwos/reference/ — Universal CWOS Reference Docs

Standing knowledge for CWOS-aligned repos. These docs don't change frequently; they describe formats, standards, and vocabulary used across CWOS deployments.

---

## Docs in this starter

- **[`taxonomy.md`](taxonomy.md)** — CWOS vocabulary glossary. Definitions of hub, spoke, memory, skill, ADR, conversation file, the three-layer split, session lifecycle terms, operating tests (sovereignty, accumulation), and deprecated vocabulary. Read first to standardize terminology across sessions.
- **[`conventional-commits.md`](conventional-commits.md)** — Conventional Commits format reference. Types (feat, fix, docs, chore, refactor, etc.), scope syntax, breaking-change conventions, examples.
- **[`agent-skills-standard.md`](agent-skills-standard.md)** — Anthropic Agent Skills standard reference. Format spec (YAML frontmatter, SKILL.md body, supporting files like scripts/ and references/ and assets/), authoring guidance, when skills are useful vs overkill.
- **[`mcp-stack.md`](mcp-stack.md)** — Model Context Protocol (MCP) server configuration reference. Common MCP servers, configuration patterns, vendor-specific setup notes.

## Adding repo-specific reference docs

When a target repo adopts CWOS, you may add repo-specific reference docs to this folder (e.g., a domain-specific glossary, a coding standards reference for the repo's primary language, etc.). Same pattern: stable content that doesn't change frequently and gets read on demand.

## What does NOT belong here

- **Decisions** → `operations/cwos/memory/decisions/` (ADRs).
- **Procedures** → `operations/cwos/skills/<name>/SKILL.md` (skills).
- **Project status** → `operations/cwos/memory/projects/<spoke>/re-entry.md`.

Reference docs are facts that don't change session-to-session. If content evolves with each project or decision, it belongs in memory, not reference.

---

[End of Document]
