# AI Configuration Template — {{REPO_NAME}}

> **Template note:** copy this file to `/AICONFIG.md` at the root of your target repo when bootstrapping CWOS. Replace `{{REPO_NAME}}`, `{{PROJECT_DESCRIPTION}}`, `{{REPO_STRUCTURE}}`, etc. Adjust standards as your repo evolves. The skeleton below covers the universal sections (canonical source note, core instructions, writing standards, file conventions, git workflow); fill in repo-specific sections (project context, repository context) for your own situation.

---

Repository: {{REPO_NAME}}

Version: 1.0.0

Last Updated: {{DATE}}

---

## CANONICAL SOURCE NOTE

**This file (AICONFIG.md) is the canonical, vendor-agnostic source of truth for all behavioral rules in this repository.** Vendor-specific files (`CLAUDE.md`, `AGENTS.md`, individual memory files at `operations/cwos/memory/`, etc.) should reference this file, not duplicate its content. New behavioral rules belong here first; vendor-specific files exist only as bootstraps and lightweight session-spanning reminders.

**For the operating system specification** (Conversational Work Operating System: hub-and-spoke variants, the three-layer split, vendor-agnostic principle, aiconversations extension, foundation files, deployment scenarios), see [`/CWOS.md`](CWOS.md). For setting up the same architecture in a new repo, see [`/CWOS-SETUP.md`](CWOS-SETUP.md). For the implementation layer in this repo (memory, skills, reference), see [`/operations/cwos/README.md`](operations/cwos/README.md). For vocabulary used across these docs, see [`/operations/cwos/reference/taxonomy.md`](operations/cwos/reference/taxonomy.md).

**Always-restart pattern.** This repo's operational preference: when context needs refreshing, restart the AI session rather than refresh in place. All reasoning lives in durable artifacts (conversation files, memory at `operations/cwos/memory/`, AICONFIG.md, CWOS.md), so restart is cheap and produces a cleaner state than refresh.

### Where new behavioral content goes (three-layer split)

When codifying new AI behavior, choose by shape, not by vendor. CWOS's three-layer split (configuration / state / procedure):

1. **Configuration — behavioral rules** (always-on guidance)
   - **Home:** `/AICONFIG.md`, in the appropriate section.
   - **Why:** Vendor-agnostic plain text. Auto-loaded once at session start by every AI tool's bootstrap.
2. **Procedure — multi-step workflows** (with branching, sequencing, runtime decisions)
   - **Home:** `/operations/cwos/skills/<name>/SKILL.md` in Agent Skills standard format.
   - **Why:** Vendor-agnostic, cross-vendor portable. Loaded on demand. Zero baseline token cost.
3. **State — decisions, project status, accumulated learnings**
   - **Home:** `/operations/cwos/memory/` (decisions/, projects/<spoke>/, archive/, plus feedback memories indexed in MEMORY.md).

**Vendor-specific slash-command UX** is a fourth surface that sits on top of any of the above. Always a thin shell referencing canonical content; never duplicates.

**Decision tree:**

- Single always-on rule? → AICONFIG.md (configuration).
- Multi-step procedure? → operations/cwos/skills/<name>/SKILL.md (procedure).
- A decision worth recording? → operations/cwos/memory/decisions/NNN-slug.md (state, ADR).
- A re-entry brief? → operations/cwos/memory/projects/<spoke>/re-entry.md (state).
- One-time note? → not a rule. Conversation file or `working/`.

### What belongs in AICONFIG.md (writing standards, file conventions, brand guidelines, not just "AI rules")

Writing standards, file-naming conventions, brand guidelines, document-layout rules, voice and tone preferences — these aren't only "AI-behavioral rules." They're standards that apply to anyone (human or AI) working in the repo, and AICONFIG.md is the canonical home for all of them. Three reasons:

1. AICONFIG.md is the one file AI tools auto-load, so embedded standards get applied automatically every session
2. It serves as the canonical reference for humans needing to check conventions
3. Keeping standards in one place avoids the multi-file drift that older install-pipeline patterns produced (where standards lived in scattered template files that had to be "installed" into a config — each install drifted slightly from the source)

The distinction between "AI rules" and "project conventions" is blurry in practice. Both apply to whoever is doing work. Put them in AICONFIG.md (under `## STANDARDS - WRITING` / `## STANDARDS - OPERATIONS` / `## STANDARDS - MAINTENANCE` as appropriate) and let one canonical source serve both audiences.

If you're migrating from an older `operations/conversational-work/` predecessor that used the install-pipeline pattern, see [`/CWOS-SETUP.md`](CWOS-SETUP.md) "Standards consolidation" for the procedure that lifts template content into the canonical AICONFIG.md sections.

