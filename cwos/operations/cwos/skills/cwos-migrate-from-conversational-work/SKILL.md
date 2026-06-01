---
name: cwos-migrate-from-conversational-work
description: Migrate a repository from the older operations/conversational-work/ infrastructure to the CWOS architecture (operations/cwos/ + canonical files at repo root). Clean-break approach per chevan-content ADR-001. Use when a repo has the predecessor ai-work-patterns, ai-configs, AI-OPS-ARCHITECTURE.md, claude-configs/, etc. installed and the user wants to switch it to CWOS.
---

# Migrate from `operations/conversational-work/` to CWOS

This skill codifies the three-session migration that takes a repo from the older `operations/conversational-work/` infrastructure to the CWOS (Conversational Work Operating System) architecture. Companion read-it-yourself doc: `/CWOS-SETUP.md` "Migrating from `operations/conversational-work/`" section.

The migration uses the **clean-break approach** per chevan-content ADR-001: build `operations/cwos/` fresh; mark the old folder LEGACY rather than rename in place. This produces a coherent new architecture from day one without hybrid files.

## When to invoke

- User asks to migrate a repo to CWOS and the repo has `operations/conversational-work/` installed
- User says "convert this repo to CWOS" or "switch this repo from conversational-work to cwos"
- A pre-flight check (below) confirms predecessor infrastructure is present

If the repo has NO predecessor infrastructure, use `/CWOS-SETUP.md` fresh bootstrap procedure instead.

## Inputs

- Target repo's current root directory (`pwd` confirms)
- Path to a CWOS-aligned source repo to copy from (e.g., chevan-content) for canonical files
- Permission from the user to author commits in the target repo

## Pre-flight check

Run these commands in the target repo and report findings to the user before starting:

```bash
ls operations/conversational-work/ 2>&1
cat CLAUDE.md 2>/dev/null | grep -i "^Version:" | head -1
ls "$HOME/.claude/projects/$(pwd | sed 's|/|-|g')/memory/" 2>/dev/null
ls operations/claude-configs/ 2>/dev/null
ls aiconversations/0-work-priority.md 2>/dev/null
git status --short
```

Surface to the user:
- What predecessor infrastructure is present
- Current CLAUDE.md version (informs the version bump)
- Whether Claude memory exists at the per-project path (informs whether to migrate memory + symlink)
- Whether `operations/claude-configs/` exists (informs whether to delete it)
- Whether the old dashboard name `0-work-priority.md` is present (informs whether to rename)
- Whether working tree is clean (must be clean before starting; uncommitted work goes in a stash or separate commits first)

## Procedure

The migration is structured as three sessions. Each session ends with focused commits in Conventional Commits format.

### Session A — Foundation (~30-45 min)

**Goal:** Author the four root-level CWOS foundation files and scaffold `operations/cwos/`.

**Steps:**

1. Author `/CWOS.md` at the target repo root. Easiest: `cp <source-repo>/CWOS.md ./CWOS.md`. If target repo wants its own variant, edit after copying.
2. Author `/CWOS-SETUP.md` at the target repo root. Same approach.
3. Author `/AGENTS.md` at the target repo root as a thin pointer to AICONFIG.md. Use source repo's AGENTS.md as template.
4. Create `operations/cwos/{memory,skills,reference}/` directory tree. Author a `README.md` in each describing its role.
5. Author top-level `operations/cwos/README.md` describing the three subfolders.

**Commit:** `feat(cwos): scaffold CWOS architecture at repo root and operations/cwos/`

**Verify after Session A:**

```bash
ls CWOS.md CWOS-SETUP.md AGENTS.md operations/cwos/{memory,skills,reference}/README.md
```

All six files should exist.

### Session B — Skills + reference content (~60-90 min)

**Goal:** Port the right skills and reference docs. Don't bulk-port — pick what's universally useful and what matches the target repo's workflow.

**Steps:**

1. **Universally useful skills (port for every repo):**
   - `run-prompt-protocol` — copy `<source>/operations/cwos/skills/run-prompt-protocol/SKILL.md`

2. **Conditionally useful skills (port if the repo uses the relevant pattern):**
   - `conversation-archiving` — port if the repo uses `aiconversations/` and has files that grow over 75KB
   - `work-status-dashboard` — port if the repo uses `aiconversations/0-work-status.md` (or has the dashboard pattern under another name)

3. **Skip media-platform-specific skills** like `essay-publish` unless the target repo produces media-platform content.

