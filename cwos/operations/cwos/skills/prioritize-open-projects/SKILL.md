---
name: prioritize-open-projects
description: Produce a priority-ranked outline of open projects across the corpus, grouping them into priority tiers (P1-P7-ish) with reasoning per tier and an honest "what to work on this week" recommendation. Reads from /aiconversations/0-project.md (or walks -tasks.md files directly if the rollup is missing or stale). Output is a structured analysis written to chat or back to a conversation file — does NOT modify 0-project.md, the -tasks.md files, or any other source-of-truth state. Use when the operator asks for prioritization, "what should I work on?", weekly planning, or a sanity check before a focused work block.
---

# Prioritize Open Projects

The `open-projects` skill produces a centralized rollup (`aiconversations/0-project.md`) listing what's open across the corpus. That answers *"what's open?"* but not *"what should I do first?"*. This skill closes that gap: it reads the open-projects landscape and produces a ranked analysis with reasoning per tier.

The output is **analytic**, not prescriptive: priority reflects the AI's read of time-sensitivity, dependency structure, and impact based on the data in the tasks files and recent conversation activity. The operator may agree, disagree, or shift weights. The skill produces the analysis; the operator decides what to act on.

**Read-only**: this skill does NOT modify any source-of-truth file. The output goes to chat or to the conversation file from which it was invoked, never back into `0-project.md` or `-tasks.md` files (those remain the canonical detail surface).

## When to invoke

- Operator asks "what should I work on?", "prioritize", "what's most important right now?", or any variant
- Weekly planning / start-of-week sanity check
- Returning to the corpus after a multi-week break and needing orientation on what to pick up first
- Before a focused work block when the operator wants to confirm they're spending the hour on the highest-leverage thread

## When NOT to invoke

- If the operator already named a specific project to work on — they don't need ranking; help them execute.
- If `0-project.md` doesn't exist and the corpus has fewer than ~3 active conversations — overkill. Use plain judgment in chat.
- If the question is about a single project's internal prioritization (which task within Project X to do first) — answer in-line from the project's own tasks file rather than running this skill.
- Immediately after running the skill in the same session — the answer is already in the conversation. Re-running for the same operator state burns tokens.

## Inputs

- `/aiconversations/0-project.md` — primary source. If present and recently updated, use it directly.
- `-tasks.md` files across the corpus — fallback if `0-project.md` is missing or stale (>7 days old without refresh).
- Recent activity signals: `git log --since="2 weeks ago"` for files in `aiconversations/` and `media-platform/` (or equivalent content paths) to identify which projects have momentum versus which have stalled.
- Optional operator context: any constraint the operator named (deadline, energy level, time available, blocked-by-someone-else state).

## Procedure

1. **Verify `0-project.md` is fresh.** Check `lastUpdated`. If older than 7 days OR if the operator says "after recent work landed," run `open-projects` (or `refresh-work-management`) first so the rollup is current.

2. **Walk the rollup and gather signals per project:**
   - Time-sensitivity (external deadline, response window, calendar trigger)
   - Income / career impact (job search, billable work, publication cadence)
   - Dependency status (blocked vs unblocked; waiting on external party)
   - Recent activity (touched in last 2 weeks? stalled for months?)
   - Operator context if provided (e.g., "I have 2 hours" → favor tractable items)

3. **Group into priority tiers.** Suggested taxonomy (adapt to repo):
   - **P1 — Time-sensitive with external clock** (applications submitted N weeks ago needing follow-up; press deadlines; legal/financial response windows)
   - **P2 — Active production with momentum** (publishing pipeline, billable client work, in-flight features)
   - **P3 — Time-anchored but lower urgency** (phased plans on multi-month cadence)
   - **P4 — Recurring volunteer / civic / community work** (can pause without consequence in sole-operator context)
   - **P5 — Personal infrastructure** (important non-urgent; finance baseline, life-admin)
   - **P6 — Infrastructure / operations / tooling** (low urgency; do in slack time)
   - **P7 — Closure / maintenance scan** (stale projects to close, deferred items, duplicate threads)
   - Adjust tier count and labels to match the corpus. Don't force a project into a tier that misrepresents it.

4. **Within each tier, name specific projects and tasks files.** Use markdown links to the source tasks files so the operator can click through.

5. **Write an honest "recommended focus this week" note.** Pick 1-3 priorities. State the trade-off (what's deprioritized). Don't list everything as P1 — the point of the analysis is choosing.

6. **Flag stale or closure-candidate projects.** Projects with no movement in 60+ days, projects whose underlying premise has changed, projects that look duplicate. Recommend closure or restructuring, but don't auto-close.

7. **Output structure:**
   - Tier list (P1, P2, ...) with one paragraph of reasoning per tier explaining why projects in that tier rank where they do
   - Per-tier project bullets with links
   - "Recommended focus this week" section (1-3 picks + trade-off statement)
   - "Closure / maintenance scan" section (stale candidates)

## Outputs

Structured analysis written to:

- Chat (default), OR
- Conversation file (if invoked from one — follow the `run-prompt-protocol` pattern, write as an `[AI Assistant | TIMESTAMP]` entry plus a `[User | +2min]` stub)

**Does NOT modify:**
- `/aiconversations/0-project.md` (canonical rollup; refreshed by `open-projects`)
- `/aiconversations/0-work-status.md` (canonical dashboard; refreshed by `work-status-dashboard`)
- Any `-tasks.md` file

If the operator agrees with the analysis and wants to close stale projects or restructure tiers in the source files, that's a separate operator-driven action invoking the relevant skill (`open-projects` re-run, manual tasks-file edit, etc.).

## Verification

- The output has a clear tier structure with reasoning, not just a flat list
- Each project named in the output appears in `/aiconversations/0-project.md` or a real `-tasks.md` file (no hallucinated projects)
- The "recommended focus this week" section names 1-3 picks (not "all of them")
- No source-of-truth file was modified (run `git status` to confirm; only the conversation file should show changes if the response was written there)

## References

- Related skills: [`open-projects`](../open-projects/SKILL.md) (provides the rollup this skill analyzes), [`refresh-work-management`](../refresh-work-management/SKILL.md) (refreshes both dashboards before analysis if either is stale), [`work-status-dashboard`](../work-status-dashboard/SKILL.md) (companion surface for conversation-level activity)
- AICONFIG.md `## STANDARDS - OPERATIONS` → "Tasks File Structure" for the standard structure of the source files this skill reads