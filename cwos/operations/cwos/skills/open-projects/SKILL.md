---
name: open-projects
description: Generate or refresh the centralized open-projects rollup at /aiconversations/0-project.md by walking every aiconversations/**/*-tasks.md file, extracting each file's "Summary of Open Projects" section, and assembling a domain → conversation → project view of all currently-open project work across the repo. The rollup is a derived view; the -tasks.md files remain canonical detail. Use when refreshing the project-state surface, after significant project work has landed, during a session-start orientation, or as part of the quarterly maintenance review.
---

# Open Projects Rollup

The hub's per-conversation `-tasks.md` files are the canonical source of project state — each conversation captures its own projects close to the work that produced them. The trade-off is that visibility across the corpus requires opening each file individually. This skill solves that by generating a centralized rollup at `aiconversations/0-project.md`.

The rollup is a *derived view*: source files (`-tasks.md`) remain authoritative; the rollup is regenerated to reflect their current state. The pattern parallels the work-status dashboard at `aiconversations/0-work-status.md` — same shape (centralized derived view aggregated from distributed sources), different signal (project tracking vs work-state).

## When to invoke

- **Session-start orientation** when the operator wants to see what's open across all projects at once
- **After significant project work has landed** in one or more conversations (an active session updated several `-tasks.md` files; refresh the rollup so future scans see the changes)
- **As part of the quarterly maintenance review** — the `conversational-maintenance-review` skill should invoke this skill alongside the dashboard refresh
- **On demand** when the user asks "what's open across all my projects?" or any variant of that semantic

## When NOT to invoke

- Mid-conversation when you're updating one specific `-tasks.md` file — that's an isolated edit; the rollup refreshes when there are multiple updates worth surfacing
- When the user wants the full detail of a specific project — they should open the source `-tasks.md` file directly; the rollup is a scanning surface, not a detail surface

## Inputs

Optional `$ARGUMENTS`:

- A subfolder path (e.g., `aiconversations/<folder>/`) to limit the scan
- `--dry-run` flag to produce the rollup output without writing the file (operator inspects first)
- `--surface-cleanup` flag to include a "Files needing cleanup" section at the end of the rollup listing any `-tasks.md` files where parsing failed (default behavior surfaces these inline)

## Procedure

### Step 1 — Identify scan scope

Walk every `aiconversations/**/*-tasks.md` file. Exclude:

- Files in `aiconversations/_system/archives/` (frozen historical snapshots)
- Files in `aiconversations/archives/` (same)
- Files explicitly marked archived (📦 ARCHIVED notice in their body)

### Step 2 — For each in-scope file, extract project state

Read each file. Look for the "Summary of Open Projects" section (this is the convention prescribed by the AICONFIG.md "Tasks File Structure" guidance — a summary section at the top of every `-tasks.md` listing currently-open projects with status and next action).

For each project found in the summary:

