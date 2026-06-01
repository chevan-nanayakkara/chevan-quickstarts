# cwos/ Changelog

Per-inhabitant change log for the CWOS starter inside [`chevan-quickstarts`](../). For umbrella-level changes (new inhabitants, structural reorganization, top-level docs), see [`../CHANGELOG.md`](../CHANGELOG.md).

Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) loosely followed; versioning [SemVer](https://semver.org).

The version here tracks this starter's content evolution. It is independent of the canonical CWOS spec version at the upstream reference implementation (`chevan-content`) until the source-of-truth flip happens (see [`WHY-CWOS.md`](WHY-CWOS.md) "Status").

---

## [1.1.0] — 2026-06-01

### Added

**Maintenance lane (3 new universal skills + supporting AICONFIG.template.md updates):** ported the maintenance-tooling pattern that emerged from the workspace-chevan May 31, 2026 post-CWOS-migration maintenance pass. These were authored in workspace-chevan (the second CWOS deployment) on 2026-05-31 to codify the drift-detection and periodic-review patterns; brought back into this starter so future CWOS deployments get the tooling lane from day one.

- `operations/cwos/skills/frontmatter-validate/` — detection-only skill that walks every conversation file's YAML frontmatter and reports dangling structural pointers (`companionTo`, `archives`, `parentConversation`, `subConversations`, `relatedDocuments`, `location`, `companionTasks`, plus optional `companionRepo`/`companionPath`). Codifies the 4-rule maintenance pattern (A: cross-system path migrations; B: root-folder relocations; C: stale archive folders; D: sibling renames + dangling drops).
- `operations/cwos/skills/conversational-maintenance-review/` — periodic detection-only orchestrator. Runs the four-phase sweep (frontmatter validation → size-threshold scan → status/activity scan → optional dedup heuristic) and produces a punch list grouped by which skill should fix each finding. Designed for the CWOS quarterly review schedule. Invokes `frontmatter-validate` for Phase 1.
- `operations/cwos/skills/cwos-cold-start/` — invokable cold-start session-orientation skill. Reads canonical files (`AICONFIG.md`, `README.md`, `CWOS.md`, `operations/cwos/README.md`, memory index, work-status dashboard if present) and produces an absorbed-state summary covering architecture / repo context / current active work / open re-entry briefs / recent governance decisions. Read-only; the invokable equivalent of the cold-start prompt template in the repo's README.md.

**AICONFIG.template.md updates:**

- New `### Cross-repo pointer convention (hub→spoke companions — optional)` subsection under STANDARDS - WRITING. Documents the Option α field pair (`companionRepo:` + `companionPath:`) backed by a spoke registry. Marked optional — only relevant for hub repos in multi-repo hub-and-spoke setups; skip for single-repo / spoke / polyrepo deployments.
- New `### Frontmatter validation after restructures` subsection under STANDARDS - MAINTENANCE. Codifies the post-restructure rule: after any folder rename, file relocation, or conversation deletion, run `frontmatter-validate` before committing. Quarterly via `conversational-maintenance-review`.
- Quarterly periodic-review note updated to reference the orchestrator skill.

**Skills README updates:** restructured the skill listing into four subsections (Core workflow / Maintenance lane / Session orientation / Template) to surface the lane structure that emerged in workspace-chevan.

### Rationale

These additions emerged from the workspace-chevan migration's discovery that CWOS treats frontmatter as the canonical navigation graph, which raised the standard for "navigable" from *findable* (a determined human + AI can locate the target) to *traversable without context* (a fresh agent with no session history can follow only the declared pointers). The maintenance lane codifies the discipline that supports the new standard, and the cold-start skill gives operators an invokable alternative to the text-prompt template. See the case study at chevan-content `knowledgebase/knowledge-architecture/cwos-migration-frontmatter-as-navigation-graph.md` for the conceptual framing.

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
