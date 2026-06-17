# cwos/ Changelog

Per-inhabitant change log for the CWOS starter inside [`chevan-quickstarts`](../). For umbrella-level changes (new inhabitants, structural reorganization, top-level docs), see [`../CHANGELOG.md`](../CHANGELOG.md).

Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) loosely followed; versioning [SemVer](https://semver.org).

The version here tracks this starter's content evolution. It is independent of the canonical CWOS spec version at the upstream reference implementation (`chevan-content`) until the source-of-truth flip happens (see [`WHY-CWOS.md`](WHY-CWOS.md) "Status").

---

## [1.7.0] — 2026-06-16

### Added

**`#### Tasks File Structure (Standard)` subsection in `AICONFIG.template.md`** — codifies the four-section layout (Summary of Open Projects / Notes/Reference / Open Projects / Closed / Completed Projects), four-level hierarchy (`### Project N:` / `#### Phase N:` / `**Deliverable**` / `- [ ] Task`), and the "Why this section order" rationale. Lifted from chevan-content `AICONFIG.md` (v5.11.0 codification) and workspace-chevan `AICONFIG.md` (June 10, 2026 originator). Was flagged as candidate v1.5.0 / v1.6.0 work in prior changelog entries; now shipped.

**`Conversational Work Help` section in `aiconversations/0-work-status.template.md`** — the canonical skill-invocation reference block (work management, conversation file maintenance, session orientation, run-prompt workflow, bootstrap/migration, skill management, configuration sources, standing rules). Same content the implementation repos (chevan-content, workspace-chevan) use, with one principled adaptation noting that `work-status-dashboard` skill is repo-specific and not shipped in the starter.

### Changed

**Canonical structure rules codified in `aiconversations/README.md`** — the structural invariants for `0-work-status.md` and `0-project.md` across CWOS-aligned repos:

- Parallel lean frontmatter schema (documentType / purpose / version / versionNote / lastUpdated / lastUpdatedNote / status / location / companionSurface / generatedBy).
- 0-work-status.md section structure: tables (`## N. Domain Name`) + Maintenance notes + Conversational Work Help.
- 0-project.md section structure: domain sections mirroring 0-work-status.md's table set + Files needing cleanup (optional) + Notes on this rollup.
- **Mirror rule**: 0-project.md section numbering and labels MUST mirror 0-work-status.md's table set so the two surfaces cross-reference cleanly.

**`aiconversations/0-work-status.template.md` frontmatter cleaned up to canonical lean schema** — switched from the prior multi-line `purpose: |` block to a one-line `purpose:`; added `version` + `versionNote` fields; consolidated the `lastUpdatedNote:` description. Added a "Canonical structure note" paragraph in the template body explaining the mirror rule with `0-project.md`.

**`aiconversations/0-project.template.md` restructured to use mirror-numbering** — sections changed from `## DOMAIN 1` to `## 1. Domain Name` to match the dashboard's table numbering convention. Added "Canonical structure note" paragraph explaining the mirror rule. Added optional "Files needing cleanup" section between the domain sections and Notes section. Frontmatter aligned to the canonical schema.

### Notes

These changes close the structural-parity gap between `0-work-status.md` and `0-project.md` across the three CWOS implementation repos. Future bootstraps now have:

- A documented frontmatter schema (lean, optional-field-aware)
- A documented section structure for both files
- The mirror rule made explicit (cross-reference between dashboard tables and rollup sections)
- The tasks-file standard available in the AICONFIG template (no more need to re-author per repo)

The existing implementation repos (chevan-content, workspace-chevan) have files that mostly match the canonical structure but with legacy frontmatter shape differences. Per the standing "don't force a one-off reshuffle" rule, those files converge naturally at the next meaningful edit rather than via a forced rewrite.

Driving conversation: chevan-content `aiconversations/_system/operations/conversational-work-operations-conversation.md` (entry 2026-06-16 5:50PM).

---

## [1.6.0] — 2026-06-16

### Changed

**`refresh-work-management` skill extended to chain `prioritize-open-projects` as Step 3.** Previously the orchestrator ran two steps (dashboard refresh + projects rollup refresh); now it runs three steps with prioritization as the final read-only analytic pass. Single command (`refresh work management`) now produces the full work-management workflow: dashboard + rollup + priority outline.

- Description updated to mention the prioritization step explicitly so the AI invokes the orchestrator on prompts like "what should I work on?" as well as the prior "refresh the dashboards" / "what's active?" triggers.
- New `--skip-prioritize` flag preserves the v1.5.x and earlier two-step behavior for cases where the operator wants refresh without analysis.
- New Step 3 in the procedure invokes `prioritize-open-projects` against the just-refreshed rollup.
- Combined report (Step 4) expanded with a "Priority outline" section showing tiers produced, "Recommended focus this week" pick, and closure candidates flagged.

The three underlying skills (`work-status-dashboard`, `open-projects`, `prioritize-open-projects`) remain independently invokable. The orchestrator is the recommended common case; the individual skills are for edge cases (only-dashboard, only-projects, only-prioritize).

### Notes

This is a behavioral change to an existing skill — bumped MINOR per SemVer. Operators who scripted the orchestrator's prior two-step behavior can pass `--skip-prioritize` to preserve it.

Driving conversation: chevan-content `aiconversations/_system/operations/conversational-work-operations-conversation.md` (entry 2026-06-16 5:31PM).

---

## [1.5.0] — 2026-06-16

### Added

**`prioritize-open-projects` skill** at `operations/cwos/skills/prioritize-open-projects/`. Reads the open-projects rollup (or walks `-tasks.md` files directly) and produces a priority-ranked tier outline (P1-P7-ish) with reasoning per tier and an honest "recommended focus this week" pick of 1-3 items. Read-only — does NOT modify `0-project.md`, `0-work-status.md`, or any `-tasks.md` file. Closes the gap between `open-projects` (which says *what's open*) and operator decision-making (which needs *what to do first*).

**Dashboard template files** at `aiconversations/`:

- `0-work-status.template.md` — Work-status dashboard skeleton with frontmatter, placeholder table set (5 tables with `{{TABLE N NAME}}` placeholders), and table-set adaptation notes. Bootstrappers copy and customize for their content domains.
- `0-project.template.md` — Open-projects rollup skeleton with frontmatter and domain placeholders. After bootstrap, the `open-projects` skill regenerates the content from real `-tasks.md` data.
- `README.md` for the folder explains the bootstrap procedure, the rationale for shipping templates (the dashboard FILE follows a stable shape; the dashboard-refresh SKILL stays repo-specific because table-set logic varies per repo).

### Notes

The companion `work-status-dashboard` skill is still NOT shipped in the CWOS starter — that remains a repo-specific authoring exercise per the existing v1.2.0 / v1.4.0 architectural decision. Shipping the dashboard template files closes a different gap: bootstrappers now have a structural starting point for the FILE without having to derive it from scratch, even though they still author the refresh SKILL themselves.

The reverse-parity tasks-file refinements from chevan-content v5.11.0 Part 2 (bullet-list summary, "Closed / Completed Projects" naming, "Project N:" numbering, "Why this order" rationale) are still NOT ported into `AICONFIG.template.md` — separate decision, still candidate for a later v1.6.0 if a bootstrapper wants the full Tasks File Structure standard pre-shipped rather than authored from scratch per repo.

Driving conversation: chevan-content `aiconversations/_system/operations/conversational-work-operations-conversation.md` (entry 2026-06-16 5:18PM).

---

## [1.4.0] — 2026-06-16

### Changed

**`[End of Document]` marker deprecated for formal documents (parity with chevan-content v5.11.0 and workspace-chevan's earlier June 3, 2026 deprecation).** The marker added noise without value — git history is the record of completeness. Exception preserved: archive chunks still end with `[End of Document]` as an immutable historical record. Active conversation files end with the trailing user placeholder stub (already canonical).

Anyone bootstrapping a new CWOS repo from this starter no longer inherits the deprecated rule.

### Removed

- Trailing `[End of Document]` markers stripped from the four canonical starter files that carried them:
  - `cwos/AICONFIG.template.md`
  - `cwos/WHY-CWOS.md`
  - `cwos/CWOS-SETUP.md`
  - `cwos/AGENTS.md`
- `cwos/CWOS.md` and `cwos/README.md` were already free of the marker; no change needed.

### Notes

The chevan-quickstarts `AICONFIG.template.md` Formatting section had already been free of the explicit "End of document marker" rule for some time, so no behavioral-rule edit was needed there — this release is the file-content sweep that aligns the starter's own files with the rule's absence.

Reverse parity from workspace-chevan's June 10, 2026 tasks-file refinements (bullet-list summary, "Closed / Completed Projects" naming, "Project N:" numbering prefix, "Why this order" rationale) was NOT brought into this template release — those refinements live in chevan-content `AICONFIG.md` (v5.11.0) and workspace-chevan `AICONFIG.md`. They can be lifted into `chevan-quickstarts/cwos/AICONFIG.template.md` as a future v1.5.0 if a target repo bootstrapper wants the full Tasks File Structure standard pre-shipped rather than authored from scratch per repo.

Driving conversation: chevan-content `aiconversations/_system/operations/conversational-work-operations-conversation.md` (entry 2026-06-16 3:14PM).

---

## [1.3.0] — 2026-06-02

### Added

**Standards-consolidation step in the migration playbook (closes a real gap that emerged from reviewing the chevan-content migration history).** The original `operations/conversational-work/` install-pipeline pattern stored standards content (writing-style, conversation-files, document-layout, essay-workflow, etc.) in `ai-configs/github/prompts/<template>.md` templates that got "installed" into AICONFIG.md on demand. Migrating a repo that hasn't run the consolidation pass yet would functionally orphan that standards content when the LEGACY notice goes on `operations/conversational-work/`.

- `operations/cwos/skills/cwos-migrate-from-conversational-work/SKILL.md` — pre-flight check expanded to audit standards-consolidation state (templates present? AICONFIG.md STANDARDS sections populated?). Surfaces three states (A: consolidated, B: unconsolidated, C: no templates) and recommends a consolidation pass in Session B if the target is in State B. Session B procedure adds explicit step 7 covering the consolidation work — read each template, identify the right AICONFIG.md section, lift behavioral content, skip install-pipeline metadata, commit as `chore(aiconfig): consolidate ... from predecessor templates`.
- `CWOS-SETUP.md` "Migrating from operations/conversational-work/" — new "Standards consolidation (if predecessor templates have unconsolidated content)" subsection after "Cleanup horizon." Documents the three states, the per-template lift procedure, the canonical worked example reference (chevan-content v5.8.x changelog entries).
- Pre-flight check shell snippet in CWOS-SETUP.md migration section also expanded with the consolidation audit (`ls operations/conversational-work/ai-configs/github/prompts/` + `grep STANDARDS AICONFIG.md`).

**"What belongs in AICONFIG.md" framing clarification (clears up a real interpretive ambiguity).** The doc previously left implicit that writing standards, file conventions, brand guidelines, and other "non-AI" configurations are still AICONFIG.md content — the rules apply to humans and AI alike, so AICONFIG.md is the canonical home regardless of who's reading it.

- `cwos/WHY-CWOS.md` — new "### A note on what belongs in AICONFIG.md" subsection at the end of "The approach: three layers." Three-reason framing: AI auto-loads it, humans use it as canonical reference, multi-file drift gets prevented.
- `cwos/AICONFIG.template.md` — same clarification added under the three-layer-split decision tree. Cross-references CWOS-SETUP.md "Standards consolidation" for migration cases.

### Rationale

These additions emerged from a 2026-06-02 review of the chevan-content migration history (`v5.8.x` consolidation series → `v5.9.0` CWOS migration). The chevan-content case worked cleanly because consolidation predated the migration; the playbook silently assumed that pattern. For repos doing migration without the pre-consolidation work, the gap would functionally orphan standards content. v1.3.0 closes the gap by making the consolidation step explicit and surfacing the audit at pre-flight time.

---

## [1.2.0] — 2026-06-02

### Added

**Project-tracking pattern: distributed `-tasks.md` files with centralized rollup + orchestrator.** Codifies the solution to a visibility problem that emerged in workspace-chevan after months of operation: per-conversation `-tasks.md` files capture project state well, but cross-corpus visibility requires opening each file individually. The fix is a derived rollup that aggregates without breaking the capture convention, plus a thin orchestrator that refreshes both rollup surfaces in sync.

- `operations/cwos/skills/open-projects/` — new skill that walks `aiconversations/**/*-tasks.md`, extracts each file's "Summary of Open Projects" section, and assembles a domain → conversation → project view at `aiconversations/0-project.md`. Derived view; the `-tasks.md` files remain canonical detail. Companion to a repo-specific work-status dashboard (different signal, same shape). Only currently-open projects appear in the rollup; completed work stays in the source files. Skill auto-detects the repo's domain grouping from `0-work-status.md`'s top-level headings (or falls back to grouping by `aiconversations/` top-level folder if no dashboard exists yet).
- `operations/cwos/skills/refresh-work-management/` — thin orchestrator that invokes the repo's `work-status-dashboard` skill and the `open-projects` skill in sequence, ensuring both derived surfaces stay in sync. Composition pattern (over collapsing the two skills into one), matching the pattern already used by `conversational-maintenance-review` and `cwos-cold-start`. Recommended common-case invocation for "refresh the work management surfaces"; the two underlying skills remain available for edge cases.
- `AICONFIG.template.md` STANDARDS - OPERATIONS gains a new "Project tracking — distributed `-tasks.md` files with centralized rollup" subsection inside Conversation Files. Documents the capture/visibility split and points at the `open-projects` and `refresh-work-management` skills.
- `CWOS.md` "aiconversations — long-form dialogue threads" section gains a "Dual rollup pattern: work-state and project-state" subsection. Frames the two derived surfaces (`0-work-status.md` for thread-level, `0-project.md` for project-level) at the architecture level so future readers see the pattern as part of the aiconversations extension's design, not as a hidden skill.

### Rationale

The pattern emerged on 2026-06-02 when the workspace-chevan operator noticed that distributed `-tasks.md` files were going stale and undiscovered. The first instinct was to centralize all project tracking into a single file, but size analysis showed that approach would require structural rotation rules to bound growth — essentially recreating per-file isolation inside one big file. The cleaner answer was to add a rollup (derived view) alongside the existing `-tasks.md` files. This v1.2.0 codifies the rollup pattern at the starter level so future CWOS deployments get it from day one. Companion case study lives at the chevan-content knowledgebase (`knowledgebase/knowledge-architecture/cwos-migration-frontmatter-as-navigation-graph.md`) — the project-tracking pattern is part of the same operational maturity lane as the frontmatter-as-navigation-graph work.

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
