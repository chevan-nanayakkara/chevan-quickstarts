---
documentType: Open Projects Rollup
purpose: Centralized derived view of all currently-open projects across the repo's per-conversation -tasks.md files. Detail lives in the source files; this rollup is the scanning surface.
version: 1.0.0
versionNote: <one-line; document recent rollup refreshes here>
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