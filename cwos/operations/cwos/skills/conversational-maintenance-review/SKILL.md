---
name: conversational-maintenance-review
description: Run a periodic (typically quarterly) maintenance review of the aiconversations/ corpus. Orchestrates frontmatter-validate to detect dangling pointers, identifies size-threshold candidates for conversation-archiving, scans for archive-in-place candidates (status: Complete files older than 90 days, stalled threads with no activity in 60+ days, possible duplicates), and produces a structured punch list grouped by recommended action. Detection-only; does NOT auto-fix. Use when running quarterly review per the CWOS periodic-review schedule, before major restructures, or when the user asks "what maintenance does the corpus need?"
---

# Conversational Maintenance Review

Periodic detection-only sweep of `aiconversations/` to surface what needs maintenance, grouped by which CWOS skill should fix it. Designed to be cheap to run (5-10 minutes) and produce a punch list, not to do the fixing itself. The fixes happen via the individual skills (`frontmatter-validate`, `conversation-archiving`, and any repo-specific maintenance skills) plus per-item judgment calls.

This is the orchestrator that sits above the focused single-responsibility skills. It exists because periodic maintenance benefits from a *single* "what should I check today?" entry point even though the work itself decomposes into several distinct operations.

## When to invoke

- **Quarterly review** per the CWOS periodic-review schedule (`CWOS.md` "Periodic review" section). Default cadence; the reason this skill exists.
- **Before a major restructure** — capture a baseline before folder renames, file relocations, or large dedup operations. Re-running after gives you the diff.
- **When the user asks** "what maintenance does the corpus need?" or "run a maintenance review" or anything matching that semantic.
- **After a long gap** in conversational work (re-entering the hub after a multi-week break) to surface what drifted while you were away.

## When NOT to invoke

- Right after another maintenance pass — diminishing returns; no fresh drift to find.
- For a single file's maintenance — invoke the specific skill directly (`frontmatter-validate` for a single file, `conversation-archiving` for a known-oversize file).
- When you want to *fix* findings, not detect them — this skill stops at the punch list; switch to the individual skills.

## Inputs

Optional `$ARGUMENTS`:

- A subfolder path (e.g., `aiconversations/<folder>/`) to limit scope
- `--include-tasks` flag to also scan `-tasks.md` files (default: scan only `-conversation.md` files plus `_system/` and the root-level `0-*.md` files)
- `--since YYYY-MM-DD` to scope the activity-window scan from a specific date (default: 90 days ago for the status-Complete check; 60 days ago for the stalled-thread check)

If no arguments, scan every `*.md` file under `aiconversations/` except the `archives/` subtree (historical snapshots; intentionally frozen).

## Procedure

The review has four phases. Each phase produces a findings list; the final report groups findings by *which skill should fix it* so the operator can route fixes efficiently.

### Phase 1: Frontmatter validation

Invoke the `frontmatter-validate` skill (or follow its procedure inline). Capture the dangling-reference report. This phase is the same scope as that skill alone — re-running it inside this review keeps the orchestrator self-contained.

**Findings produce:** "Fix frontmatter" group in the punch list. Each entry: file + field + dangling value.

### Phase 2: Size-threshold scan

For each conversation file in scope, check size in KB:

- **Under 75KB:** no action; skip
- **75-100KB:** flag as `consider archiving at next natural break`
- **Over 100KB:** flag as `archive-now (must-archive threshold exceeded)`
- **Over 200KB:** flag as `archive-urgent` (file is becoming unprocessable)

Use the bash `ls -lh` or equivalent for size. Apply markers in the report.

**Findings produce:** "Archive by chunking" group in the punch list. Each entry: file + size + recommended urgency. The fix uses the `conversation-archiving` skill.

### Phase 3: Status + activity scan

For each conversation file's frontmatter, check `status:` and `lastUpdated:`:

- **`status: Complete` AND `lastUpdated` older than 90 days:** flag as `consider archiving in place (completed work, no recent activity)`. May be a dedup candidate or just a finished thread worth marking with an ARCHIVED notice.
- **`status: Active - ...` AND `lastUpdated` older than 60 days:** flag as `stalled thread`. Status field claims active but no recent activity — surface for review.
- **No `lastUpdated` field at all:** flag as `frontmatter-incomplete` (should have lastUpdated; add via a small frontmatter edit).
- **`status:` field missing or null:** flag as `status-missing` (should at minimum be `Active - <description>`, `Paused`, `Complete`, or `Archived - <description>`).

**Findings produce:** "Archive in place / Update status / Reconcile" group in the punch list. Each entry: file + status field + lastUpdated + recommended action. The fix uses a future `conversation-deprecate` skill (when authored in your repo), or an inline 📦 ARCHIVED notice + frontmatter edit per the pattern established in the workspace-chevan May 31, 2026 maintenance pass (the canonical worked example).

