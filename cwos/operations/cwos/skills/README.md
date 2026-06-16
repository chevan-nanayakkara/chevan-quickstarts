# operations/cwos/skills/ — Universal CWOS Skills

Agent Skills (per Anthropic's December 2025 standard) for the CWOS architecture. Each skill is a directory containing a `SKILL.md` with YAML frontmatter (`name`, `description`) and a procedural body.

Universal skills only — these apply to any CWOS-aligned repo regardless of its content domain. Repo-specific skills (essay publishing, dashboard refresh schemas, etc.) belong in the target repo's own `operations/cwos/skills/` directory, not here.

For the Agent Skills format spec, see [`../reference/agent-skills-standard.md`](../reference/agent-skills-standard.md).

---

## Skills in this starter

**Core workflow:**

- **[`run-prompt-protocol/`](run-prompt-protocol/SKILL.md)** — Execute a "run prompt" instruction by reading a specified prompt at a timestamp in a conversation file, executing the instruction, then writing the response back as an `[AI Assistant | TIMESTAMP]` entry plus a `[User | +2min]` stub. The core run-prompt workflow used in any repo with the `aiconversations/` extension.
- **[`conversation-archiving/`](conversation-archiving/SKILL.md)** — Archive content from a conversation file that has exceeded the size threshold. Use when a conversation file is over 75KB and needs to be split into historical archive chunks while preserving an active working file under threshold.
- **[`cwos-migrate-from-conversational-work/`](cwos-migrate-from-conversational-work/SKILL.md)** — Migrate a repository from the older `operations/conversational-work/` infrastructure to the CWOS architecture. Clean-break approach: build `operations/cwos/` fresh, mark predecessor LEGACY. Three-session structure (Foundation / Skills + reference / Migration + cleanup).

**Maintenance lane** (detection-only; codifies the periodic-review and drift-detection patterns established in the workspace-chevan May 31, 2026 maintenance pass):

- **[`frontmatter-validate/`](frontmatter-validate/SKILL.md)** — Detection-only skill: walks every `aiconversations/**/*.md`, parses YAML frontmatter, checks every structural pointer (`companionTo`, `archives`, `parentConversation`, `subConversations`, `relatedDocuments`, `location`, `companionTasks`, optionally `companionRepo`/`companionPath`) against the filesystem, reports dangling refs file-by-file. Does NOT auto-fix. Run quarterly per the CWOS periodic review schedule, or after any folder rename / restructure. Codifies the 4-rule maintenance pattern (A/B/C/D) for detection.
- **[`conversational-maintenance-review/`](conversational-maintenance-review/SKILL.md)** — Periodic detection-only orchestrator. Runs the four-phase sweep (frontmatter validation, size-threshold scan, status/activity scan, optional dedup heuristic) and produces a punch list grouped by which CWOS skill should fix each finding. Invokes `frontmatter-validate` for Phase 1; surfaces findings for `conversation-archiving` (Phase 2) and any repo-specific deprecation/archive skill (Phase 3). Designed for the CWOS quarterly review schedule.

**Session orientation:**

- **[`cwos-cold-start/`](cwos-cold-start/SKILL.md)** — Load full CWOS context into the current AI session by reading the canonical files (/AICONFIG.md, /README.md, /CWOS.md, /operations/cwos/README.md, /operations/cwos/memory/MEMORY.md) plus the work-status dashboard and the memory index, then produce an absorbed-state summary covering architecture / repo context / current active work / open re-entry briefs / recent governance decisions. Read-only. Invokable equivalent of the cold-start prompt template in the repo's README.md Session-Start Prompts.

**Project management:**

- **[`open-projects/`](open-projects/SKILL.md)** — Generate or refresh the centralized open-projects rollup at `/aiconversations/0-project.md` by walking every `aiconversations/**/*-tasks.md` file, extracting each file's "Summary of Open Projects" section, and assembling a domain → conversation → project view. Companion to a repo-specific work-status dashboard — same shape (centralized derived view aggregated from distributed sources), different signal (project tracking vs work-state). The two surfaces answer different questions: work-status tells you *which threads need attention*; this rollup tells you *what specific projects are open*. Authored 2026-06-02 to codify the visibility solution for distributed `-tasks.md` files.
- **[`refresh-work-management/`](refresh-work-management/SKILL.md)** — Thin orchestrator that refreshes both `0-work-status.md` (via the repo's `work-status-dashboard` skill) and `0-project.md` (via `open-projects`) in sequence, ensuring the two derived views stay in sync. **This is the recommended common-case invocation** for refreshing work-management surfaces; the two underlying skills remain available for edge cases (only-dashboard or only-projects refreshes). Authored 2026-06-02 alongside `open-projects` to codify the composition pattern (over collapsing the two skills into one). Requires the repo to author its own `work-status-dashboard` skill (table set is repo-specific).
- **[`prioritize-open-projects/`](prioritize-open-projects/SKILL.md)** — Read-only analytic skill that produces a priority-ranked outline of open projects across the corpus (P1-P7-ish tiers with reasoning) plus an honest "recommended focus this week" pick of 1-3 items. Reads `/aiconversations/0-project.md` (or walks `-tasks.md` files directly if the rollup is missing or stale). Does NOT modify source-of-truth files — analysis goes to chat or back to the invoking conversation file. Closes the gap between `open-projects` (which says *what's open*) and operator decision-making (which needs *what to do first*). Authored 2026-06-16.

**Template:**

- **[`_template/`](_template/SKILL.md)** — Agent Skills format template. Copy when authoring new skills in a target repo.

## Authoring repo-specific skills

After deploying CWOS to a target repo, author skills for that repo's specific workflows. The pattern:

1. Identify a multi-step procedure that's repeated across sessions (not a one-off, not a single rule).
2. Copy `_template/SKILL.md` into a new directory: `<skill-name>/SKILL.md`.
3. Write a specific `description` field — should be specific enough that an AI tool reading only the description (not the body) can determine when to invoke this skill.
4. Write the procedure body with explicit steps, branching where needed, and verification commands.
5. Reference canonical content (AICONFIG.md sections, other skills, reference docs) rather than duplicating.

See [`../reference/agent-skills-standard.md`](../reference/agent-skills-standard.md) for the full format spec and authoring guidance.

## Skill format quick reference

```markdown
---
name: skill-name
description: Specific description of when this skill applies and what it does.
---

# Skill Name

Multi-step procedure body. Markdown formatting. Reference other docs.

If the skill takes arguments, reference them as $ARGUMENTS in the body.
```

For supporting files (scripts, references, assets), use the standard Agent Skills directory structure: `scripts/`, `references/`, `assets/`.

## When skills are useful

- **Multi-step workflows with branching.** "When the user asks for X, first check Y, then if Z is true do A else do B."
- **Format enforcement.** "Generate a release-notes entry with these required sections."
- **Domain-specific procedures.** "When the user asks to archive a conversation file, follow this procedure."

## When skills are overkill

- **Single-step rules** ("never use markdown tables") → AICONFIG.md (configuration).
- **Always-on conventions** ("use Conventional Commits format") → AICONFIG.md (configuration).
- **One-time procedures** → a one-off in a conversation file, not a skill.

The decision tree lives in `CWOS.md` and `AICONFIG.md` `## CANONICAL SOURCE NOTE` (three-layer split: configuration / state / procedure).

---

[End of Document]
