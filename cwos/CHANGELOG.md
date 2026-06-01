# cwos/ Changelog

Per-inhabitant change log for the CWOS starter inside [`chevan-quickstarts`](../). For umbrella-level changes (new inhabitants, structural reorganization, top-level docs), see [`../CHANGELOG.md`](../CHANGELOG.md).

Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) loosely followed; versioning [SemVer](https://semver.org).

The version here tracks this starter's content evolution. It is independent of the canonical CWOS spec version at the upstream reference implementation (`chevan-content`) until the source-of-truth flip happens (see [`WHY-CWOS.md`](WHY-CWOS.md) "Status").

---

## [1.0.0] — 2026-06-01

### Added

Initial release of the cwos starter as the first inhabitant of `chevan-quickstarts`. Synced from the maintainer's reference implementation at `chevan-content`.

**Root-level canonical files:**

- `WHY-CWOS.md` — narrative intro covering the acronym (Conversational Work Operating System), the concept of conversational work, the three-layer approach, knowledge architecture (memory / inference / reasoning), why this works for human-human work too, philosophy, who it's for.
- `CWOS.md` — canonical CWOS architectural specification (hub-and-spoke variants, three-layer split, vendor-agnostic principle, foundation files, the optional aiconversations extension).
- `CWOS-SETUP.md` — bootstrap procedure (four deployment scenarios), migration recipe from `operations/conversational-work/` predecessor infrastructure, session-refresh patterns.
- `AGENTS.md` — thin cross-vendor entry-point template (Codex, Cursor, Aider, others reading AGENTS.md).
- `AICONFIG.template.md` — operational charter template with universal sections filled in and `{{REPO_NAME}}` / `{{PROJECT_CONTEXT}}` placeholders for repo-specific content.
- `CLAUDE.template.md` — thin Claude Code bootstrap template.
- `README.md` — inhabitant overview with three consumption patterns (raw URL fetches, sparse checkout, full clone).

**Universal Agent Skills** (`operations/cwos/skills/`):

- `run-prompt-protocol/` — the core run-prompt workflow.
- `conversation-archiving/` — archive conversation files over 75 KB.
- `cwos-migrate-from-conversational-work/` — clean-break migration from the older `operations/conversational-work/` predecessor infrastructure to CWOS.
- `_template/` — Agent Skills format scaffold for authoring new skills.

**Universal reference docs** (`operations/cwos/reference/`):

- `taxonomy.md` — CWOS vocabulary glossary (lightly genericized from the reference implementation).
- `conventional-commits.md` — Conventional Commits format reference.
- `agent-skills-standard.md` — Anthropic Agent Skills standard reference.
- `mcp-stack.md` — Model Context Protocol server configuration reference.

**Memory templates** (`operations/cwos/memory/`):

- `decisions/_template.md` — Architecture Decision Record (Nygard format) template.
- `projects/_template.md` — project re-entry brief template.

---

## How to update this changelog

Contributors submitting PRs that touch `cwos/` should add an entry to the **Unreleased** section at the top of this file (create it if it doesn't exist). On release, the maintainer rolls Unreleased into the next versioned section and tags accordingly.

For umbrella-level changes (adding a new inhabitant, top-level renames, license changes), update the umbrella `CHANGELOG.md` at the repo root instead of (or in addition to) this file.

See [`../CONTRIBUTING.md`](../CONTRIBUTING.md) for the contribution flow.
