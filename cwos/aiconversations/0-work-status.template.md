---
documentType: Work-Status Dashboard
purpose: Centralized dashboard tracking activity, size, and status across all conversation files in this repo. Derived view; source of truth is per-conversation files. Refreshed by the repo-specific `work-status-dashboard` skill.
version: 1.0.0
versionNote: <one-line; document recent refreshes here>
lastUpdated: {{DATE}}
lastUpdatedNote: <optional multi-line for activity-since-last-refresh notes>
status: Active - In Use
location: /aiconversations/0-work-status.md
companionSurface: /aiconversations/0-project.md
---

# Work Status

Centralized view of conversation activity across the corpus. Answers *"which threads need attention?"* (conversation-level activity). Companion to [`/aiconversations/0-project.md`](0-project.md) which answers *"what specific projects are open?"* (project-level rollup).

**Refresh:** invoke the repo's `work-status-dashboard` skill, or the `refresh-work-management` skill to refresh both surfaces together.

**Canonical structure note:** the table set below uses `## N. Domain Name` numbering. The companion `0-project.md` rollup MUST mirror this exact numbering and label set so the two surfaces cross-reference cleanly (a conversation tracked in Table 2 here corresponds to projects listed under Section 2 there). Adapt the domain LABELS to match this repo's content shape (life-domain / business-domain / content-domain / etc.); keep the NUMBERING pattern intact.

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

---

## Conversational Work Help

Quick reference for invoking the CWOS skill set. Canonical content — same across CWOS implementation repos (chevan-content, workspace-chevan, chevan-quickstarts/cwos/) for cross-repo consistency. Adapt per-repo if a skill is repo-specific or unavailable.

### Work management — single command

- **"Refresh work management"** / **"Refresh the dashboards"** / **"What's active and what's open?"** / **"What should I work on?"** — invokes the [`refresh-work-management`](/operations/cwos/skills/refresh-work-management/SKILL.md) orchestrator. Three-step workflow: refreshes `0-work-status.md` (dashboard) + `0-project.md` (open-projects rollup) + produces a P1-P7 priority outline with "recommended focus this week" pick. Single command for the full work-management workflow.
- Append `--skip-prioritize` to run as the prior two-step refresh-only behavior.

### Work management — individual components (rarely needed; use the orchestrator above)

- **"Update work status"** / **"Refresh dashboard only"** — `work-status-dashboard` alone. **Note:** the CWOS starter does NOT ship this skill — it's repo-specific because the table set differs per repo. Author at `operations/cwos/skills/work-status-dashboard/SKILL.md` during bootstrap. Refreshes `0-work-status.md`.
- **"Refresh open projects"** / **"Update 0-project.md"** — [`open-projects`](/operations/cwos/skills/open-projects/SKILL.md) alone. Refreshes `0-project.md`.
- **"Prioritize"** / **"Priority outline"** — [`prioritize-open-projects`](/operations/cwos/skills/prioritize-open-projects/SKILL.md) alone. Read-only analysis (no file writes). Run when the rollup is already fresh.

### Conversation file maintenance

- **"Archive [conversation-name]"** / **"Check for large conversations"** — [`conversation-archiving`](/operations/cwos/skills/conversation-archiving/SKILL.md). Conversation files over 75KB should be archived to keep active threads scannable. Archive chunks preserve full history; active file retains 5-10K of recent context at a natural break point.
- **"Validate frontmatter"** / **"Check for dangling pointers"** / **"Frontmatter audit"** — [`frontmatter-validate`](/operations/cwos/skills/frontmatter-validate/SKILL.md). Detection-only — walks every conversation file's YAML frontmatter, verifies structural pointers resolve to existing paths. Reports findings; doesn't auto-fix.
- **"Run maintenance review"** / **"Quarterly review"** — [`conversational-maintenance-review`](/operations/cwos/skills/conversational-maintenance-review/SKILL.md). Periodic detection-only orchestrator (frontmatter validation + size scan + status/activity scan + dedup heuristic). Produces a punch list grouped by which fix-skill applies.

### Session orientation

- **"Cold start"** / **"Load CWOS context"** / **"Orient me"** — [`cwos-cold-start`](/operations/cwos/skills/cwos-cold-start/SKILL.md). Loads `/AICONFIG.md` + `/CWOS.md` + the work-status dashboard + open-projects rollup + memory index. Produces an absorbed-state summary. Use at the start of a fresh session, after Claude Code compaction, or when picking up the repo after a multi-week break.

### Run-prompt workflow (driving sessions from conversation files)

- **"Run prompt [User | TIMESTAMP] in [conversation-file]"** — [`run-prompt-protocol`](/operations/cwos/skills/run-prompt-protocol/SKILL.md). Reads the named prompt, executes it, writes the response back to the conversation file as an `[AI Assistant | TIMESTAMP]` entry plus a `[User | +2min]` stub. Core run-prompt workflow.

### Bootstrap and migration

- **"Migrate this repo to CWOS"** — [`cwos-migrate-from-conversational-work`](/operations/cwos/skills/cwos-migrate-from-conversational-work/SKILL.md). For repos with the predecessor `operations/conversational-work/` infrastructure. Three-session migration: foundation + skills/reference + cleanup.
- **New repo bootstrap** — see `/CWOS-SETUP.md` for the fresh-bootstrap procedure.

### Skill management

- **"Run [skill-name] skill"** — Invoke a skill's procedure. Claude Code resolves via `.claude/skills` → `/operations/cwos/skills/`.
- **"List installed skills"** — See `/operations/cwos/skills/README.md` for the inventory + descriptions.
- **"Scan for new skills"** — Check `/operations/cwos/skills/` for new `SKILL.md` files.

### Configuration sources

- **Canonical behavioral rules:** `/AICONFIG.md` (vendor-agnostic — see CWOS three-layer split). Edit directly to add or change rules.
- **Vendor bootstraps:** `/CLAUDE.md` (Claude-specific), `/AGENTS.md` (other AI tools) — thin pointers, not duplicate content.
- **Spec:** `/CWOS.md` (system specification) + `/CWOS-SETUP.md` (bootstrap procedure) + `/WHY-CWOS.md` (rationale).
- **Memory layer:** `/operations/cwos/memory/` (decisions, projects, feedback memories, archive).

### Standing rules (apply to all sessions)

- **No commits without explicit per-batch instruction.** File edits are fine; `git add` / `git commit` / `git push` / branch operations require operator authorization. Past authorization in the same session does not extend to new work. See AICONFIG.md `## CORE INSTRUCTIONS`.
- **C+E permissions posture** is the recommended starting point — auto-allow read-only Bash + Edit/MultiEdit + read-only `gh`; ask gates mutating git, mutating gh, filesystem mutations, package/network; hard-deny `rm` shapes, `git push --force*`, `git clean*`, `git reset --hard`, `git branch -D`, `git checkout --`, `git filter-branch`, `sudo`. See the chevan-content / workspace-chevan / chevan-quickstarts `.claude/settings.local.json` files for reference shapes.