---

## ⚠️ CORE INSTRUCTIONS (Always Follow)

### What to Do

- **Read only files explicitly specified** — never auto-scan.
- **Match context to task** — writing voice for content, precision for configuration.
- **Ask before making assumptions** — covers personal and technical content.
- **Show planned changes before changing files.**
- **Preserve intent** — don't over-polish writing, don't break configurations.

### What NOT to Do

- **Don't auto-read files** — respect privacy and avoid irrelevant context.
- **Don't assume all writing needs editing** — some is just thinking.
- **Don't add content not requested** — stay focused.
- **Don't commit, push, or run destructive git operations without explicit instruction** — file edits are fine; `git add` / `git commit` / `git push` / branch operations require explicit per-task authorization. Past authorization does not extend to new work.

### When Asked for Git Work

1. File edits and read-only `git status` / `git log` / `git diff` are fine without asking.
2. `git add`, `git commit`, `git push`, branch creation/deletion, force-push, reset, rebase — require explicit go-ahead per task.
3. A "commit and push" instruction authorizes that one batch only.
4. When work is done, summarize what changed and ask whether to commit and how to group.
5. Never bypass hooks (`--no-verify`), never amend pushed commits, never force-push to main without explicit instruction.
6. **Commit message format:** use [Conventional Commits](operations/cwos/reference/conventional-commits.md) (`feat:`, `fix:`, `docs:`, `chore:`, `refactor:` with optional scope like `feat(api):` or `docs(operations):`).

---

## PROJECT CONTEXT

{{PROJECT_DESCRIPTION}}

(Describe what this repo is, who maintains it, what work happens here, and any cross-cutting context an AI session needs to understand the work.)

---

## REPOSITORY CONTEXT

{{REPO_STRUCTURE}}

(Describe the top-level folder structure, what each major folder contains, and any naming conventions specific to this repo. Use a tree diagram if helpful.)

Example skeleton:

```
{{REPO_NAME}}/
├── AICONFIG.md              # This file (canonical AI configuration)
├── CWOS.md                  # CWOS specification (copied from upstream)
├── CWOS-SETUP.md            # Bootstrap procedure
├── CLAUDE.md                # Thin Claude Code pointer to AICONFIG.md
├── AGENTS.md                # Thin cross-vendor pointer
├── README.md                # Repo overview
├── operations/
│   └── cwos/                # CWOS implementation layer
│       ├── memory/          # Accumulated state (ADRs, projects, archive)
│       ├── skills/          # Reusable procedures (Agent Skills format)
│       └── reference/       # Standing knowledge (taxonomy, standards)
└── (your domain folders)
```

---

## STANDARDS - WRITING

### Voice and Tone

- **Authentic and personal** — write like you talk, not like a corporate brand.
- **Direct** — say what you mean, drop the hedging.
- **Conversational rhythm** — use ellipses (...) for pauses instead of em-dashes.

### Avoid AI Patterns and Watermarks

- **No em-dashes (—).** Use ellipses (...) for pauses, commas for clauses, or restructure the sentence.
- **No "load-bearing" used figuratively.** ("Load-bearing pillar," "load-bearing assumption," etc.) Pet peeve; needs active self-policing because it's a common AI tic.
- **No dramatic exposition.** No theatrical "here's the thing," no ALL CAPS for emphasis, no "imagine if."
- **No meta-evaluation of user input.** Never open responses with praise of the user's question ("Good catch," "Good point," "great question," "insightful," "the heart of the matter," etc.). Engage directly with content. Neutral acknowledgments ("Yes, and —" / "That's right, though —") are fine when the conversation calls for them; meta-praise is not.

### Formatting