4. **Author target-repo-specific skills.** If the target repo has multi-step procedures that don't exist as skills yet (e.g., a `slcdems-meeting-notes` skill for political-activism repos, a `caucus-checkin-deploy` skill for code repos), author them in Agent Skills format. Use `<source>/operations/cwos/skills/_template/SKILL.md` as a starting point.

5. **Port reference docs (universal):**
   - `taxonomy.md` — copy with light tweaks for the target repo's terminology
   - `conventional-commits.md` — copy as-is
   - `agent-skills-standard.md` — copy as-is

6. **Port `mcp-stack.md` if the target repo uses MCP servers.**

**Commits (in two logical groups):**

- `feat(cwos): author N skills in Agent Skills standard format`
- `docs(cwos): port taxonomy and reference standards from <source-repo>`

**Verify after Session B:**

```bash
ls operations/cwos/skills/*/SKILL.md
ls operations/cwos/reference/
```

Skills and reference docs should be in place.

### Session C — Migration + cleanup (~60-90 min)

**Goal:** Wire the new architecture into the auto-loaded files, migrate Claude memory, delete redundant legacy files, mark `operations/conversational-work/` as LEGACY. This session has the heaviest "touching existing files" load.

**Steps:**

1. **Memory migration (if Claude memory exists at the per-project path):**

   ```bash
   SLUG=$(pwd | sed 's|/|-|g')
   CLAUDE_MEM="$HOME/.claude/projects/$SLUG/memory"
   if [ -d "$CLAUDE_MEM" ] && [ ! -L "$CLAUDE_MEM" ]; then
     cp -a "$CLAUDE_MEM"/*.md operations/cwos/memory/
     rm -rf "$CLAUDE_MEM"
     ln -s "$(pwd)/operations/cwos/memory" "$CLAUDE_MEM"
   fi
   ```

   Then update `operations/cwos/memory/MEMORY.md` to merge with any existing memory index file's content.

2. **Skills discoverability for Claude Code:**

   ```bash
   ln -s ../operations/cwos/skills .claude/skills
   ```

   Update `.gitignore`: under the `.claude/*` block, add `!.claude/skills` exception.

3. **Forward-reference updates** in the auto-loaded canonical files:

   - **`AICONFIG.md`:**
     - Replace any references to `operations/conversational-work/` paths with `operations/cwos/` equivalents
     - Add or rewrite `## CANONICAL SOURCE NOTE` to reference `/CWOS.md`, `/CWOS-SETUP.md`, `/operations/cwos/README.md`, `/operations/cwos/reference/taxonomy.md`
     - Add "always-restart pattern" paragraph if not present
     - Rename "three-bucket framework" vocabulary to "three-layer split (configuration / state / procedure)"
     - Mark Configuration Management Operations section as legacy if present

   - **`CLAUDE.md`:**
     - Version bump (typically MINOR — architecture-level change)
     - Add changelog entry covering the migration
     - Update Last Updated date

   - **`README.md`** (if exists):
     - Refresh AI Context & Configuration section
     - Refresh Session-start prompts (cold-start, post-compaction, post-update, mid-session-drift)
     - Refresh Key documentation references

   - **Maintenance docs** like `aiconversations/_system/operations/repository-maintenance.md`:
     - Refresh forward references to old paths

4. **Rename `0-work-priority.md` → `0-work-status.md` (if present):**

   ```bash
   git mv aiconversations/0-work-priority.md aiconversations/0-work-status.md
   git mv aiconversations/0-work-priority-conversation.md aiconversations/0-work-status-conversation.md 2>/dev/null || true
   ```

   Update internal references (frontmatter `location:`, header, `instrumentedPatterns` → `instrumentedSkills` block if present, "Installed Configurations" section → CWOS canonical pointer). Update all forward references in `AICONFIG.md`, `README.md`, `operations/cwos/skills/work-status-dashboard/SKILL.md`, `operations/cwos/reference/taxonomy.md`. Leave historical references in archives and past conversation entries untouched (immutable record).

5. **Author ADR-001 for the target repo** at `operations/cwos/memory/decisions/001-cwos-migration.md`:
   - Status: Accepted
   - Context: what predecessor infrastructure was present
   - Options: in-place migration / side-by-side / clean break
   - Decision: clean break (typically), with reasoning
   - Consequences: what changed, what becomes easier, what becomes harder
   - References: link to chevan-content's ADR-001 for the canonical reasoning

6. **Legacy deletions:**

   ```bash
   git rm operations/conversational-work/AI-OPS-ARCHITECTURE.md 2>/dev/null || true
   git rm -rf operations/claude-configs/ 2>/dev/null || true
   rm -rf operations/claude-configs/ 2>/dev/null || true  # cleans up any dangling symlinks
   ```

   Remove `operations/claude-configs/*` rules from `.gitignore` if present.

