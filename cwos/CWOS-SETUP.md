# CWOS-SETUP.md — Bootstrap a New CWOS-Aligned Repository

**Purpose:** step-by-step procedure for setting up the Conversational Work Operating System (CWOS) architecture in a new repository. Covers four deployment scenarios.

**Prerequisites:** an empty (or near-empty) Git repository, an AI agent that reads markdown (Claude Code, Codex, Cursor, or any CLI/IDE-integrated agent), basic shell access.

**Reference docs:** [`/CWOS.md`](CWOS.md) (the canonical specification) and [`/AICONFIG.md`](AICONFIG.md) (this repo's example operational charter).

---

## Choosing your deployment scenario

CWOS supports several deployment shapes. Pick the one that fits the new repo's role.

### Scenario A — Hub repo (canonical CWOS layout)

The repo is a hub: reasoning, decisions, knowledge, project re-entry briefs, reusable skills. Spoke repos reference back to it. Closest to the full CWOS spec.

**Use when:** the new repo is the primary reasoning hub for an organization or person.

**Layout:**

```
hub/
├── CWOS.md
├── CWOS-SETUP.md
├── AICONFIG.md
├── CLAUDE.md
├── AGENTS.md
├── README.md
├── operations/cwos/
│   ├── memory/
│   ├── skills/
│   └── reference/
└── aiconversations/   (optional reasoning thread layer)
```

### Scenario B — Spoke repo (work happens here; hub lives elsewhere)

Individual project repository where development or production work happens. References a hub for cross-cutting reasoning.

**Use when:** the new repo is a code/output project that contributes to a hub.

**Layout:**

```
spoke/
├── README.md
├── LICENSE
├── CLAUDE.md         # thin pointer to spoke-specific operating notes + hub's AICONFIG.md
├── AGENTS.md         # thin pointer
├── AICONFIG.md       # spoke-specific overrides only; defers to hub for cross-cutting rules
├── .editorconfig
├── .gitignore
├── .tool-versions or mise.toml
├── justfile or Taskfile.yml
├── .env.example
├── .pre-commit-config.yaml
├── .mcp.json         # if MCP servers used
├── .claude/skills/   # spoke-specific skills if any
├── docs/adr/         # spoke-level ADRs
└── ... (source)
```

### Scenario C — Single repo (no separate hub)

One repository contains both reasoning and work. No separate hub. Common for solo work or small teams.

**Use when:** you don't need cross-repo coordination; everything lives in one place.

**Layout:** like Scenario A but with code/output also in this repo. The `operations/cwos/` layer holds reasoning, decisions, and skills; project work happens in other top-level folders.

### Scenario D — Polyrepo coordination

Multiple repos that share CWOS conventions without a single hub. Each repo follows the same operating pattern but operates independently.

**Use when:** multiple repos with similar AI-ops needs but no centralized reasoning. Common for teams where each project is self-contained but conventions are shared.

**Layout:** each repo follows Scenario C or B. CWOS-SETUP.md and CWOS.md may be copied to each, or referenced from a shared location (npm-style or git submodule). Configuration files like .editorconfig, .gitignore patterns, and AICONFIG.md fragments can be shared across repos via templates.

---

## Bootstrap procedure (applies to all scenarios)

The setup is the same shape across scenarios; what differs is which folders you populate vs leave empty.

### Step 1 — Top-level operating files at repo root

Create these files at the repo root. Adapt content from this repo's examples.

**`CWOS.md`** — copy from the source CWOS-aligned repo. This is the canonical spec; refer to it but don't usually edit per-repo. If you need repo-specific deviations, document them in operations/cwos/README.md rather than editing CWOS.md.

**`AICONFIG.md`** — start with a skeleton modeled on this repo's structure:

- `## CANONICAL SOURCE NOTE` referencing CWOS.md and the three-layer split (configuration / state / procedure)
- `## CORE INSTRUCTIONS` with repo-appropriate "what to do / what NOT to do" guidance
- `## PROJECT CONTEXT` describing the new repo's specifics
- `## REPOSITORY CONTEXT` describing the new repo's structure
- `## STANDARDS - WRITING` (if the new repo produces writing)
- `## STANDARDS - OPERATIONS` (file conventions, git workflow, etc.)
- Version: 1.0.0
- Last Updated: today

**`CLAUDE.md`** — thin pointer:

```markdown
**Read and apply /AICONFIG.md for all behavioral standards, conventions, and project context.**

# Claude Code Configuration — <repo-name>

Repository: <repo-name>
Version: 1.0.0
Last Updated: <date>
```

**`AGENTS.md`** — thin pointer (use this repo's AGENTS.md as a template).

**`CWOS-SETUP.md`** — this file. Copy as-is if you want it as a reference in the new repo. Optional.

**`README.md`** — repo-level overview. Include an "AI Context & Configuration" section pointing at AICONFIG.md, CLAUDE.md, AGENTS.md, CWOS.md.

### Step 2 — Implementation layer at `operations/cwos/`

Create the three-folder structure for CWOS implementation.

```
operations/cwos/
├── README.md                          # describes the implementation layer
├── memory/                            # accumulated state
│   ├── MEMORY.md                      # memory index
│   ├── README.md                      # navigation
│   ├── always.md                      # optional always-loaded context
│   ├── decisions/                     # ADRs
│   │   └── _template.md
│   ├── projects/                      # re-entry briefs per project/spoke
│   │   └── _template.md
│   └── archive/                       # completed or paused content
├── skills/                            # reusable procedures (Agent Skills standard)
│   ├── README.md                      # skills index
│   └── _template/
│       └── SKILL.md                   # Agent Skills format template
└── reference/                         # standing knowledge
    ├── README.md
    ├── taxonomy.md                    # CWOS vocabulary
    ├── mcp-stack.md                   # MCP server configuration reference
    ├── agent-skills-standard.md       # Agent Skills format reference
    └── conventional-commits.md        # Conventional Commits convention
```

Copy templates from the source repo. Most files are stubs that get content as the repo accumulates decisions, skills, and reference material.

### Step 3 — Foundation files (strongly recommended for all scenarios)

These aren't CWOS-specific but pair well with the operating system. Add what's relevant.

- `.editorconfig` — universal editor config; prevents formatting drift
- `.gitignore` — at minimum, ignore `.claude/` runtime state (with `!.claude/settings.json` if tracking project Claude Code settings), `__pycache__/`, `.env*`, `node_modules/` if applicable
- `.gitattributes` — line endings, diff behavior
- `LICENSE` — if applicable

**For development repos** (Scenario B or working repos):

- `.tool-versions` (asdf) or `mise.toml` (mise) — pin runtime versions
- `justfile` or `Taskfile.yml` — document project commands
- `.env.example` — declare expected environment variables
- `.pre-commit-config.yaml` — pre-commit hooks for linters/formatters
- `.mcp.json` — MCP server configuration (if using MCP)

### Step 4 — Optional: conversation thread layer

If the new repo uses long-form dialogue with AI (run-prompt protocol, archived conversations, tasks files):

```
aiconversations/
├── README.md
├── 0-work-status.md                   # work-state dashboard
├── _system/                           # infrastructure conversations
├── <domain>/                          # per-domain conversation files
└── archives/                          # archived chunks
```

This is a CWOS extension (covered in CWOS.md's "aiconversations" section). Skip if the new repo doesn't have long-form reasoning threads.

### Step 5 — Verify the setup

Open an AI session in the new repo and run a cold-start verification:

> Read `/AICONFIG.md` for behavioral rules and `/README.md` for repo orientation. Then read `/CWOS.md` to understand the operating system. Summarize the three-layer split (configuration / state / procedure) and confirm what artifacts exist in operations/cwos/.

If the AI's summary reflects the architecture correctly, setup is complete. If not, fix gaps and re-verify.

---

## What to copy from the source repo

When bootstrapping from an existing CWOS-aligned repo, these files are reusable as starting templates:

**Directly copyable (minor edits at most):**

- `CWOS.md` — the spec
- `CWOS-SETUP.md` — this file
- `CLAUDE.md` — version + repo name update
- `AGENTS.md` — minor adjustments
- `operations/cwos/memory/decisions/_template.md` (ADR template)
- `operations/cwos/memory/projects/_template.md` (re-entry brief template)
- `operations/cwos/skills/_template/SKILL.md` (Agent Skills format template)
- `operations/cwos/reference/taxonomy.md`, `agent-skills-standard.md`, `conventional-commits.md`, `mcp-stack.md`

**Adapt heavily for new repo's context:**

- `AICONFIG.md` — keep structure, replace project-specific content
- `README.md` — write fresh; copy the "AI Context & Configuration" section pattern
- `operations/cwos/README.md` — minimal repo-specific content

**Skip unless relevant:**

- Skills authored in the source repo (e.g., conversation-archiving/, work-status-dashboard/) — most are project-specific; author new ones for the new repo when patterns emerge

---

## Adapting CWOS to non-hub layouts

The CWOS spec describes hub-and-spoke as the canonical model but explicitly supports variants. For Scenarios B, C, and D:

- **In a spoke (B):** the hub's `operations/cwos/` lives elsewhere; this repo has minimal CWOS infrastructure. AICONFIG.md may defer to the hub via reference rather than duplicating content. ADRs at `docs/adr/` are spoke-level; cross-cutting decisions go to the hub's `memory/decisions/`.
- **In a single repo (C):** treat the repo as both hub and spoke. Full CWOS layout, plus the project work in domain-specific folders alongside `operations/cwos/`.
- **In polyrepo (D):** each repo follows B or C. Shared conventions go in a documents-only repo or a copied set of bootstrap files. CWOS.md can be a git submodule if you want literal version pinning across repos.

---

## Migrating from `operations/conversational-work/` (or another predecessor)

If the target repo already has the older `operations/conversational-work/` infrastructure installed — `ai-work-patterns/`, `ai-configs/`, `AI-OPS-ARCHITECTURE.md`, `SETUP-NEW-AI-OPS-REPO.md`, `claude-configs/` discovery mirror, etc. — you're not bootstrapping fresh; you're migrating. The clean-break approach (recommended; see chevan-content ADR-001 for full reasoning) builds `operations/cwos/` new and marks the old folder LEGACY rather than renaming in place.

**Companion skill:** [`/operations/cwos/skills/cwos-migrate-from-conversational-work/SKILL.md`](operations/cwos/skills/cwos-migrate-from-conversational-work/SKILL.md) codifies this procedure as a runnable skill. The section below is the read-it-yourself version.

### Pre-flight check

Run before starting:

```bash
ls operations/conversational-work/              # what's there
cat CLAUDE.md | grep -i version                 # current architecture version
ls ~/.claude/projects/$(pwd | sed 's|/|-|g')/memory/ 2>/dev/null  # Claude memory if any
ls operations/claude-configs/ 2>/dev/null       # discovery mirror if any
ls aiconversations/0-work-priority.md 2>/dev/null  # old dashboard name

# Standards consolidation state
ls operations/conversational-work/ai-configs/github/prompts/ 2>/dev/null
grep -E "^## STANDARDS - (WRITING|OPERATIONS|MAINTENANCE)" AICONFIG.md 2>/dev/null
```

This tells you what to migrate, what to delete, what to rename, and whether the predecessor templates need consolidating before the LEGACY notice goes on. See "Standards consolidation" below.

### Three-session migration structure

Reference implementation: chevan-content Sessions A/B/C (commits `bb255ba` through `1946695`, May 2026). Each session is one focused pass; together they convert the repo.

**Session A — Foundation (~30-45 min):**

1. Author `/CWOS.md` at repo root. Easiest: copy from a CWOS-aligned source repo.
2. Author `/CWOS-SETUP.md` at repo root. Same approach.
3. Author `/AGENTS.md` at repo root as a thin pointer to AICONFIG.md.
4. Create `operations/cwos/{memory,skills,reference}/` with a README in each.
5. Commit: `feat(cwos): scaffold CWOS architecture at repo root and operations/cwos/`.

**Session B — Skills + reference content (~60-90 min):**

1. Port skills selectively from the source repo. Universally useful: `run-prompt-protocol`. Conditionally useful: `conversation-archiving`, `work-status-dashboard` (only if the repo uses `aiconversations/`).
2. Author target-repo-specific skills as needed.
3. Port reference docs: `taxonomy.md`, `conventional-commits.md`, `agent-skills-standard.md`, `mcp-stack.md` (if MCP used).
4. Commits: `feat(cwos): author N skills in Agent Skills standard format`, `docs(cwos): port taxonomy and reference standards`.

**Session C — Migration + cleanup (~60-90 min):**

1. **Memory migration** (if Claude memory exists):
   - Copy `~/.claude/projects/<slug>/memory/*.md` to `operations/cwos/memory/`
   - Remove the original directory: `rm -rf ~/.claude/projects/<slug>/memory`
   - Symlink: `ln -s <repo-abs-path>/operations/cwos/memory ~/.claude/projects/<slug>/memory`
   - Merge any existing memory index file with the new `operations/cwos/memory/MEMORY.md`
2. **Skills discoverability for Claude Code:**
   - `ln -s ../operations/cwos/skills .claude/skills`
   - `.gitignore`: add `!.claude/skills` exception under the `.claude/*` block
3. **Forward-reference updates** (the auto-loaded canonical files):
   - `AICONFIG.md` — replace references to `operations/conversational-work/` paths with `operations/cwos/` equivalents. Add `## CANONICAL SOURCE NOTE` referencing CWOS docs. Mark Configuration Management Operations section as legacy if present.
   - `CLAUDE.md` — version bump (typically MINOR — architecture-level change), add changelog entry, update Last Updated date.
   - `README.md` (if exists) — refresh AI Context & Configuration section, Session-start prompts, Key documentation references.
   - Any maintenance docs (e.g., `aiconversations/_system/operations/repository-maintenance.md`) — refresh forward references.
4. **Rename `0-work-priority.md` → `0-work-status.md`** (if the repo has the dashboard pattern). Update internal references in renamed files + all forward references in canonical files.
5. **Author ADR-001 for the target repo** at `operations/cwos/memory/decisions/001-cwos-migration.md`. Capture: what was present, what was ported, what was rewritten, what was deleted, what was marked legacy. Each repo gets its own ADR-001 because each migration has its own context.
6. **Legacy deletions** (the cleanly redundant files):
   - Delete `operations/conversational-work/AI-OPS-ARCHITECTURE.md` if it exists (superseded by `/CWOS.md`)
   - Delete `operations/claude-configs/` entirely if it exists as a discovery mirror (vestigial after memory migrates)
   - Remove `operations/claude-configs/*` rules from `.gitignore`
7. **LEGACY notice on `operations/conversational-work/README.md`** — prepend a quote block listing where each old artifact moved, why the folder still exists, and pointing at ADR-001.
8. **Commit in logical groups** using Conventional Commits format (`feat(cwos):`, `refactor(cwos):`, `docs(cwos):`, `chore(config):`).

### Verify

Cold-start prompt in the migrated repo:

> Read `/AICONFIG.md` for behavioral rules and `/README.md` for repo orientation. CLAUDE.md auto-loads. Then summarize the CWOS three-layer split and the always-restart pattern so I know you've absorbed them.

If the summary reflects the new architecture, the migration is complete. If not, check whether the forward-reference updates landed in all auto-loaded files.

### Cleanup horizon

After 2-3 months of CWOS operation in the migrated repo, evaluate whether `operations/conversational-work/` can be deleted entirely or kept indefinitely. The LEGACY notice doesn't enforce a deletion timeline — re-evaluate based on whether the folder still has reference value.

### Standards consolidation (if predecessor templates have unconsolidated content)

Older `operations/conversational-work/` deployments used an install-pipeline pattern where standards templates (writing-style.md, conversation-files.md, document-layout.md, essay-workflow.md, content-categories.md, tool-selection.md, working-files.md, core-workflow.md, collaboration-workflows.md, document-size.md, file-operations.md, operator-workflow-guidance.md, portable-project-memory.md, readme-best-practices.md, organization-strategy.md, reorganization.md, config-management.md) lived in `ai-configs/github/prompts/` and got "installed" into AICONFIG.md on demand. CWOS treats AICONFIG.md as the canonical, always-loaded source for standards (writing, operations, maintenance), so any template content that hasn't been consolidated into AICONFIG.md will be functionally orphaned when `operations/conversational-work/` is marked LEGACY.

The pre-flight check identifies three possible states:

- **State A (consolidated):** templates exist AND AICONFIG.md STANDARDS sections are populated → no consolidation work needed; the migration proceeds normally
- **State B (unconsolidated):** templates exist BUT AICONFIG.md STANDARDS sections are missing or sparse → standards content lives only in templates; must consolidate before marking conversational-work LEGACY
- **State C (no templates):** templates don't exist → no consolidation question; proceed normally

For State B, do consolidation as part of Session B (alongside skills porting), before Session C marks the predecessor LEGACY. For each `ai-configs/github/prompts/<template>.md`:

1. Read the template file
2. Identify which AICONFIG.md section it belongs in:
   - Writing-related templates (writing-style, content-categories, essay-workflow, document-layout, document-size) → `## STANDARDS - WRITING`
   - Operations-related templates (file-operations, working-files, readme-best-practices, organization-strategy) → `## STANDARDS - OPERATIONS`
   - Maintenance-related templates (reorganization, config-management) → `## STANDARDS - MAINTENANCE`
3. Lift the template's behavioral content (rules, conventions, examples) into the corresponding AICONFIG.md section as a new subsection
4. Skip install/uninstall instructions, version numbers, dependency metadata, and other install-pipeline-specific framing — they don't carry over
5. If AICONFIG.md doesn't yet have the target STANDARDS section, create it
6. Commit as `chore(aiconfig): consolidate <template> from predecessor templates` (one commit per template, or one commit per STANDARDS section, whichever reads cleaner)

The canonical worked example of this consolidation is the chevan-content v5.8.x series — see the changelog entries `v5.8.5` through `v5.8.8` in `chevan-content/CLAUDE.md` for what was lifted from each template.

After consolidation completes, Session C's LEGACY notice on `operations/conversational-work/README.md` is safe to apply — the standards content lives in AICONFIG.md as the canonical home, and the templates become historical artifacts.

---

## What's intentionally NOT in this guide

- **Bootstrap script.** Not built yet. If you find yourself running through this guide repeatedly, a `scaffold-cwos-repo.sh` script is worth building. Until then, the manual procedure is reproducible enough.
- **Snapshot/clone mechanism.** Capturing the source repo's current state for cloning elsewhere is more complex than this guide warrants. Build only if needed.
- **Per-vendor MCP setup details.** Reference doc at `operations/cwos/reference/mcp-stack.md` describes the structure; actual configuration depends on which MCP servers you use.

---

## Common adaptations

- **Pure code repo (Scenario B):** skip the `aiconversations/` layer; conversation files aren't useful for short-cycle code work. AICONFIG.md focuses on coding conventions, not writing conventions.
- **Content-production repo (Scenario C):** lean on `aiconversations/` for essay reasoning, drafts, and per-piece dialogue.
- **Client engagement repo (Scenario B):** include `operations/cwos/memory/enterprise/<client>/conventions.md` to capture client-specific patterns separately from your usual workflow.
- **Multi-vendor from day one:** create `operations/{vendor}-configs/` folders for each vendor you'll use (Claude, Codex, Cursor, etc.) — see CWOS.md for the discovery-mirror pattern (though this is optional and currently deprecated in the source repo).

---

## Refreshing an existing session

CWOS files (AICONFIG.md, CLAUDE.md, AGENTS.md, CWOS.md, skills/, memory/) are loaded into an AI session at session start. Edits to those files after the session begins don't propagate automatically — the AI keeps using the in-memory copy it loaded at the beginning. Same applies after Claude Code compacts the conversation: auto-loaded files remain, but the conversational reasoning around them has been compressed and may have drifted.

This section codifies the four refresh scenarios and the prompts that handle them. Vendor-agnostic; same pattern applies to Claude / Codex / Cursor / Copilot when their configs change mid-session.

### When to refresh vs. when to restart

**This repo's preference is always-restart.** When context needs refreshing for any non-trivial reason, restart the session rather than refresh in place. Reasoning: all durable state (conversation files, memory, AICONFIG.md, skills) lives in git-versioned artifacts, so restart is cheap. A fresh session that picks up the new canonical files from cold start produces cleaner reasoning than a mid-session refresh that's working around stale accumulated context.

The refresh prompts below are still useful for cases where restart isn't worth it — small config tweaks, quick recalibration after one drift incident, post-compaction recovery when you want to continue immediate work. For architecture-level changes (version bumps, skill additions, AICONFIG.md restructuring), restart.

### Scenario 1: Cold start (new session, fresh context)

The AI just opened in this repo. Auto-loaded files (CLAUDE.md, memory) were pulled in silently. You want to verify the AI actually absorbed the canonical docs and isn't running on training-data priors.

**Prompt:**

> Read `/AICONFIG.md` for behavioral rules and `/README.md` for repo orientation. CLAUDE.md auto-loads. Then summarize the CWOS three-layer split (configuration / state / procedure) and the always-restart pattern so I know you've absorbed them.

**Why the verification step matters:** auto-loaded ≠ understood. Asking the AI to summarize specific concepts catches sessions where the model didn't actually read or didn't actually parse the canonical docs. If the summary comes back wrong or generic, the session is worth restarting.

### Scenario 2: Post-compaction refresh

Claude Code (or another AI tool with conversation compaction) compressed the conversation when context filled. Auto-loaded files remain in memory; conversation history is now a summary. The AI has the canonical rules but may have lost the in-flight work-state context.

**Prompt:**

> Compaction just happened. Re-read `/AICONFIG.md`, `/README.md`, and `aiconversations/0-work-status.md` (or your repo's work-state dashboard equivalent) to re-orient. Memory (auto-loaded from operations/cwos/memory/) and CLAUDE.md are still loaded. Confirm you understand current work state.

**Why this works:** the dashboard recovers the "what's active right now" context that compaction discards. The other two re-reads recover behavioral rules and repo orientation.

### Scenario 3: Post-update refresh (you edited config mid-session)

You changed AICONFIG.md, CLAUDE.md, a skill, or another canonical doc during the session. The AI's in-memory copy is now stale.

**Single-file prompt:**

> I just updated `/AICONFIG.md`. Re-read it and tell me what changed in the rules you should now apply.

**Multi-file version bump:**

> I just bumped to v5.X.X. Re-read `/AICONFIG.md` and `/CLAUDE.md`'s recent changelog. Summarize the new behavioral rules or framework changes you should apply going forward.

**Why the "tell me what changed" phrasing:** forces the AI to actively diff its in-memory version against the on-disk version. A plain "re-read" prompt sometimes doesn't dislodge the cached version because the model treats the file as already-known.

### Scenario 4: Mid-session drift (model is producing rule-violating output)

The AI is writing em-dashes you've told it not to use, opening responses with meta-evaluation praise, generating markdown tables in conversation files, etc. The rule is in AICONFIG.md but the model isn't applying it.

**Prompt:**

> Re-read `/AICONFIG.md` `## STANDARDS - WRITING` section. You used X pattern in your last response which violates Y.

**Why specific recalibration beats full re-read:** citing the rule and the violation surgically corrects the drift without burning context on a full reload. Reserve the full re-read for cases where multiple rules are slipping at once.

### Authoring your own refresh prompts

The pattern across all four scenarios:

1. **State what changed** (compaction happened, you updated a file, you noticed drift)
2. **Specify the files to re-read** (always AICONFIG.md; add others as relevant)
3. **Ask for an active verification** (summary, diff, confirmation) rather than a passive acknowledgment

The verification step is the most important part. "Re-read X" by itself is too easy for the AI to comply with shallowly. "Re-read X and summarize Y" or "Re-read X and tell me what changed" forces the model to actually engage with the contents.

### Cross-vendor notes

Same prompts work for Codex, Cursor, Copilot, and other tools that read AGENTS.md or AICONFIG.md. The differences:

- **Claude Code** auto-loads CLAUDE.md and project memory; the "auto-loaded" comment in prompts is accurate
- **Codex / Cursor** typically auto-load AGENTS.md or the equivalent vendor entry point; substitute that filename when adapting prompts
- **Aider, Continue, others** read whichever entry point they're configured for; same pattern — verify what they auto-load and adjust the prompt

For deeper cross-vendor session patterns, see [`/CWOS.md`](CWOS.md) → "The session cycle."

---

## Maintaining CWOS-aligned repos over time

Once set up, the operating cycle:

- **Capture decisions as ADRs** in `operations/cwos/memory/decisions/` when you face cross-cutting choices.
- **Update re-entry briefs** in `operations/cwos/memory/projects/<spoke>/` before pausing work on a project.
- **Author skills** in `operations/cwos/skills/<name>/SKILL.md` when you find yourself describing the same multi-step procedure repeatedly.
- **Update AICONFIG.md** when behavioral rules evolve. Bump version.
- **Update CWOS.md** when cross-repo patterns emerge worth standardizing.
- **Periodic review** (quarterly recommended): audit memory/, prune archive/ candidates, surface patterns worth promoting to skills or AICONFIG.md.

---

## When this guide changes

CWOS-SETUP.md should evolve as you spin up more repos and learn what's actually friction. If you find a step that consistently needs adaptation, document the adaptation here. If a step is rarely useful, mark it optional or remove it.

This guide is a snapshot of current bootstrap practice; not a permanent contract.
