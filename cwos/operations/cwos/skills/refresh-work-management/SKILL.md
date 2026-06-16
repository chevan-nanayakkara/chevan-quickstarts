---
name: refresh-work-management
description: Refresh both work-management surfaces then produce a priority outline — invokes the repo's work-status-dashboard skill, then open-projects, then prioritize-open-projects in sequence. Refreshes /aiconversations/0-work-status.md and /aiconversations/0-project.md, then produces a P1-P7-style priority-ranked analysis with a "recommended focus this week" pick. This is the recommended single-command for the full work-management workflow. Use when refreshing the work-management surfaces, after significant work has landed across conversations and -tasks.md files, during session-start orientation, weekly planning, or when the user asks "what's active and what's open?" / "refresh the dashboards" / "what should I work on?"
---

# Refresh Work Management

CWOS-aligned repos using the `aiconversations/` extension commonly maintain two centralized derived views for work-management state plus an analytic layer on top:

- **`/aiconversations/0-work-status.md`** — work-state dashboard. Tells you *which conversations need attention* (active / paused / dormant / complete). Refreshed by a repo-specific `work-status-dashboard` skill (the dashboard's table set is repo-specific — life-domain, business-domain, or other groupings — so the dashboard refresh skill itself is repo-specific even though the pattern is universal).
- **`/aiconversations/0-project.md`** — open-projects rollup. Tells you *what specific projects are open* across all conversations. Refreshed by the [`open-projects`](../open-projects/SKILL.md) skill.
- **Priority outline** (analytic, no file output) — Tells you *what to do first*. Produced by the [`prioritize-open-projects`](../prioritize-open-projects/SKILL.md) skill, which reads the freshly-regenerated `0-project.md` and groups projects into priority tiers (P1-P7-ish) with reasoning per tier and an honest "recommended focus this week" pick. Read-only — does NOT modify any source-of-truth file.

The three surfaces are companions and this skill chains them so a single invocation produces the full picture. The first two writes update state; the third reads that state and produces actionable analysis.

This is **composition over collapse**: the three underlying skills remain independently invokable for edge cases. For the common case — "refresh everything and tell me what to work on" — invoke this orchestrator.

**Prerequisite:** the target repo must have a `work-status-dashboard` skill authored at `operations/cwos/skills/work-status-dashboard/SKILL.md` (table set adapted to the repo's content domain) and the `open-projects` + `prioritize-open-projects` skills from this starter. The `open-projects` skill auto-detects domain grouping from `0-work-status.md`'s headings, so the dashboard refresh should run first.

## When to invoke

- **Most common case:** the user asks "refresh the dashboards" / "update the work surfaces" / "what's active and what's open?" / "refresh work management" / "what should I work on?" — invoke this orchestrator
- **After significant work has landed** across multiple conversations and `-tasks.md` files (both surfaces are likely stale; refresh both and re-prioritize)
- **Session-start orientation** when the operator wants both surfaces current plus a priority outline before working
- **Weekly planning** — refresh state and get a fresh priority ranking
- **As part of the quarterly maintenance review** — the `conversational-maintenance-review` skill should invoke this orchestrator alongside its other phases

## When NOT to invoke

- **Only the dashboard moved** (no `-tasks.md` updates) — invoke `work-status-dashboard` directly; skip the project rollup work
- **Only `-tasks.md` updates** (no conversation-level state changes) — invoke `open-projects` directly; skip the dashboard pass
- **Just want the priority outline against existing rollup** — invoke `prioritize-open-projects` directly (skips the two refresh steps)
- **Mid-conversation edits** — refreshes are operational maintenance, not session-level work

## Inputs

Optional `$ARGUMENTS`:

- `--skip-dashboard` — skip the work-status-dashboard refresh
- `--skip-projects` — skip the open-projects refresh
- `--skip-prioritize` — skip the prioritization analysis (refresh-only mode; the v1.5.x and earlier behavior)
- `--dry-run` — pass through to all underlying skills so none write their output file (the dashboard and rollup refreshes simulate; prioritize is already read-only)

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

### Step 3 — Invoke prioritize-open-projects

Run the [`prioritize-open-projects`](../prioritize-open-projects/SKILL.md) skill. The open-projects rollup has just been refreshed (Step 2), so the prioritization reads the freshest possible state.

Capture:

- Priority tiers produced (P1-P7-ish, taxonomy may adapt per repo)
- "Recommended focus this week" pick (1-3 projects)
- Any stale / closure-candidate projects flagged

This step is read-only — does not modify any source-of-truth file. The analysis output is appended to the combined report (Step 4) for operator review.

Unless `--skip-prioritize` was passed.

### Step 4 — Combined report

Produce a brief combined report:

```
# Work-management surfaces refreshed + prioritized

## Dashboard (0-work-status.md)
- Conversations processed: <N>
- Findings: <count>
- Status: refreshed | skipped

## Project rollup (0-project.md)
- -tasks.md files scanned: <N>
- Projects surfaced: <M>
- Files needing cleanup: <count>
- Status: refreshed | skipped

## Priority outline (analytic, no file output)
- Tiers produced: <list>
- Recommended focus this week: <1-3 projects>
- Closure candidates flagged: <count>
- Status: produced | skipped

## Recommended next actions
- (route findings to the appropriate fix skill or to operator review)
```

### Step 5 — Stop

This orchestrator does not modify source files. The dashboard + rollup refreshes are write operations on derived views (regenerable, not source-of-truth). The prioritization step is read-only. If findings surface, route them to the appropriate fix skill.

## Don't do

- **Don't modify the two underlying skills' procedures.** Composition pattern; if a behavior change is needed, change the focused skill.
- **Don't replace the underlying skills with inline logic.** If the orchestrator's procedure grows beyond delegation, that's a signal a new focused skill should split out.
- **Don't auto-fix findings.** Surface them; the operator decides.

## References

- `operations/cwos/skills/work-status-dashboard/SKILL.md` — the repo's focused dashboard refresh skill (repo-specific; author per repo since dashboard table set varies)
- [`open-projects`](../open-projects/SKILL.md) — the focused project rollup refresh skill
- [`prioritize-open-projects`](../prioritize-open-projects/SKILL.md) — the read-only analytic step that produces the priority outline
- [`conversational-maintenance-review`](../conversational-maintenance-review/SKILL.md) — quarterly orchestrator that should invoke this skill
- [`cwos-cold-start`](../cwos-cold-start/SKILL.md) — session-start orientation; reads both `0-work-status.md` and `0-project.md` after refresh
