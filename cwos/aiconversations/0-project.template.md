---
documentType: Open Projects Rollup
purpose: Centralized derived view of all currently-open projects across the repo's per-conversation -tasks.md files. Detail lives in the source files; this rollup is the scanning surface.
version: 1.1.0
versionNote: |
  June 17, 2026 — added Priority View section template at top of file. Manually maintained surface for the tiered priority outline ("what to work on next?"); distinct from the auto-derived domain rollup body. Pattern originated in workspace-chevan June 17, 2026.
lastUpdated: {{DATE}}
status: Active - In Use
location: /aiconversations/0-project.md
companionSurface: /aiconversations/0-work-status.md
generatedBy: /operations/cwos/skills/open-projects/SKILL.md
---

# Open Projects Rollup

Centralized view of open projects across all `aiconversations/**/*-tasks.md` files. Derived view; source of truth is per-conversation `-tasks.md` files.

**Companion surface**: [`/aiconversations/0-work-status.md`](0-work-status.md) — answers *"which threads need attention?"* (conversation-level activity). This rollup answers *"what specific projects are open?"* (project-level rollup).

**Refresh:** invoke the [`refresh-work-management`](/operations/cwos/skills/refresh-work-management/SKILL.md) skill (orchestrates dashboard + rollup + prioritization in one command), or [`open-projects`](/operations/cwos/skills/open-projects/SKILL.md) alone for just this file.

**Prioritize:** invoke the [`prioritize-open-projects`](/operations/cwos/skills/prioritize-open-projects/SKILL.md) skill to produce a ranked tier outline (P1-P7-ish) with reasoning, identifying what to focus on this week.

**Canonical structure note:** the section numbering below (`## 1. Domain Name` through `## N. Domain Name`) MUST mirror the table set in `/aiconversations/0-work-status.md`. This makes the cross-reference between the two surfaces explicit — a project listed under `## 2. Client Engagements` here corresponds to a conversation tracked in `## 2. Client Engagements` over there. Numbering parity is the canonical pattern; don't drop the numbers or use different domain labels between the two files.

---

## Priority View

*Manually maintained surface. Sits above the auto-derived domain rollup below. Captures the tiered priority outline (P1–P4 style) — the answer to "what should I work on next?" Distinct from the rest of this file, which answers "what specific projects are open?" The [`prioritize-open-projects`](/operations/cwos/skills/prioritize-open-projects/SKILL.md) skill produces an ephemeral version of this analysis on demand; this section is the persistent home where the latest tiered view lives. Refresh manually when tiers shift, when items close, or when new cross-cutting work surfaces.*

**Refresh discipline:** when an item below ships, mark [X] here AND in the source `-tasks.md` file (canonical state). When tiers shift, update the recommendation footer. The `open-projects` skill that regenerates the rest of this file deliberately preserves this section — do not rely on the skill to update it.

**Pattern source:** introduced in workspace-chevan June 17, 2026; adopted into chevan-content the same day; codified here as the recommended starting shape for new CWOS-aligned repos.

**Last refresh:** {{DATE — populate when first used}}

### Tier 1 — small unblockers (30-45 min focused block)

- [ ] *(example)* **{{ITEM_NAME}} — {{short description}}.** *Source:* {{tasks-file-path}} {{project-name}}.

*(Populate with the actual priorities — items pulled from across `-tasks.md` files or surfaced from conversations.)*

### Tier 2 — codify decisions while context is fresh

- [ ] *(example)* **{{ITEM_NAME}} — {{description}}.** *Source:* {{location}}.

### Tier 3 — engagement-critical + housekeeping

- [ ] *(example)* **{{ITEM_NAME}} — {{description}}.** *Source:* {{location}}.

### Tier 4 — ongoing tracks at their own cadence

- [ ] *(example)* **{{ITEM_NAME}} — {{description}}.** *Source:* {{location}}.

**Recommendation as of {{DATE}}:** *(short narrative — which tier is the highest-leverage 30-45 minute next session, and any cross-cutting context. Optional but useful.)*

---

## 1. {{DOMAIN 1 — match Table 1 in 0-work-status.md}}

### [example-tasks.md](example/example-tasks.md)

- **Project 1:** Example project (replace with real entries from each tasks file's "Open Projects" section)
- **Project 2:** Another example

---

## 2. {{DOMAIN 2 — match Table 2 in 0-work-status.md}}

### [example-tasks.md](example/example-tasks.md)

- Example project

---

## 3. {{DOMAIN 3 — match Table 3 in 0-work-status.md}}

### [example-tasks.md](example/example-tasks.md)

- Example project

---

## 4. {{DOMAIN 4 — match Table 4 in 0-work-status.md}}

### [example-tasks.md](example/example-tasks.md)

- Example project

---

## 5. {{DOMAIN 5 — match Table 5 in 0-work-status.md}}

### [example-tasks.md](example/example-tasks.md)

- Example project

---

## Files needing cleanup

(Optional section) — `-tasks.md` files that don't yet follow the standard four-section layout, or whose Open Projects section has no `### Project N:` H3 entries. The `open-projects` skill surfaces these so they can be groomed. Empty if the corpus is clean.

---

## Notes on this rollup

This rollup is regenerated periodically by the `open-projects` skill — walks every `aiconversations/**/*-tasks.md`, extracts each file's "Open Projects" section, and assembles a domain → conversation → project view.

Tasks files that follow the standard four-section layout (Summary of Open Projects / Notes/Reference / Open Projects / Closed / Completed Projects) with `### Project N:` numbering produce full project listings here. Files using a different structure show "See active tasks file" placeholders in the rollup and surface in the "Files needing cleanup" section above; address them at the next meaningful edit per the standard's "don't force a one-off reshuffle" rule.

See `AICONFIG.md` `## STANDARDS - OPERATIONS` → `### Project tracking` → `#### Tasks File Structure (Standard)` for the canonical tasks-file layout.

**Template usage**: copy this file to `/aiconversations/0-project.md` (drop the `.template` suffix), replace `{{DATE}}` and `{{DOMAIN N}}` placeholders to match the table set in `0-work-status.md`, then invoke the `open-projects` skill to do the first real population from this repo's actual `-tasks.md` files.