---
documentType: Work Status Dashboard
purpose: |
  Centralized dashboard tracking activity, size, and status across all conversation files in this repo.
  Derived view: source of truth is per-conversation files; this dashboard aggregates last-activity signals.
  Refreshed by the `work-status-dashboard` skill (must be authored repo-specifically — table set differs per repo).
status: Operational dashboard tracking conversational work
location: /aiconversations/0-work-status.md
companionSurface: /aiconversations/0-project.md
lastUpdated: {{DATE}}
lastUpdatedNote: |
  Template instantiated {{DATE}} as part of CWOS bootstrap. Customize the table set below for this repo's content domains.
---

# Work Status

Centralized view of conversation activity across the corpus. Answers *"which threads need attention?"* (conversation-level activity). Companion to [`/aiconversations/0-project.md`](0-project.md) which answers *"what specific projects are open?"* (project-level rollup).

**Refresh:** invoke the repo's `work-status-dashboard` skill, or the `refresh-work-management` skill to refresh both dashboards together.

**Template note:** the table set below is a starting point. Adapt the domain groupings to your repo's actual content shape — life-domain (Active Work / Personal / Job Search / Recurring / System) for a personal repo, business-domain (Client Engagements / Internal Projects / Operations) for a consulting hub, content-domain (Essays / Posts / Distribution / Strategy) for a media-platform repo, etc. The dashboard structure stays consistent (sorted-by-most-recent-activity tables); the table set varies.

---

## 1. {{TABLE 1 NAME — e.g., Active Work & Initiatives}}

{{Description: what kind of conversation belongs in this table, and what activity here signals to the operator.}}

| # | Conversation | Folder | Size | Last Activity Summary |
|---|--------------|--------|------|----------------------|
| 1 | [example-conversation.md](/aiconversations/example/example-conversation.md) | example | 0K | **{{DATE}}** - Example row; replace with real entries |

---

## 2. {{TABLE 2 NAME — e.g., Recurring / Hobby / Volunteer}}

{{Description.}}

| # | Conversation | Folder | Size | Last Activity Summary |
|---|--------------|--------|------|----------------------|
| 1 | example | example | 0K | **{{DATE}}** - Example row |

---

## 3. {{TABLE 3 NAME — e.g., Media Platform / Publishing}}

| # | Conversation | Folder | Size | Last Activity Summary |
|---|--------------|--------|------|----------------------|
| 1 | example | example | 0K | **{{DATE}}** - Example row |

---

## 4. {{TABLE 4 NAME — e.g., System & Operations}}

| # | Conversation | Folder | Size | Last Activity Summary |
|---|--------------|--------|------|----------------------|
| 1 | example | example | 0K | **{{DATE}}** - Example row |

---

## 5. {{TABLE 5 NAME — e.g., Job Search / Client Engagement / Closed}}

| # | Conversation | Folder | Size | Last Activity Summary |
|---|--------------|--------|------|----------------------|
| 1 | example | example | 0K | **{{DATE}}** - Example row |

---

## Table-set adaptation notes

When bootstrapping this dashboard for a new repo:

1. Decide the table count (usually 3-5) and what each table groups.
2. Replace the placeholder headings (`{{TABLE N NAME}}`) with real domain labels.
3. For each conversation file in `aiconversations/`, decide which table it belongs to and add a row.
4. Sort rows within each table by most-recent activity (newest first).
5. Update size + last-activity summary as part of routine maintenance (the `work-status-dashboard` skill automates this if authored for the repo).

**Note on tables**: AICONFIG's no-markdown-tables rule applies to general document content. This dashboard is an exception — the table format is structural to the dashboard's purpose, and the file is consumed by tools that render tables (GitHub, VS Code preview, the `work-status-dashboard` skill). If your operator's primary view doesn't render tables well, fall back to bullet lists with the same field structure (`**Conversation:** ... | **Folder:** ... | **Size:** ... | **Last Activity:** ...`).

**Template usage**: copy this file to `/aiconversations/0-work-status.md` (drop the `.template` suffix), replace `{{DATE}}` and table-set placeholders, then begin populating real rows.