---
name: cwos-cold-start
description: Load full CWOS context into the current AI session by reading the canonical files (/AICONFIG.md, /README.md, /CWOS.md if not already loaded), the current work-status dashboard and open-projects rollup (if present), and the memory index — then summarize the three-layer split, the always-restart pattern, current active work, current open projects, and any open re-entry briefs so the operator can verify the AI has absorbed the architecture. Use at the start of a fresh AI session in a CWOS-aligned repo, after Claude Code compaction, when picking up the repo after a multi-week break, or when an AI session has drifted and needs full re-orientation.
---

# CWOS Cold Start

Load and verify the canonical CWOS context for the current repo. Invoke this when:

- Starting a fresh AI session in a CWOS-aligned repo (alternative to pasting the cold-start prompt from `README.md` or `CWOS-SETUP.md`)
- After Claude Code conversation compaction has compressed session history
- Returning to the repo after a multi-week break
- An active AI session has drifted from the CWOS conventions and needs a full re-orientation (heavier than a post-update or mid-session-drift refresh prompt)

This skill is the *invokable* equivalent of the cold-start prompt template documented in `README.md` Session-Start Prompts (or in `CWOS-SETUP.md` "Refreshing an existing session"). Both work; the skill form is convenient when you'd rather say "run cwos-cold-start" than paste the template.

## When NOT to invoke

- **Within an established working session** that's already absorbed the context — invoke `frontmatter-validate` or `conversational-maintenance-review` directly if those are what you need.
- **For a single-file refresh** (you just edited AICONFIG.md mid-session) — use a lighter post-update prompt. That's targeted.
- **For mid-session drift on one specific rule** — cite the specific rule and the violation. Surgical recalibration beats full re-load.

## How to invoke

Several equivalent forms:

- "run cwos-cold-start"
- "invoke cwos-cold-start"
- "load the CWOS context" (the harness's natural-language matching routes this to the skill)
- "cold-start the session" (same)

For an AI session that doesn't have the harness's auto-discovered skills index — typically a non-Claude-Code vendor that doesn't yet support Agent Skills natively — paste the equivalent text-prompt template from `README.md` or `CWOS-SETUP.md`.

## Procedure

The skill reads canonical files, builds an absorbed-state summary, and surfaces current active work. It does not modify anything.

### Step 1 — Read the canonical foundation

Read the following files in order. For each, confirm absorption by including its key concepts in the final summary:

1. **`/AICONFIG.md`** — vendor-neutral behavioral charter. Critical sections: CANONICAL SOURCE NOTE (three-layer split + decision tree + always-restart pattern), STARTUP CONTEXT, PROJECT CONTEXT, REPOSITORY CONTEXT, STANDARDS sections (WRITING / OPERATIONS / MAINTENANCE).

2. **`/README.md`** — repo overview. Critical sections vary per repo: Quick Start, Session-Start Prompts (where this skill's text-prompt equivalent lives if the repo includes that section), What This Repository Contains, Directory Structure, AI Configuration.

3. **`/CWOS.md`** — only if the architecture/spec context isn't already loaded from a prior session. Otherwise, the AICONFIG.md CANONICAL SOURCE NOTE references suffice.

4. **`/operations/cwos/README.md`** — implementation-layer overview.

5. **`/operations/cwos/memory/MEMORY.md`** — the memory index (auto-loaded by Claude Code at session start in some configurations, but explicitly re-read here to surface what's available on demand).

### Step 2 — Read the current work surfaces (if present)

Two derived rollup surfaces capture different signals; read both if present.

**`/aiconversations/0-work-status.md`** — work-state dashboard (thread-level). Read if the repo uses the aiconversations extension and has this dashboard authored. The dashboard format varies per repo (this starter does not prescribe a specific table set — repos adapt the format to their content domain; chevan-content uses life-domain tables, workspace-chevan uses business-domain tables). Note:

- Which threads are in `Active Work` (what's in flight right now)
- Which engagements / projects / spokes are active vs paused
- Which products / initiatives are active
- Which system / ops threads are in progress

**`/aiconversations/0-project.md`** — open-projects rollup (project-level). Read if present (some CWOS deployments use the dual rollup pattern; others use only the dashboard). Note:

- Which specific projects are currently open across the repo, grouped by domain → conversation → project
- Each row shows status (`in progress` / `not started` / `planning` / `nearly complete` / `ongoing`) and next action
- The "Files needing cleanup" section at the bottom names `-tasks.md` files that don't use the canonical heading and were therefore not parsed into the rollup
- Both rollups refresh together via `refresh-work-management`; either can refresh independently via `work-status-dashboard` or `open-projects`

If the repo doesn't have either rollup authored yet, skip this step and note in the summary that the rollups aren't authored — the cold-start may still complete usefully via the foundation-files load.

### Step 3 — Check for re-entry briefs

List `/operations/cwos/memory/projects/` to see if any project re-entry briefs exist. Over time these accumulate per-project or per-spoke. If the operator's stated task involves a project that has a re-entry brief, read that brief explicitly.

### Step 4 — Produce the absorbed-state summary

Output a structured summary covering:

**Architecture absorbed:**

- The CWOS three-layer split (configuration → /AICONFIG.md, procedure → /operations/cwos/skills/, state → /operations/cwos/memory/)
- The always-restart pattern (when context needs refreshing for non-trivial reasons, restart rather than refresh in place; durable reasoning lives in git-versioned artifacts)
- Cross-repo pointer convention (Option α — `companionRepo` + `companionPath` fields, backed by a spoke-registry) for hub→spoke references, if this repo is a hub; otherwise hub-internal `companionTo` is the default

**Repo context absorbed:**

- The repo's role in its ecosystem (hub / spoke / single-repo / polyrepo)
- The repo's products / engagements / projects (varies per repo)
- The aiconversations taxonomy if used (folder naming convention)

**Current state from the work-status dashboard (if present):**

- Active threads / projects / engagements (list from dashboard)
- Paused or stalled threads worth knowing about

**Currently open projects from the open-projects rollup (if present):**

- Total currently-open projects: (count from `0-project.md`)
- Notable in-progress projects requiring near-term attention: (top 3-5 with status + next action)
- Source files needing cleanup: (count from "Files needing cleanup" section)

**Open re-entry briefs:** (list from `operations/cwos/memory/projects/`)

**Recent governance decisions:**

- Read all ADRs in `operations/cwos/memory/decisions/` and surface the most recent one or two
- Surface any ADR explicitly marked `Status: Accepted` and recent

**Maintenance discipline established:**

- `frontmatter-validate` skill for ad-hoc drift detection
- `conversational-maintenance-review` skill for quarterly orchestration
- `conversation-archiving` skill for file-size-driven chunk splits
- See `operations/cwos/skills/README.md` for the full skill inventory

### Step 5 — Stop, await operator direction

After producing the summary, ask: "Ready to proceed — what would you like to work on?" Do not initiate work until the operator gives direction.

If the operator is picking up a specific thread, invoke `run-prompt-protocol` to execute the relevant prompt or otherwise focus the session on that thread.

If the operator wants to run maintenance, invoke `conversational-maintenance-review`.

If the operator is starting fresh exploratory work, the absorbed-state summary should be enough orientation.

## Output expectations

A clean cold-start run produces a roughly 200-400-word summary covering the three-axis absorption (architecture / repo context / current state) plus a brief mention of open re-entry briefs and recent governance decisions. The operator should read it and confirm absorption was successful (or push back if something looks wrong — that's the verification step the cold-start pattern is designed to surface).

## Don't do

- **Don't modify any files** during cold-start. This is read-and-summarize only.
- **Don't auto-route to a downstream skill.** Wait for the operator to direct the next action after the summary lands.
- **Don't paraphrase the architecture from training-data priors.** Read the actual files. The whole point of cold-start is verifying you've absorbed the *current* canonical content, not whatever the model thinks CWOS means from general knowledge.
- **Don't read every conversation file in aiconversations/.** Scope is the dashboard + the canonical foundation. Specific threads get loaded later when the operator says what they want to work on.

## References

- The text-prompt equivalent in `/README.md` Session-Start Prompts section (or `/CWOS-SETUP.md` "Refreshing an existing session") — same effect; use whichever interface is more convenient
- `/CWOS-SETUP.md` "Refreshing an existing session" — covers the four scenarios (cold-start, post-compaction, post-update, mid-session-drift) and the always-restart pattern's reasoning
- Your repo's CWOS migration ADR at `/operations/cwos/memory/decisions/001-*.md`

---

[End of Skill]
