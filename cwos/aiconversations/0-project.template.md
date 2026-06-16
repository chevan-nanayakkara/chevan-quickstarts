---
documentType: Open Projects Rollup
purpose: |
  Centralized view of open projects across all aiconversations/**/*-tasks.md files in this repo.
  Derived view: source of truth is per-conversation -tasks.md files; this rollup aggregates the
  "Open Projects" section from each. Refreshed by the `open-projects` skill.
status: Active
location: /aiconversations/0-project.md
companionSurface: /aiconversations/0-work-status.md
generatedBy: operations/cwos/skills/open-projects/SKILL.md
lastUpdated: {{DATE}}
version: 1.0.0
---

# Open Projects Rollup

Centralized view of open projects across all `aiconversations/**/*-tasks.md` files. Derived view; source of truth is per-conversation `-tasks.md` files.

**Companion surface**: [`/aiconversations/0-work-status.md`](0-work-status.md) — answers *"which threads need attention?"* (conversation-level activity). This rollup answers *"what specific projects are open?"* (project-level rollup).

**Refresh:** invoke the [`refresh-work-management`](../operations/cwos/skills/refresh-work-management/SKILL.md) skill (orchestrates both this rollup and `0-work-status.md`), or [`open-projects`](../operations/cwos/skills/open-projects/SKILL.md) alone for just this file.

**Prioritize:** invoke the [`prioritize-open-projects`](../operations/cwos/skills/prioritize-open-projects/SKILL.md) skill to produce a ranked tier outline (P1-P7-ish) with reasoning, identifying what to focus on this week.

---

## {{DOMAIN 1 — e.g., System & Operations}}

### [example-tasks.md](system/example-tasks.md)

- **Project 1:** Example project (replace with real entries from each tasks file's "Open Projects" section)

---

## {{DOMAIN 2 — e.g., Personal}}

### [example-tasks.md](personal/example-tasks.md)

- Example project

---

## {{DOMAIN 3 — e.g., Work / Content / Business}}

### [example-tasks.md](work/example-tasks.md)

- Example project

---

## Notes on this rollup

This rollup is regenerated periodically by the `open-projects` skill — walks every `aiconversations/**/*-tasks.md`, extracts each file's "Open Projects" section, and assembles a domain → conversation → project view.

Tasks files that follow the standard four-section layout (Summary of Open Projects / Notes/Reference / Open Projects / Closed-or-Completed Projects) with `### Project N:` numbering produce full project listings here. Files using a different structure show "See active tasks file" placeholders and would benefit from a structural pass.

**Template usage**: copy this file to `/aiconversations/0-project.md` (drop the `.template` suffix), replace `{{DATE}}` and `{{DOMAIN N}}` placeholders, then invoke the `open-projects` skill to do the first real population from this repo's actual `-tasks.md` files.