### Phase 4: Optional dedup heuristic

Scan filenames across folders. Flag potential duplicates:

- **Same filename in multiple folders** — possible duplicate work tracked in two places.
- **Era-suffixed twins** (e.g., `<name>-jan-conversation.md` and `<name>-conversation.md`).
- **Suspiciously similar topic names** across folders — easier to detect by eye than algorithmically; surface for operator review.

**Findings produce:** "Dedup decide" group in the punch list. Each entry: the duplicate pair + recommended canonical / archived choice (with reasoning). These need per-item judgment; the orchestrator surfaces them but doesn't pick.

### Phase 5: Produce the punch list

Grouped report by recommended action. Suggested format:

```markdown
# Conversational Maintenance Review

**Scope:** <scope description>
**Scan time:** <YYYY-MM-DD HH:MM>
**Files scanned:** <N>

## Summary

- Frontmatter findings: <count> (Fix via frontmatter-validate)
- Size-threshold findings: <count> (Archive via conversation-archiving)
- Status / activity findings: <count> (Archive in place via conversation-deprecate, or update status)
- Possible duplicates: <count> (Dedup decide via operator judgment)

## Group: Fix frontmatter

(From Phase 1; see frontmatter-validate output)
<entries>

## Group: Archive by chunking

(Files over 75KB; route to conversation-archiving)
<entries>

## Group: Archive in place / Update status / Reconcile

(Stalled or completed threads; route to conversation-deprecate or inline 📦 notice)
<entries>

## Group: Dedup decide

(Possible duplicates; need per-item judgment)
<entries>

## Recommended next actions

1. Run frontmatter-validate fixes first (cheapest, mechanical)
2. Address size-threshold archiving (mechanical, follows the conversation-archiving skill)
3. Walk the status / activity findings with the user; decide archive vs un-stall vs delete per item
4. Walk the dedup pairs with the user; decide canonical vs archived per pair

## Notes

- Detection-only — no fixes applied
- Files in aiconversations/_system/archives/ are intentionally excluded (frozen historical snapshots)
```

Write the report to the AI session for operator review. If `$ARGUMENTS` contains `--write-report`, also write to `working/.ai.working.conversational-maintenance-review.{YYYY-MM-DD-HHMM}/report.md` for archival.

### Phase 6: Stop

Do not apply fixes. Direct the operator to:

- Invoke `frontmatter-validate` (or proceed per its findings) for Group 1
- Invoke `conversation-archiving` per file for Group 2
- Invoke a repo-specific `conversation-deprecate` skill (if authored) or apply the 📦 ARCHIVED notice pattern inline for Group 3
- Walk Group 4 with the user per-item

## Output expectations

A clean run on a well-maintained corpus produces:

```
# Conversational Maintenance Review

Files scanned: <N>
Findings: 0

✓ Corpus is clean. No maintenance needed.
```

A run after meaningful drift produces a punch list comparable to what the workspace-chevan May 31, 2026 maintenance pass surfaced — roughly 30 findings across the four groups, with most concentrated in Group 1 (frontmatter) and Group 3 (status/activity).

## Don't do

- **Don't apply fixes.** This is the orchestrator's defining constraint. Mixing detection and fixing in one skill would re-introduce the "monolith vs composable" problem the architecture is built to avoid.
- **Don't auto-decide judgment calls.** Phase 3 and Phase 4 findings need operator judgment. Surfacing them with "recommended action" is fine; auto-routing them isn't.
- **Don't include the body-text sweep.** Body-text references (e.g., inline mentions of stale paths in prose) follow different rules from frontmatter (frontmatter must always resolve; body text can validly retain historical references). Body sweeps are higher-friction and best run separately when the user wants that level of cleanup.
- **Don't run as part of normal session-start.** This is a periodic-maintenance skill, not a session-orientation skill. Invoke on cadence or on demand.

## Common adaptations

- **Scope to a folder** when targeting a specific domain — useful for focused audits.
- **Tighten the activity windows** (`--since` flag) when running before a major review or restructure — surface things stalled for shorter periods.
- **Include `-tasks.md`** via `--include-tasks` when auditing companion tasks files for similar drift patterns.
- **Run after a CWOS migration or other architectural change** to validate the baseline before starting the next phase of work.

## References

- Your repo's CWOS migration ADR at `operations/cwos/memory/decisions/001-*.md`
- `frontmatter-validate` skill — the focused skill invoked by Phase 1
- `conversation-archiving` skill — the focused skill applied to Phase 2 findings
- `operations/cwos/reference/taxonomy.md` — CWOS vocabulary

---

[End of Skill]