- **No markdown tables.** WYSIWYG editors (especially VS Code's markdown preview) render tables poorly; cell contents become unreadable. Use bullet lists with key/value patterns (`**Label:** value`), definition-style sub-bullets, or prose. Tables acceptable only when (a) content is genuinely tabular AND (b) the file's primary consumer renders tables correctly (e.g., GitHub README viewed on github.com).
- **No hard line breaks within paragraphs.** No trailing-2-spaces+newline, no backslash+newline, no `<br>` tags, no source-text hard-wrapping. Paragraphs are single long lines that wrap naturally on the reader's screen.
- **Standard markdown** — headers, lists, code fences, blockquotes. Keep it simple.

### Numbering Conventions

- **1-indexed** for human-facing lists, sections, and steps.
- Exceptions: prerequisites (sometimes labeled 0), code (where 0-indexed arrays are standard).

---

## STANDARDS - OPERATIONS

### Brand-Free Folders

If your repo has folders intended for external review (e.g., `business/for-review/`), use concept-based names and content — never brand names, project codenames, or internal identifiers. Brand naming creates the impression you're shopping the work to specific buyers; concept naming keeps options open.

### File Naming

- **Snake_case for system files** (`memory_index.md`, `repo_paths.md`).
- **kebab-case for content** (`my-essay-title.md`, `2026-05-24-event-recap.md`).
- **PascalCase for canonical capitalized docs** (`CWOS.md`, `AICONFIG.md`, `README.md`, `LICENSE`).
- **ISO 8601 dates** in filenames (`YYYY-MM-DD-slug.md`).

### Conversation Files

For repos using the `aiconversations/` extension (long-form AI dialogue threads):

- **Entry label convention:** `### [User | DATE TIME]` and `### [AI Assistant | DATE TIME]` — vendor-agnostic. Don't use `[Claude | ...]` or vendor-specific labels.
- **Timestamp discipline:** always run `date "+%B %-d, %Y %-I:%M%p"` immediately before writing an `[AI Assistant | ...]` entry. Never extrapolate or reuse a session-start timestamp.
- **Run-prompt protocol:** when the user says "run prompt [User | DATE TIME] in FILE.md," follow the procedure in `operations/cwos/skills/run-prompt-protocol/SKILL.md`. The response always gets written back to the conversation file, not just chat. Always add a `[User | +2min]` stub for the next entry.

### Project tracking — distributed `-tasks.md` files with centralized rollup

For repos using the `aiconversations/` extension with companion task tracking, the pattern is:

- **Per-conversation `-tasks.md` files** capture project state close to the conversation that produced it. Each `-tasks.md` file has a "Summary of Open Projects" section at the top listing currently-open projects with status and next action. This is the canonical detail surface.
- **Centralized rollup at `/aiconversations/0-project.md`** is a derived view: a domain → conversation → project listing aggregated from every `-tasks.md` file. Generated by the [`open-projects`](operations/cwos/skills/open-projects/SKILL.md) skill. This is the scanning surface for cross-corpus visibility.
- **Refresh discipline:** invoke the `open-projects` skill on demand or as part of the quarterly maintenance review. The rollup never goes out of sync because it's regenerated from the source files each time.
- **Companion to `0-work-status.md`:** the two surfaces answer different questions. Work-status tells you *which threads need attention*; the project rollup tells you *what specific projects are open*. Both regenerate from distributed sources on the same refresh cadence.

The split (capture in `-tasks.md`, visibility in `0-project.md`) preserves per-conversation isolation as the corpus scales while solving the "I lose track of distributed task files" visibility problem. See the `open-projects` skill for the full procedure and `CWOS.md` for the architectural framing.

#### Tasks File Structure (Standard)

**Four-Section Layout:**

Every `-tasks.md` file uses this four-section layout, in this order.

1. **Summary of Open Projects** — Bullet list at the top showing only active/open projects with one-line digest per project (number, name, status, key dependency or next-action signal). Bullets, not a table (per the no-markdown-tables formatting rule). Remove rows when projects move to Closed / Completed Projects.
2. **Notes/Reference** — Catch-all for context the open work depends on: session-starter prompts, key file pointers, design decisions, glossary entries, links to ADRs / decision logs / sibling conversations, plus any other notes that don't belong inside a project. The "what an operator needs to know to engage" section. Living document; prune as projects close out.
3. **Open Projects** — Detailed project / phase / deliverable / task breakdown for everything currently in flight. One H3 per project.
4. **Closed / Completed Projects** — Historical record. Each entry includes what was delivered, completion date, file references, and any follow-up context worth keeping. Append-only; do not prune. (Section name covers both completed-with-delivery and closed-without-completion.)

**Four-Level Hierarchy:**

Within the Open Projects and Closed / Completed Projects sections, use this hierarchy:

- **Project** (H3, prefixed with "Project N:"): `### Project 1: Config Enhancement`
- **Phase** (H4): `#### Phase 1: Research`
- **Deliverable** (bold text): `**Enhancement spec document**`
- **Task** (checkbox): `- [x] Draft spec outline`

The numbering prefix (`Project N:`) enables stable cross-document references ("see Project 3 in `media-platform-work-tasks.md`") and survives renames better than name-only references.

**Summary List:**

The summary list at the top provides at-a-glance status for all active work. Only include open/active projects — when a project completes, remove it from the summary list and move its detail block to the Closed / Completed Projects section.

**Closed / Completed Projects Section:**

Each entry should include:

- What was delivered (brief description of outputs)
- Completion or closure date
- File references (paths to deliverables, specs, or artifacts produced)
- Reason for closure if not completed-with-delivery (e.g., scope shift, deprioritized, blocked indefinitely)

**Why this section order:** the Summary lets a returning operator (or AI) see live state immediately without scrolling; Notes/Reference supplies the context needed to act on any of those items; Open Projects is the detailed surface; Closed / Completed sits at the bottom because it's reference, not active work. If you wrote the sections in a different order historically, move them at the next meaningful edit — don't force a one-off reshuffle.

This structure scales from single-project tasks files to multi-project tracking while keeping the summary scannable and the detail organized.

### Cross-repo pointer convention (hub→spoke companions — optional)

If this repo is a **hub** in a multi-repo hub-and-spoke setup, conversation files may sometimes need to reference content that lives in a separate spoke repo (typically live code or active output that wasn't migrated to the hub). The Option α convention (adopted as canonical for the workspace-chevan hub on 2026-05-31; portable to any hub) uses a `companionRepo` + `companionPath` field pair in frontmatter:

```yaml
companionRepo: <spoke-short-name>
companionPath: /relative/path/from/spoke/root.tsx
```

- `companionRepo:` is the spoke's short name as registered in `operations/cwos/reference/spoke-registry.md` (if you author one) — it resolves to the spoke's absolute filesystem path
- `companionPath:` is the path **relative to the spoke's repo root** (starts with `/`)
- The two fields appear together; either both or neither

For hub-internal companion references (the dominant case), use `companionTo:` with a hub-internal path. The cross-repo fields are opt-in and only relevant when a conversation actually references live spoke content.

The same `Repo` / `Path` pattern extends to other relationship fields when cross-repo references are needed (`parentRepo:` + `parentPath:`, `relatedRepo:` + `relatedPath:`). Skip this convention entirely if the repo is single-repo, a spoke, or polyrepo without a hub.
- **Size management:** archive conversation files when they exceed 75KB. See `operations/cwos/skills/conversation-archiving/SKILL.md`.

---

## STANDARDS - MAINTENANCE

### Periodic Review

CWOS-aligned repos benefit from periodic review of memory, skills, and reference content:

- **Monthly:** scan `operations/cwos/memory/` for stale or contradicted entries. Update or archive.
- **Quarterly:** review skills for drift against actual workflow. Update or retire skills that no longer reflect what's actually done. Run the [`conversational-maintenance-review`](operations/cwos/skills/conversational-maintenance-review/SKILL.md) skill orchestrator for the full quarterly sweep (validates frontmatter + flags size-threshold files + flags stalled/completed threads + surfaces possible duplicates).
- **As-needed:** add ADRs for cross-cutting decisions, re-entry briefs for projects paused for an extended time.

### Frontmatter validation after restructures

The CWOS architecture treats conversation-file YAML frontmatter as the canonical navigation graph. Structural pointers — `companionTo`, `archives`, `parentConversation`, `subConversations`, `relatedDocuments`, `location`, `companionTasks` — must resolve to existing paths, or be intentionally absent.

**Rule:** after any folder rename, file relocation, or conversation deletion, run the [`frontmatter-validate`](operations/cwos/skills/frontmatter-validate/SKILL.md) skill before committing the restructure. The skill reports dangling references file-by-file; apply the 4-rule maintenance pattern (Rule A: cross-system path migrations; Rule B: root-folder relocations; Rule C: stale archive folders; Rule D: sibling renames + dangling drops) to clean any findings.

The canonical worked example of the 4-rule pattern is the workspace-chevan May 31, 2026 maintenance pass — refer to that as the reference implementation when cleaning new findings; the pattern transfers to any CWOS-aligned repo.

---

## 📋 REFERENCE

### Common Mistakes to Avoid

- **Duplicating canonical content in vendor-specific files.** Keep `CLAUDE.md`, `AGENTS.md`, etc. as thin pointers. New rules go in `AICONFIG.md`; vendor entries reference.
- **Embedding multi-step procedures in `AICONFIG.md`.** Procedures with branching belong in `operations/cwos/skills/<name>/SKILL.md`. AICONFIG.md side keeps a one-line reference.
- **Authoring memories speculatively.** Only write feedback memories, ADRs, or re-entry briefs that capture real decisions, real feedback, or real state. Speculative memories rot.
- **Refreshing in place when restart would be cleaner.** Always-restart pattern; restart cost is cheap because all durable state lives in git-versioned artifacts.

---

## Version History

- {{DATE}} — v1.0.0: Initial CWOS bootstrap. AICONFIG.md authored from chevan-quickstarts template.

(Add subsequent version entries in descending date order.)
