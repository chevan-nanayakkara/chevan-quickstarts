---
name: refresh-work-management
description: Refresh both work-management surfaces in sequence — the work-status dashboard at /aiconversations/0-work-status.md and the open-projects rollup at /aiconversations/0-project.md. Thin orchestrator that invokes the repo's work-status-dashboard skill then open-projects, ensuring the two derived views stay in sync. Use when refreshing the work-management surfaces, after significant work has landed across conversations and -tasks.md files, during session-start orientation, or when the user asks "what's active and what's open?" or "refresh the dashboards."
---

# Refresh Work Management

CWOS-aligned repos using the `aiconversations/` extension commonly maintain two centralized derived views for work-management state:

- **`/aiconversations/0-work-status.md`** — work-state dashboard. Tells you *which conversations need attention* (active / paused / dormant / complete). Refreshed by a repo-specific `work-status-dashboard` skill (the dashboard's table set is repo-specific — life-domain, business-domain, or other groupings — so the dashboard refresh skill itself is repo-specific even though the pattern is universal).
- **`/aiconversations/0-project.md`** — open-projects rollup. Tells you *what specific projects are open* across all conversations. Refreshed by the [`open-projects`](../open-projects/SKILL.md) skill.

The two surfaces are companions — they answer paired questions about the same underlying corpus and drift apart when refreshed independently. This skill is a thin orchestrator that refreshes both in sequence so they stay in sync.

This is **composition over collapse**: the two underlying skills remain independently invokable for edge cases (just the dashboard moved without project changes, or just `-tasks.md` updates without conversation-level changes). For the common case — "refresh everything" — invoke this orchestrator.

**Prerequisite:** the target repo must have a `work-status-dashboard` skill authored at `operations/cwos/skills/work-status-dashboard/SKILL.md` (table set adapted to the repo's content domain) and the `open-projects` skill from this starter. The `open-projects` skill auto-detects domain grouping from `0-work-status.md`'s headings, so the dashboard refresh should run first.

## When to invoke

- **Most common case:** the user asks "refresh the dashboards" / "update the work surfaces" / "what's active and what's open?" / "refresh work management" — invoke this orchestrator
- **After significant work has landed** across multiple conversations and `-tasks.md` files (both surfaces are likely stale; refresh both)
- **Session-start orientation** when the operator wants both surfaces current before working
- **As part of the quarterly maintenance review** — the `conversational-maintenance-review` skill should invoke this orchestrator alongside its other phases

## When NOT to invoke

- **Only the dashboard moved** (no `-tasks.md` updates) — invoke `work-status-dashboard` directly; skip the project rollup work
- **Only `-tasks.md` updates** (no conversation-level state changes) — invoke `open-projects` directly; skip the dashboard pass
- **Mid-conversation edits** — both refreshes are operational maintenance, not session-level work

## Inputs

Optional `$ARGUMENTS`:

- `--skip-dashboard` — skip the work-status-dashboard refresh (only run open-projects)
- `--skip-projects` — skip the open-projects refresh (only run work-status-dashboard)
- `--dry-run` — pass through to both underlying skills so neither writes its output file

## Procedure

### Step 1 — Invoke the repo's work-status-dashboard skill

Run `operations/cwos/skills/work-status-dashboard/SKILL.md` (repo-specific). Capture:

- Whether `0-work-status.md` was successfully refreshed
- Any findings (size-threshold conversation files flagged, format inconsistencies surfaced, etc.)
- The list of conversation files processed

Unless `--skip-dashboard` was passed.

### Step 2 — Invoke open-projects

Run the [`open-projects`](../open-projects/SKILL.md) skill. The dashboard has just been refreshed (Step 1), so `open-projects` can rely on `0-work-status.md`'s domain headings being current when it groups projects.

Capture:

- Whether `0-project.md` was successfully refreshed
- Number of projects surfaced in the rollup
- Number of `-tasks.md` files that failed to parse

Unless `--skip-projects` was passed.

### Step 3 — Combined report

Produce a brief combined report:

```
# Work-management surfaces refreshed

## Dashboard (0-work-status.md)
- Conversations processed: <N>
- Findings: <count>
- Status: refreshed | skipped

## Project rollup (0-project.md)
- -tasks.md files scanned: <N>
- Projects surfaced: <M>
- Files needing cleanup: <count>
- Status: refreshed | skipped

## Recommended next actions
- (route findings to the appropriate fix skill or to operator review)
```

### Step 4 — Stop

This orchestrator does not modify source files. It only invokes the two refresh skills. If findings surface, route them to the appropriate fix skill.

## Don't do

- **Don't modify the two underlying skills' procedures.** Composition pattern; if a behavior change is needed, change the focused skill.
- **Don't replace the underlying skills with inline logic.** If the orchestrator's procedure grows beyond delegation, that's a signal a new focused skill should split out.
- **Don't auto-fix findings.** Surface them; the operator decides.

## References

- `operations/cwos/skills/work-status-dashboard/SKILL.md` — the repo's focused dashboard refresh skill (repo-specific; author per repo since dashboard table set varies)
- [`open-projects`](../open-projects/SKILL.md) — the focused project rollup refresh skill
- [`conversational-maintenance-review`](../conversational-maintenance-review/SKILL.md) — quarterly orchestrator that should invoke this skill
- [`cwos-cold-start`](../cwos-cold-start/SKILL.md) — session-start orientation; reads both `0-work-status.md` and `0-project.md` after refresh

---

[End of Skill]