- Project name
- Brief description of what the project is (one sentence — enough that the row makes sense without opening the source file)
- Current phase (or status if phase isn't tracked)
- Next action (the single specific next step)
- Status (`new` / `in progress` / `paused` / `blocked` — completed projects are intentionally excluded from this rollup; only currently-open projects appear here)
- Source file path
- Source file `lastUpdated` (from frontmatter)

If a file does not have a parseable "Summary of Open Projects" section, capture it as a finding: `<file> — could not parse; reason: <reason>`. Continue with other files; don't abort.

### Step 3 — Group by domain → conversation → project

The rollup's domain grouping should mirror the repo's `0-work-status.md` dashboard table set. The dashboard format is repo-specific (chevan-content uses life-domain tables; workspace-chevan uses business-domain tables; other CWOS deployments may use other groupings). This skill reads the dashboard's top-level section headings to discover the repo's domain set, then groups projects to match. If the repo does not have a `0-work-status.md` dashboard authored yet, fall back to grouping by `aiconversations/` top-level folder.

Within each domain, group by source conversation (one subsection per conversation that has any open projects). Within each conversation subsection, list each project as a bullet row.

### Step 4 — Author the rollup file

Write to `aiconversations/0-project.md`. Frontmatter:

```yaml
---
documentType: Open Projects Rollup
purpose: Centralized derived view of all currently-open projects across the repo's per-conversation -tasks.md files. Detail lives in the source files; this rollup is the scanning surface.
version: 1.0.0
lastUpdated: <today YYYY-MM-DD>
status: Active - In Use
location: /aiconversations/0-project.md
companionDashboard: /aiconversations/0-work-status.md
---
```

Body structure:

```markdown
# Open Projects

**Centralized rollup of currently-open projects across the repo.** Detail lives in per-conversation `-tasks.md` files; this view is regenerated by the `open-projects` skill. Companion to [`0-work-status.md`](0-work-status.md) (work-state dashboard) — the two surfaces answer different questions: work-status tells you *which threads need attention*; this rollup tells you *what specific projects are open*.

**Refresh discipline:** invoke `open-projects` skill on demand or as part of the quarterly maintenance review. Last refreshed: <YYYY-MM-DD>.

---

## <Domain Name 1>

### <Conversation Name>
**Source:** [`<path-to-tasks-file>`](<path-to-tasks-file>) — last updated <YYYY-MM-DD>

- **<Project Name>** — `<status>` — <one-sentence description>. Next action: <next action>.
- **<Project Name>** — ...

### <Another Conversation Name>
...

---

## <Domain Name 2>

(same shape as Section 1, per conversation, per project)

---

(continue per domain matching the work-status dashboard's table set)

---

## Files needing cleanup

(only if any -tasks files failed to parse; surface for targeted fix)

- `<file>` — <reason parsing failed>

---

[End of Document]
```

### Step 5 — Surface findings to the operator

After writing the file, report:

- Number of `-tasks.md` files scanned
- Number of projects surfaced in the rollup
- Number of files that failed to parse (surfaced in "Files needing cleanup")
- Any conversations where the active-projects list is empty (signal: either the conversation has no open project work, or the file structure prevented parsing)

### Step 6 — Stop

Don't modify the source `-tasks.md` files. The rollup is read-only-derived from them. If format inconsistencies are surfaced, the operator decides whether to fix the source files (small targeted cleanup) or accept the gaps in the rollup.

## Output expectations

A clean run on a well-maintained corpus produces a single file at `aiconversations/0-project.md` with one section per domain (matching the work-status dashboard's structure), each listing the active projects per conversation. The file is scannable by humans (under one minute to read end-to-end) and parseable by AI (the structure is consistent enough that future skills could derive further views from it).

A run with format inconsistencies surfaces them as "Files needing cleanup" findings at the bottom of the rollup. The rollup is still useful — it just has gaps that the operator can decide to fill.

## Don't do

- **Don't modify source `-tasks.md` files.** This skill is rollup-only. If `-tasks.md` files need cleanup, that's a separate operation the operator triggers.
- **Don't include completed projects in the rollup body.** The rollup is "what needs attention." Completed work shows up in the work-status dashboard's status field for the parent conversation, and the source `-tasks.md` files retain the full completed history.
- **Don't include body-text-derived TODOs.** Only structured "Summary of Open Projects" section content counts. Inline TODOs in conversation bodies are out of scope (future enhancement if needed).
- **Don't try to infer staleness.** Surface `lastUpdated` per row; let the operator (or a downstream skill like `conversational-maintenance-review`) decide what to flag as stale.

## Common adaptations

- **Scope to a subfolder** when working on a specific domain or client engagement
- **Run in `--dry-run` mode** before a destructive refresh to inspect changes
- **Run after a large batch of `-tasks.md` updates** to refresh the surface
- **Add the rollup to the cwos-cold-start skill's Step 2** — reading `0-project.md` alongside `0-work-status.md` gives a full session-start orientation

## References

- `aiconversations/0-work-status.md` — companion work-state dashboard (different signal, same pattern)
- `operations/cwos/skills/work-status-dashboard/SKILL.md` — the work-state dashboard refresh skill (parallel structure; not in this starter — author per-repo since dashboard shape varies)
- `operations/cwos/skills/conversational-maintenance-review/SKILL.md` — quarterly orchestrator that should invoke this skill alongside the dashboard refresh
- AICONFIG.md "Tasks File Structure" or equivalent project-tracking section — documents the `-tasks.md` convention this skill parses

---

[End of Skill]