7. **LEGACY notice on `operations/conversational-work/README.md`:**

   Prepend a quote block at the top listing where each old artifact moved, why the folder still exists, and pointing at the target repo's ADR-001. See chevan-content's `operations/conversational-work/README.md` for the canonical pattern.

8. **Commit in logical groups (typically 5-7 commits):**

   - `feat(cwos): migrate Claude memory to operations/cwos/memory/ and wire skills discovery`
   - `docs(cwos): author ADR-001 for clean-break CWOS migration decision`
   - `refactor(aiconversations): rename 0-work-priority -> 0-work-status across forward references` (if applicable)
   - `docs(cwos): point canonical config files at operations/cwos/`
   - `refactor(cwos): remove legacy AI-OPS-ARCHITECTURE.md and operations/claude-configs/`
   - `docs(conversational-work): mark folder as LEGACY, superseded by operations/cwos/`
   - `chore(config): bump CLAUDE.md to vX.Y.Z with CWOS migration changelog entry`

## Outputs

After the migration completes, the target repo has:

- Four foundation files at repo root: `CWOS.md`, `CWOS-SETUP.md`, `AGENTS.md`, `AICONFIG.md` (updated)
- `operations/cwos/{memory,skills,reference}/` populated with ported and target-specific content
- Claude memory at `operations/cwos/memory/` with symlink at the per-project Claude path for auto-load
- `.claude/skills` symlink for Claude Code Agent Skills discovery
- ADR-001 capturing the migration decision
- `operations/conversational-work/` marked LEGACY with a quote-block notice and most-redundant files deleted
- CLAUDE.md version-bumped with a changelog entry covering the migration

## Verification

Cold-start prompt in the migrated repo:

> Read `/AICONFIG.md` for behavioral rules and `/README.md` for repo orientation. CLAUDE.md auto-loads. Then summarize the CWOS three-layer split and the always-restart pattern so I know you've absorbed them.

If the summary reflects the new architecture, the migration is complete. If the summary still references "conversational-work" as canonical or uses old vocabulary ("patterns" instead of "skills", "three-bucket framework"), the forward-reference updates in step 3 missed something — check AICONFIG.md, README.md, CLAUDE.md.

Also verify:

```bash
readlink .claude/skills              # should be ../operations/cwos/skills
ls operations/cwos/memory/feedback_*.md  # memory files present if Claude memory was migrated
git log --oneline -15                # commits land in logical groups
```

## Don't do

- **Don't rename `operations/conversational-work/` to `operations/cwos/`.** Clean break, not rename. The hybrid files that result from renaming would carry old vocabulary into the new structure.
- **Don't port the old pattern files as-is.** Author skills fresh using Agent Skills format. The pattern files' vocabulary and structure don't translate cleanly.
- **Don't delete `operations/conversational-work/` entirely** as part of the migration. Mark LEGACY. Re-evaluate deletion 2-3 months after migration.
- **Don't push at the end of each session** unless the user explicitly requests it. Standing rule: state-changing git ops require per-task authorization.
- **Don't bypass hooks (`--no-verify`)** if a pre-commit hook fails during commits. Investigate and fix.
- **Don't author target-repo skills speculatively.** Only author skills for workflows that actually exist in the target repo.

## Common adaptations

- **Target repo has no `aiconversations/`:** skip the dashboard rename in step 4 of Session C; skip `conversation-archiving` and `work-status-dashboard` skills in Session B.
- **Target repo has no Claude memory:** skip step 1 of Session C (memory migration); the symlink isn't needed.
- **Target repo is a spoke (Scenario B):** the new `operations/cwos/` is minimal; AICONFIG.md defers to hub for cross-cutting rules; cross-cutting ADRs go to the hub's `memory/decisions/`.
- **Target repo uses MCP servers:** port `mcp-stack.md` reference in Session B.

## References

- `/CWOS-SETUP.md` "Migrating from `operations/conversational-work/`" section — read-it-yourself version of this skill
- `/CWOS.md` — canonical CWOS specification
- chevan-content's `/operations/cwos/memory/decisions/001-cwos-architecture.md` — reference ADR for clean-break rationale
- chevan-content's `aiconversations/_system/operations/conversational-work-operations-conversation.md` — reference implementation conversation (Sessions A/B/C entries from May 18, 2026)
- chevan-content commits `bb255ba` through `1946695` — reference implementation diffs

---

[End of Skill]
