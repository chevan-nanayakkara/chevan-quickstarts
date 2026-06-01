---
title: Conversational Work Operating System
short_title: CWOS
description: A reference for AI-augmented work using a hub-and-spoke git-and-markdown architecture.
tags:


* cwos
* ai-augmented-work
* hub-and-spoke
* knowledge-architecture
* ai-operations
* conversational-work
  version: 1.0
  status: living-document
  created: 2026-05-18
  updated: 2026-05-18
  maintainer: solo
  audience:
* human-reference
* ai-operating-guide
  precedence:
* aiconfig.md
* this-document
* general-defaults
  canonical_location: hub/reference/cwos.md
  structure:
  divisions:
  * architecture
  * operations
  * collaboration
  * reference
    ai_directives:
    apply_sections_marked: '## [AI:OPERATE]'
    apply_inline_marked: '> AI:'
    modification_policy: propose-not-apply
    approval_required_for:
  * file-modifications
  * tool-installations
  * commits
  * deletions
    engagement_style: direct-minimal-preamble
    review_cadence:
    quarterly: project-and-skill-review
    biannual: memory-pruning
    related_concepts:
* agent-skills-standard
* model-context-protocol
* spec-driven-development
* architecture-decision-records
  sovereignty_model: git-and-markdown
  vendor_neutrality: required
---
# Conversational Work Operating System

*A reference for AI-augmented work using a hub-and-spoke git-and-markdown architecture.*

## What this document is

This document defines a Conversational Work Operating System: the infrastructure, operations, and collaboration patterns that make work happen through conversation between a human and AI tools (and, where relevant, between current-self and future-self via durable artifacts).

The system rests on a core observation: most of my work happens through conversation. Conversations with AI agents about code, content, decisions. Conversations with future-me through notes and re-entry briefs. Conversations with past-me through the decisions log. The artifacts in this system (ADRs, skills, re-entry briefs, memory notes, configurations) are conversation residue, captured in markdown and versioned in git so they survive tool churn and time.

The system is currently AI-augmented because AI is the dominant conversational partner. The architecture is designed to outlast that specific dominance: the artifacts are vendor-neutral, the storage is git, the format is markdown. New tools plug in without the system reorganizing around them.

## Document organization

This document has four divisions:

1. **Architecture** — what the system is. Knowledge structure (aiconfig/memory/skills), the hub-and-spoke model, foundation files. Read this first.
2. **Operations** — how the system gets maintained. Decision capture, session practices, periodic review. The cycle that keeps the system useful over time.
3. **Collaboration** — how work happens inside the system. The working contract between human and AI, what AI does autonomously, what requires approval. This division contains all `## [AI:OPERATE]` guidance.
4. **Reference** — what's available to put in the system. Tool catalogs (MCP servers, vendor-specific sections, hub knowledge management tools), alternatives, configuration files inventory.

## How to read this document

Sections marked `## [AI:OPERATE]` contain operational guidance the AI should apply when helping with setup, audit, or ongoing operations. Inline guidance for AI is marked with `> AI:` callouts.

Decision frameworks are preferred over step-by-step procedures. The AI applies judgment using criteria given, asking the user when criteria conflict or context is unclear.

When in doubt, the precedence hierarchy is: aiconfig.md (the user's canonical config) overrides this document; this document overrides general defaults.

**Naming and path conventions:** CWOS uses lowercase `aiconfig.md` and lowercase folder paths throughout this document. Specific implementations may adopt different conventions (e.g., uppercase `AICONFIG.md` per a repo's "capitalized top-level operating files" convention; uppercase `CWOS.md` and `AGENTS.md` at repo root). Both forms are valid. The naming is implementation-specific; the *concept* is CWOS-defined. When this document says "aiconfig.md," read it as "your repo's canonical operational charter regardless of file name casing."

**Implementation-specific extensions:** some implementations add concepts beyond CWOS core. The aiconversations section below documents one such extension (long-form dialogue thread layer). Other implementations may add or omit such extensions.

---

# DIVISION 1: ARCHITECTURE

The system rests on three foundational concepts, in order of importance:

1. **The hub-and-spoke model** organizes repos by role
2. **The knowledge architecture** organizes information by purpose (configuration vs. state vs. procedure)
3. **The participants** identifies who is conversing through the system

Everything else in the document is downstream of these.

## The hub-and-spoke model

The **hub repo** holds reasoning, decisions, knowledge, project re-entry briefs, and reusable skills as markdown. It is the canonical source of context that survives tool churn and vendor changes.

The **spoke repos** are individual project repositories where development or production work happens. They contain project-specific configuration, skills, and code. Configuration in spokes is minimal and project-scoped; cross-cutting patterns live in the hub.

### Variants

Multiple valid topologies:

- **Multi-repo hub-and-spoke** — hub and spokes are separate git repositories. Cross-cutting reasoning lives in the hub; project work lives in spokes that reference the hub. CWOS spec's default.
- **Single-repo hub-and-spoke** — one git repository holds both the hub layer (operations/cwos/, aiconversations/, AICONFIG.md, etc.) and spoke-anchored content as folders (e.g., `aiconversations/spoke-<initiative>/`). The chevan-content repo uses this variant. Easier to operate for solo work; less suited for multi-team or multi-org coordination.
- **Spoke-only** — a repository serves as a spoke for a hub elsewhere. Minimal CWOS infrastructure locally; defers to the hub.
- **Single repo (no hub)** — both reasoning and work in one repo, treating the repo as both hub and spoke. Common for solo projects without separate hub.
- **Polyrepo coordination** — multiple repos sharing CWOS conventions without a centralized hub. Each repo follows the same operating pattern but operates independently.

All variants are valid CWOS deployments. CWOS-SETUP.md covers the four primary scenarios with specific bootstrap procedures.

### Why this works

Markdown in git is the lowest-common-denominator format. Every modern AI agent reads markdown. Git gives versioning, history, and portability. No vendor platform owns the state. If a tool disappears, the reasoning persists.

### The sovereignty test

Before adopting any tool, ask: does this require state to live outside git markdown in my control? If yes, it conflicts with the hub model. Most tools worth keeping pass this test; most that fail it are not worth the lock-in.

### The accumulation test

Before keeping any tool, skill, or configuration file: does this change what an agent or future-me can do reliably, or does it just exist for completeness? Anything that doesn't earn its place becomes maintenance debt.

## Knowledge architecture

The hub uses three layers with distinct purposes and access patterns. The split between them is the heart of the system.

### aiconfig.md — operational charter (configuration)

Heavy. Reasoning principles, conventions, cross-cutting constraints across all domains (software development, content production, civic activism, AI-augmented operations). Vendor-neutral. Updated deliberately when working patterns change.

**Mental model:** mostly write-by-human, read-by-AI. Static-ish. Changes are decisions.

**What goes in:**

* Reasoning principles applied across many situations
* Tone, voice, formatting preferences
* Cross-cutting constraints ("never recommend cloud-vendor lock-in")
* Pointers to where memory/, skills/, and other resources live and how to use them

### memory/ — accumulated state (history and observation)

A directory of markdown files. Decisions, project re-entry briefs, session notes, learned patterns. Read on demand by topic rather than preloaded.

**Mental model:** often write-by-AI (or AI-assisted), read-by-both. Dynamic. Grows continuously.

**Loading discipline matters.** Agents have context budgets. Two patterns:

* **Selective loading by task.** aiconfig.md describes what each memory subdirectory contains and when to consult it.
* **Always-loaded vs on-demand split.** A small `memory/always.md` contains the handful of things that should be in context every session. Everything else is on-demand.

The on-demand pattern is more vendor-portable. "Load on demand by topic" works for all agents; "preload everything" breaks silently once the directory crosses some threshold.

**What goes in:**

* `memory/decisions/` — cross-project ADRs
* `memory/projects/<spoke>/` — per-spoke state, re-entry briefs, notes
* `memory/enterprise/<client>/` — per-engagement context
* `memory/archive/` — completed or paused-indefinitely content
* `memory/always.md` (optional) — always-loaded context

### skills/ — reusable procedures

Each skill is a directory containing a SKILL.md file in the Agent Skills standard format. Loaded only when the task matches the skill's description field. Procedural knowledge that doesn't belong in always-loaded context.

**Mental model:** write-by-human (or AI-assisted), read-by-AI on demand. Procedural.

### The configuration-vs-state-vs-procedure split

* **aiconfig.md** answers "how should the AI reason and operate?"
* **memory/** answers "what has happened, been decided, or been learned?"
* **skills/** answers "how do I execute this specific procedure?"

If you find procedural knowledge in aiconfig.md, it consumes context every session whether relevant or not. Move it to a skill. If you find principles repeated across multiple memories or skills, they belong in aiconfig.md.

## Vendor entry points

Each vendor-specific entry-point file is a thin pointer to aiconfig.md:

* **CLAUDE.md** — Claude Code's native entry point
* **AGENTS.md** — community convention; some tools auto-load it
* **.cursorrules, .windsurfrules** — vendor-specific equivalents

**Thin pointer template:**

```markdown
# CLAUDE.md (or AGENTS.md, etc.)

See `aiconfig.md` for full operational context. This file exists so Claude Code
(or other agent) auto-loads context on session start.

Project-specific notes that don't belong in aiconfig.md can go here.
Keep brief; promote to aiconfig.md or a skill if it grows.
```

The moment entry-point files accumulate content, you have drift between them and the canonical file.

## Participants

The system serves four participants:

1. **Current-self.** The human doing work right now. Reads aiconfig.md and memory/ as reference, writes ADRs and re-entry briefs.
2. **AI agents.** Read entry-point files (which point to aiconfig.md), invoke skills, consult memory selectively, write to memory under guidance.
3. **Future-self.** Returns to projects after breaks. Reads re-entry briefs first, then ADRs, then code. The single user the system optimizes most heavily for.
4. **Other humans** (where applicable). Clients reading sanitized portions, future contractors, collaborators. The hub model accommodates them through filtering and sharing controls, though most hub content stays private.

Each participant interacts with the system through conversation: the AI literally, the humans through reading and writing artifacts that capture conversation.

## aiconversations — long-form dialogue threads (CWOS extension)

**What this is:** an optional fourth artifact type alongside aiconfig.md / memory / skills / reference. Captures long-form dialogue threads between human and AI as they evolve.

**Distinct from memory.** Memory holds decisions, state, learned patterns, and project re-entry briefs — concise structured records. aiconversations holds the dialogue process itself — back-and-forth exchanges over time, often spanning sessions and months. The two coexist:

- Memory: "here's what was decided about X" (1-2 paragraphs)
- aiconversations: the dialogue that led to deciding about X (many entries, often back-and-forth)

When a decision crystallizes in a conversation, capture it as an ADR in memory/decisions/ rather than expecting future readers to mine the conversation for it.

**Mental model:** write-by-both, read-by-both. Verbose. Grows linearly with project life; archived when size exceeds threshold.

**Layout options (both valid):**

- **Top-level `aiconversations/` folder** — chevan-content's pattern. One reasoning layer separate from `operations/cwos/memory/`. Subfolders by domain (media-platform/, business/, political-activism/, etc.) plus `spoke-*` folders for external initiatives.
- **Nested under `operations/cwos/memory/projects/<spoke>/conversations/`** — alternative pattern that treats conversations as project-state artifacts. Better for multi-spoke organizations where conversations are clearly project-bound.

**Run-prompt protocol** (chevan-content's convention; transferrable to other implementations):

- Entries alternate `### [User | DATE TIME]` and `### [AI Assistant | DATE TIME]`
- User invokes: "run prompt [User | DATE TIME] in FILE.md"
- AI reads the prompt, executes, writes response back as `[AI Assistant | ...]` entry, adds `[User | +2min]` stub
- Conversations companion to `-tasks.md` files for point-in-time status (e.g., per-issue task lists)

See the run-prompt-protocol skill in `operations/cwos/skills/` for the executable form.

**Archive when files exceed size threshold** (75KB warning, 100KB must-archive). The conversation-archiving skill handles this. Archive chunks live at `aiconversations/archives/<conversation>/<YYYY-MM-DD-HHMM>-archive.md` or sibling location.

**When to skip aiconversations entirely:**

- Pure code repos that don't accumulate long-form dialogue
- Short-cycle work where every conversation is self-contained in one session
- Cases where ADRs and re-entry briefs capture enough context

If you're not sure whether to use aiconversations, you probably don't need it yet. Start without; add the folder when conversation threads start spanning sessions and you want durable continuity.

## Foundation files (every repo)

### Required in every spoke

**CLAUDE.md / AGENTS.md** as thin pointers (see template above).

**README.md** human-facing: what the project does, how to use it. Distinct from agent config files.

**LICENSE** affects what dependencies agents can suggest.

**.gitignore** must explicitly exclude:

* Vendor-specific state (`.serena/cache/`, agent session logs, claude-mem output)
* Local environment files (`.env`, `.envrc.local`)
* Build artifacts and dependency directories

### Strongly recommended

**.editorconfig** universal editor config. Prevents formatting drift.

**justfile or Taskfile.yml** documents project commands at the repo root.

**.tool-versions (asdf) or mise.toml (mise)** declares runtime versions.

### Optional but high-value

**.envrc (direnv)** per-project environment activation.

**.env.example** documents expected environment variables.

**.pre-commit-config.yaml** runs linters and formatters before commit.

**.gitattributes** controls line endings, diff behavior.

## Hub repo structure

```
hub/
├── aiconfig.md                        # Operational charter (heavy, canonical)
├── CLAUDE.md                          # Thin pointer to aiconfig.md
├── AGENTS.md                          # Thin pointer to aiconfig.md
├── memory/
│   ├── README.md                      # Index and loading guidance
│   ├── always.md                      # Optional always-loaded context
│   ├── decisions/                     # Cross-project ADRs
│   ├── projects/
│   │   └── <spoke-name>/
│   │       ├── CLAUDE.md             # Mirror of spoke CLAUDE.md
│   │       ├── re-entry.md           # Current state for return
│   │       └── notes/
│   ├── enterprise/
│   │   └── <client-name>/
│   │       ├── conventions.md
│   │       └── re-entry.md
│   └── archive/                       # Completed or paused
├── skills/                            # Agent Skills standard
│   ├── README.md                      # Index of available skills
│   └── <skill-name>/
│       └── SKILL.md
└── reference/                         # Standing knowledge
    ├── mcp-stack.md                   # Standard MCP servers
    ├── conventional-commits.md
    ├── aider-config.md                # If using Aider
    └── ...
```

## Spoke repo baseline

```
<spoke>/
├── README.md
├── LICENSE
├── CLAUDE.md                          # Thin pointer
├── AGENTS.md                          # Thin pointer
├── .editorconfig
├── .gitignore
├── .gitattributes
├── .tool-versions or mise.toml
├── .envrc                             # if direnv used
├── .env.example
├── justfile or Taskfile.yml
├── .pre-commit-config.yaml
├── .mcp.json                          # if MCP servers used
├── .claude/
│   └── skills/                        # Spoke-specific skills
├── docs/
│   └── adr/                           # Spoke ADRs
├── CHANGELOG.md                       # auto-generated
└── ... (source)
```

**Add as needed:** .specify/, .serena/ (with gitignored memories), .devcontainer/, .github/workflows/, docker-compose.yml.

---

# DIVISION 2: OPERATIONS

The system requires ongoing maintenance to stay useful. Operations are organized as a cycle: session start, work, session end, with periodic review at quarterly and biannual intervals.

The most important operational principle: write before you stop, not after. Re-entry briefs, ADRs, and session notes capture context while it's fresh. Reconstructing them later loses 80% of the value.

## The session cycle

### Session start (when returning to a spoke after a break)

1. Read `hub/memory/projects/<spoke>/re-entry.md` first
2. Skim recent ADRs in hub and spoke
3. Run `git log --oneline -20` and `git status`
4. Open agent in the spoke
5. Ask agent to summarize current state and confirm against re-entry brief

### During the session

Capture decisions as they happen. If you and the AI consider multiple options and pick one, that's an ADR moment. Don't wait until the end of the session to remember it.

### Session end (before stopping work)

1. Commit or stash work in progress
2. Update `hub/memory/projects/<spoke>/re-entry.md` with state and next action
3. Write or update relevant ADRs
4. Mirror CLAUDE.md to hub if it changed materially

## Decision capture

### Architecture Decision Records (ADRs)

A `docs/adr/` directory of numbered markdown files (in spokes) or `memory/decisions/` (in hub).

**Template:**

```markdown
# ADR-NNN: Title

## Status
Proposed | Accepted | Deprecated | Superseded by ADR-NNN

## Context
What is the situation and why does it need a decision?

## Options
1. Option A: tradeoffs
2. Option B: tradeoffs
3. Option C: tradeoffs

## Decision
What was chosen and why.

## Consequences
What changes, what becomes harder, what we accept.
```

**Hub vs spoke split:**

* **Hub `memory/decisions/`:** Cross-project decisions. AI tooling choices, vendor evaluations, workflow patterns.
* **Spoke `docs/adr/`:** Project-specific decisions. Database choice, framework, API design.

### Re-entry briefs

The single highest-value artifact for the long-break problem. Write before you stop working.

**Location:** `hub/memory/projects/<spoke-name>/re-entry.md`

**Template:**

```markdown
# Re-entry: <project-name>

## Last session
Date, what was accomplished, what was left unfinished.

## Current state
Branch, uncommitted changes, failing tests, blocked items.

## Open questions
Decisions deferred, things to verify before continuing.

## Next action
The specific next step. Not a list. One thing.

## Context to reload
- Files to re-read
- Decisions to refresh
- External state to check
```

### Conventional Commits + CHANGELOG

Commit message convention (`feat:`, `fix:`, `refactor:`, `docs:`, `chore:`) plus `git-cliff` or `release-please` generates `CHANGELOG.md` from history.

## Special operations

### When starting an enterprise engagement

1. Read or generate their CLAUDE.md / AGENTS.md
2. Write summary in `hub/memory/enterprise/<client>/conventions.md`
3. Note constraints relevant to usual patterns
4. Configure spoke to match their conventions, not yours

### When adopting a new tool

1. Apply sovereignty test
2. Apply accumulation test
3. Write ADR in `hub/memory/decisions/`
4. Update entry-point files
5. Try on one spoke for a month before generalizing

## Periodic review

### Quarterly review

* Re-entry briefs current for active projects?
* New decisions captured as ADRs?
* Hub mirrors in sync with spokes?
* Skills worth promoting from spokes to hub?
* Tools no longer earning their place?

### Memory pruning (biannual)

* Move completed-project content to `memory/archive/`
* Consolidate session notes into summaries
* Promote recurring patterns into aiconfig.md or skills
* Delete content that turned out not to matter

### Patterns surfaced by pruning

Pruning is also discovery. Watch for:

* Same friction point mentioned across multiple re-entry briefs → signal to fix the underlying issue, not just record it
* Same decision considered repeatedly across ADRs → signal to elevate to aiconfig.md as a principle
* Same procedure described across multiple memory notes → signal to formalize as a skill
* Tool mentioned in multiple skills but never used productively → signal to remove from stack

## Adoption sequence

The operating system gets bootstrapped over time. Phased adoption avoids overload.

**Phase 1: Architecture**

* Hub structure (aiconfig.md, memory/, skills/)
* Thin pointers in hub and spokes
* .editorconfig, .gitignore, README.md, LICENSE in every spoke
* ADR convention for new decisions
* Re-entry briefs for projects being paused

**Phase 2: Operational hygiene**

* Conventional Commits + CHANGELOG generation
* justfile or Taskfile.yml per spoke
* .tool-versions or mise.toml
* .envrc

**Phase 3: Quality gates**

* .pre-commit-config.yaml
* Linter, formatter, type checker configs
* Test runner configuration

**Phase 4: Discipline**

* Spec Kit for next significant feature
* Project-specific skills
* Promote one-off skills to hub/skills/

**Phase 5: Intelligence layer (only if needed)**

* Serena on spokes where it earns its place
* Context7 if working across many unfamiliar libraries
* Claude Code code-review plugin

**Phase 6: Polish (most projects skip)**

* Repomix for cross-agent context moves
* Session capture (if memory/ proves insufficient)
* .devcontainer/

---

# DIVISION 3: COLLABORATION

This division defines the working contract between human and AI. Sections marked `## [AI:OPERATE]` contain guidance an AI applies when participating in the system.

The collaboration model: the AI is a participant, not an oracle. It contributes work, suggests options, captures artifacts, and asks for approval before significant actions. The human retains decision authority and final approval on anything modifying the system.

## Index of AI:OPERATE guidance

This document contains operational guidance the AI applies in these contexts:

* **Classifying content** (Architecture) — where new content belongs
* **Loading memory selectively** (Architecture) — what to load when
* **Spoke setup** (Architecture) — scaffolding a new spoke
* **Spoke audit** (Architecture) — reviewing an existing spoke
* **Capturing decisions** (Operations) — recognizing ADR moments
* **Skill creation** (Architecture, expanded in Reference) — when and how
* **Skill audit** (Reference) — reviewing existing skills
* **Spec Kit applicability** (Reference) — when to recommend
* **When to suggest Serena** (Reference) — fit criteria
* **MCP setup** (Reference) — choosing servers
* **Configuration audit** (Reference) — coverage, coherence, drift, sovereignty
* **Hub navigation tools** (Reference) — recommending Obsidian/Logseq
* **Claude Code setup** (Reference) — per-spoke
* **Claude Code audit** (Reference) — per-spoke
* **Suggesting alternatives** (Reference) — when primary stack misses
* **Pacing adoption** (Operations) — phase-aware recommendations
* **Session start** (Operations) — what to check
* **Session end** (Operations) — what to capture
* **Periodic review** (Operations) — quarterly and biannual

These appear inline at the relevant topics. The list above is for navigation when the AI is asked to perform a specific role.

## Core collaboration principles

### Decision authority

The AI proposes; the human decides. The AI may:

* Suggest changes, draft artifacts, identify gaps
* Apply decision frameworks given here
* Capture decisions and artifacts after they happen

The AI does not:

* Modify the hub or spoke without explicit approval
* Install tools without approval
* Commit, delete, or move content without approval
* Override user instructions even if they conflict with this document (but flag the conflict)

### Asking vs. acting

When the AI's guidance here gives clear criteria, the AI applies them and produces output. When criteria are ambiguous or context is missing, the AI asks rather than guessing.

Specifically: if scaffolding files, list what will be created and ask for confirmation. If recommending tools, present 1-2 options with tradeoffs rather than just one. If unsure about classification (where content belongs, whether a skill applies), ask.

### Engagement style

The user values direct engagement and minimal preamble. The AI should:

* Not summarize this document back unsolicited
* Not open responses with characterizations of the user's input
* Not affirm the quality of the user's thinking before responding to it
* Apply guidance when relevant; ask clarifying questions when context is missing

### Conflict handling

When user requests conflict with this document, follow the user but note the conflict so the document can be updated if the conflict reflects a real change in working pattern.

When tools or methods this document recommends are no longer current, flag that and offer to update the document rather than silently using stale guidance.

## [AI:OPERATE] Classifying content

When asked to add content to the hub, decide where it belongs:

| If the content is...                                 | It belongs in...                                    |
| ---------------------------------------------------- | --------------------------------------------------- |
| A reasoning principle applied across many situations | aiconfig.md                                         |
| A specific decision with context and consequences    | memory/decisions/ (ADR)                             |
| Project-specific state, status, or notes             | memory/projects/`<spoke>`/                        |
| A repeatable procedure with clear inputs and outputs | skills/`<name>`/SKILL.md                          |
| Observed pattern not yet formalized                  | memory/notes/ or memory/projects/`<spoke>`/notes/ |
| Reference material the user reads occasionally       | reference/                                          |
| Per-engagement client context                        | memory/enterprise/`<client>`/                     |

When unclear, ask the user. Default toward smaller, more specific locations.

## [AI:OPERATE] Loading memory selectively

When working on a spoke, don't load all of memory/ blindly:

1. Check `memory/always.md` if it exists; that's always-context
2. Check `memory/projects/<current-spoke>/` for that spoke's state
3. Consult `memory/decisions/` only when making a decision that might already be settled, or when the user references a prior decision
4. Consult `memory/enterprise/<client>/` only when working on that client's engagement
5. Skip `memory/archive/` unless explicitly relevant

If memory/ has a README.md, use it as the index.

## [AI:OPERATE] Spoke setup

When asked to set up a new spoke or scaffold a project:

**Always create:**

* README.md (ask for project description)
* LICENSE (default to user's preferred license from aiconfig.md if specified)
* .gitignore (use language-appropriate template)
* .editorconfig
* CLAUDE.md and AGENTS.md as thin pointers
* justfile or Taskfile.yml (ask which user prefers)

**Create if applicable:**

* .tool-versions or mise.toml if the project has runtime dependencies
* .env.example if the project uses environment variables
* .mcp.json if the project uses MCP servers (offer standard stack from hub/reference/mcp-stack.md)
* .pre-commit-config.yaml if the project will accumulate code

**Do not create without asking:**

* .devcontainer/ (heavy)
* CI configs (.github/workflows/, etc.)
* Docker compose files
* Vendor-specific files for tools not mentioned

**Confirm before scaffolding:** list what you propose and ask user to confirm or modify.

## [AI:OPERATE] Spoke audit

When asked to audit an existing spoke against this standard:

**Report structure:**

1. **Foundation files present:** check each required and recommended file
2. **Foundation files configured correctly:** brief check that present files are non-empty and follow conventions
3. **Knowledge architecture:** CLAUDE.md/AGENTS.md present and appropriately scoped?
4. **Decision capture:** docs/adr/ present? CHANGELOG.md maintained?
5. **Vendor lock-in risks:** any config files failing sovereignty test?
6. **Stale or redundant files:** unused, deprecated, or contradicted

**Report format:**

* Group findings by severity (gap, drift, risk, redundancy)
* For each finding, state the criterion and what's missing or wrong
* Recommend specific action but do not modify without approval

## [AI:OPERATE] Capturing decisions

When the user makes a decision in conversation, recognize it and offer to record it. A decision worth recording has these markers:

* Multiple options were considered
* Tradeoffs were discussed
* The choice will affect future work
* The reasoning is non-obvious from the outcome

When you detect this, offer to draft an ADR. Ask hub-level or spoke-level, and the next ADR number. Draft using template; present for review before writing.

When the user is about to stop work on a project, offer to draft or update the re-entry brief.

## [AI:OPERATE] Session start

When a new session begins on a spoke:

1. Check for `hub/memory/projects/<spoke>/re-entry.md` and read if present
2. Check `git status` and recent log
3. If state differs significantly from re-entry brief, flag before proceeding
4. If no re-entry brief exists, suggest creating one when the session ends

## [AI:OPERATE] Session end

When user indicates wrapping up:

1. Offer to update `hub/memory/projects/<spoke>/re-entry.md` with what was accomplished, current state, next action
2. Offer to draft any ADRs for decisions made during the session
3. Suggest commits if there are reasonable commit boundaries

Do not write to memory or commit without approval.

## [AI:OPERATE] Pacing adoption

When user asks "what should I add next" or "what am I missing":

1. Determine current phase by checking which prior-phase items exist
2. Recommend completing the current phase before advancing
3. Do not propose Phase 5 or 6 items if Phase 1-3 are incomplete
4. If user explicitly asks about a later-phase item, answer but note dependencies

## [AI:OPERATE] Periodic review

When user asks for quarterly or biannual review:

**Quarterly:**

1. List active spokes (memory/projects/ excluding archive/)
2. For each, check if re-entry.md was updated in the last 90 days
3. List decisions added to memory/decisions/ in the last quarter
4. Check hub-side mirrors against current spoke CLAUDE.md content
5. Survey hub/skills/ for stale or unused skills
6. Report findings; offer to draft prompts to address them

**Biannual (memory pruning):**

1. Identify completed or paused-indefinitely projects in memory/projects/
2. Propose moving them to memory/archive/
3. Find recurring patterns that might warrant promotion to aiconfig.md or a skill
4. Find ADRs marked Deprecated or Superseded that could be archived
5. Surface "patterns from pruning" insights to the user

Do not move, delete, or modify content without approval. Present a structured proposal.

---

# DIVISION 4: REFERENCE

Catalogs of tools, configurations, and alternatives. Not foundational; consulted when expanding the system or making specific choices.

## Configuration files catalog

A checklist of configuration files. Most are universal across agents.

### Repo metadata

* README.md
* LICENSE
* CHANGELOG.md (auto-generated)
* CONTRIBUTING.md
* SECURITY.md (when relevant)

### Agent configuration (vendor-neutral)

* AGENTS.md (thin pointer to aiconfig)
* .mcp.json (MCP server configuration)
* aiconfig.md (canonical name; if used at spoke level)

### Agent configuration (vendor-specific, thin pointers)

* CLAUDE.md (Claude Code)
* .cursorrules (Cursor)
* .windsurfrules (Windsurf)
* .aider.conf.yml (Aider)

### Editor and formatting

* .editorconfig
* .gitattributes
* .gitignore

### Toolchain and runtime

* .tool-versions or mise.toml (preferred)
* .python-version, .nvmrc, .ruby-version (single-tool fallbacks)
* pyproject.toml, package.json, Cargo.toml, go.mod (language manifests)

### Environment

* .envrc (direnv)
* .env.example
* secrets/ patterns with sops or git-crypt

### Quality gates

* .pre-commit-config.yaml
* Linter configs: .eslintrc, ruff.toml, .golangci.yml
* Type checker configs: tsconfig.json, mypy.ini, pyrightconfig.json
* Test runner configs: pytest.ini, vitest.config.ts, jest.config.js

### Build and execution

* justfile, Taskfile.yml, or Makefile
* docker-compose.yml
* .devcontainer/devcontainer.json

### CI/CD

* .github/workflows/*.yml
* .github/CODEOWNERS
* renovate.json or dependabot.yml

### Priority for solopreneur spokes

Most valuable first:

1. .editorconfig
2. justfile or Taskfile.yml
3. .tool-versions or mise.toml
4. .pre-commit-config.yaml
5. .mcp.json
6. AGENTS.md stub

Skip unless needed: .devcontainer/, SECURITY.md, CODEOWNERS, vendor-specific files for unused tools.

### [AI:OPERATE] Configuration audit

When auditing a spoke's configuration:

**Coverage check:** confirm priority configs (1-6 above) are present and non-empty.

**Coherence check:** flag contradictions:

* Multiple runtime version sources specifying different versions
* Linter configs that contradict each other
* Pre-commit hooks referencing uninstalled tools
* justfile commands that fail when run

**Drift check:** flag stale references:

* Config files mentioning tools no longer in the project
* Commands referencing moved or renamed paths
* CI workflows targeting branches that no longer exist

**Sovereignty check:** flag any config requiring cloud state or vendor-specific platforms.

Present findings as a structured report. Suggest fixes but don't apply without approval.

## Agent Skills standard

Anthropic published the Agent Skills specification as an open standard in December 2025. As of early 2026, 30+ tools support it including Claude Code, OpenAI Codex, Cursor, VS Code, Gemini CLI, JetBrains Junie, AWS Kiro, Block Goose. The spec lives at agentskills.io.

### Format

A skill is a directory:

```
my-skill/
├── SKILL.md          # Required: frontmatter + instructions
├── scripts/          # Optional: executable code
├── references/       # Optional: documentation
└── assets/           # Optional: templates
```

**SKILL.md structure:**

```markdown
---
name: skill-name
description: Specific description of when to use this skill
---

# Skill Name

Instructions for the agent...
```

The description field is the trigger. Be specific.

**Description field test:** would another agent (not the one being used right now) be able to determine when to invoke this skill from the description alone? If no, rewrite.

### Hub skills

Maintain `hub/skills/` directory of skills reused across projects. Copy or symlink into spokes as needed.

**Symlinks vs copies:**

* Symlinks: improvements propagate to all spokes. Better for personal projects.
* Copies: spoke is self-contained. Better for client engagements.

### Cross-vendor portability

Stick to the core format in hub skills:

* Required frontmatter only (`name`, `description`)
* Markdown instructions
* Standard subdirectories if needed

Avoid vendor-specific extensions in hub skills.

### Security

A 2026 study found 36% of skills on major registries contain security flaws. Skills run with shell access and filesystem permissions. Audit before installing from any third-party source.

### [AI:OPERATE] Skill creation

When user describes a procedure they repeat across sessions, offer to create a skill. Triggers:

* User describes "the way I do X" with specific steps
* User has done the same task multiple times in recent sessions
* User asks "is there a way to make Claude/the AI do this consistently?"

**Creating a skill:**

1. Ask: hub skill (reused) or spoke skill (project-specific)?
2. Draft SKILL.md with frontmatter and instructions
3. Description field must be specific enough that an agent knows when to invoke
4. Avoid vendor-specific features in hub skills

### [AI:OPERATE] Skill audit

When reviewing existing skills:

**Check each for:**

* Description field specificity
* Vendor-specific features in hub skills (push to spoke-level)
* Overlap with other skills (consolidate or differentiate)
* Skills not invoked or referenced in months (consider archiving)
* Skills mentioning deprecated tools

Report findings; do not modify without approval.

## Spec Kit (`github/spec-kit`)

Spec-driven development toolkit. Workflow chain produces versioned documents:

* `/speckit.constitution` → `.specify/memory/constitution.md`
* `/speckit.specify` → `spec.md`
* `/speckit.plan` → `plan.md`
* `/speckit.tasks` → `tasks.md`
* `/speckit.implement` → code

**When to use:** Feature work large enough to justify upfront planning. Skip for small bug fixes and tooling tweaks.

**Install:**

```bash
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git
specify init <project-name> --ai claude
```

**Known gap:** `specify init --ai claude` does not generate CLAUDE.md. Write it manually.

**Vendor-agnostic:** Supports Claude Code, Gemini CLI, Copilot, Cursor, and others.

**Watch for:** Agents may reimplement things from scratch when the spec leaves room for interpretation. Add a constitution principle like "prefer well-established libraries over custom implementations."

### [AI:OPERATE] Spec Kit applicability

When user describes new feature work, assess whether Spec Kit applies:

**Spec Kit fits when:**

* Feature has clear acceptance criteria
* Multiple components or decisions involved
* Work will span multiple sessions
* Project benefits from documented intent

**Spec Kit overhead exceeds value when:**

* Change is small (single bug fix)
* User is exploring rather than building
* Spec Kit isn't set up and cost of setup exceeds the work itself

If Spec Kit fits and isn't set up, offer to install. If set up, suggest starting with `/speckit.specify`.

## Semantic code intelligence

### Serena (MCP server)

Symbol-level code retrieval and editing via Language Server Protocol.

**When it helps:**

* Repos large enough that whole-file reads waste tokens
* Cross-file refactors
* Multi-language codebases

**Install (do not use marketplace versions; outdated):**

```bash
claude mcp add serena -- uvx --from git+https://github.com/oraios/serena \
  serena start-mcp-server --context ide-assistant --project $(pwd)
```

**Memory conflict warning:** Serena creates `.serena/memories/`. If hub memory/ is canonical, disable Serena's memories or gitignore them.

### Context7 (MCP server)

Documentation provider feeding current, version-specific library docs to agents.

**When it helps:** Projects spanning unfamiliar libraries or where API surfaces change rapidly.

### [AI:OPERATE] When to suggest Serena

Suggest Serena when:

* Large codebase (multiple modules, 10K+ lines)
* Refactor work spanning many files
* Agent reading whole files unnecessarily
* Agent missing references during renames

Don't suggest Serena when:

* Project is small (under 5K lines)
* Project is short-lived or experimental
* User has working setup without it

When suggesting, mention memory conflict and ask whether to gitignore Serena's memories.

## MCP servers reference

The Model Context Protocol (MCP) standardizes how agents connect to external tools and data sources. MCP servers run locally or proxy to services you control, compatible with the sovereignty model.

### Recommended baseline stack

**Filesystem MCP** — structured file access beyond what's built in.

**Git MCP** — git operations as tool calls. More reliable for complex git workflows.

**Sequential thinking MCP** — structured thinking scratchpad for complex reasoning.

### Project-specific

**Serena** — semantic code intelligence (covered above).

**Context7** — current library documentation (covered above).

**GitHub MCP / GitLab MCP** — issue tracking, PR management, repo operations.

**Database MCPs** (Postgres, SQLite, etc.) — query and inspect databases directly.

**Browser/Playwright MCP** — browser automation for testing or scraping.

**Linear / Notion / Obsidian MCPs** — connect to existing knowledge or task tools. Evaluate against sovereignty.

### Hub-level MCP documentation

Maintain `hub/reference/mcp-stack.md` documenting your standard MCP server set, project-specific servers, installation commands, and sovereignty notes for cloud-coupled options.

### [AI:OPERATE] MCP setup

When user sets up a new spoke:

1. Check `hub/reference/mcp-stack.md` for user's standard set
2. Propose baseline (filesystem, git, sequential thinking) by default
3. Suggest project-specific MCPs based on project type
4. Do not propose cloud-coupled MCPs without flagging sovereignty tradeoff

## Hub knowledge management tools

Tools that add navigation, backlinks, and search on top of markdown without breaking the git+markdown sovereignty model.

### Obsidian

Local-first knowledge management. Reads markdown directories directly. Graph view, backlinks, search, plugins. Vault is the file system; no proprietary format.

**Fit:** strong. Caveats: Obsidian Sync is paid and cloud-based; using git for sync matches the model.

### Logseq

Open-source local-first alternative. Block-based but stores in markdown. Strong outliner workflow.

**Fit:** strong.

### Foam

VS Code extension for Obsidian-style knowledge management. Lighter; works inside the editor.

**Fit:** good if you live in VS Code.

### Quartz

Static site generator for publishing markdown notebooks. For publishing sanitized portions of the hub publicly.

### [AI:OPERATE] Hub navigation tools

If user asks about navigating or visualizing the hub:

1. Recommend Obsidian or Logseq based on preference
2. Note that git remains canonical; the tool is a view over the files
3. Do not suggest cloud sync features without flagging sovereignty implications

## Context portability

### Repomix

Bundles a repo into a single file optimized for LLM context. Two uses:

* Feeding a whole spoke into a fresh agent session
* Moving context to a different agent for vendor-agnostic workflows

### Session capture (optional)

Tools like `claude-mem` capture session activity. Use with caution; this is a third memory system alongside hub memory/ and Serena's memories. Designate one as canonical.

## Vendor-specific sections

Most patterns apply regardless of agent. Sections below cover what's specific to each vendor.

### Claude Code

Native support for CLAUDE.md, skills, plugins, hooks.

#### CLAUDE.md

Native entry point. Loaded automatically. Thin pointer to aiconfig.md. Subdirectory CLAUDE.md files also read.

#### Custom slash commands

`.claude/commands/*.md`. Lower priority than skills now that skills are standardized.

#### Output styles

`.claude/output-styles/`. Vendor-specific.

#### Hooks

Events (SessionStart, PreToolUse, PostToolUse) configured in plugins or `.claude/hooks/`.

**Practical uses:**

* Auto-changelog hook on commits
* SessionStart hook injecting memory/re-entry content
* PreToolUse hook to block risky operations

Prefer skills over hooks when both could solve the problem.

#### Plugins

Bundle commands, agents, skills, hooks.

**Worth considering:**

* Official `code-review` plugin (CLAUDE.md compliance agent)
* Documentation plugins (evaluate individually)

**Skip:**

* Large opinionated frameworks (SuperClaude, claude-flow, BMAD)
* Marketplace installs of Serena (install from source)

#### MCP configuration paths

* Project-level: `.mcp.json` in repo root
* User-level: `~/.claude.json` or equivalent
* Plugin-bundled: `.mcp.json` within a plugin

#### Subagents

`.claude/agents/` or plugin-bundled. Overkill for typical solopreneur work.

#### Permissions

Recommended for solopreneur work:

* Auto-approve: reading files, running tests, linting
* Require approval: file writes outside expected paths, network calls, destructive git operations, package installs

Tighter for client engagements.

#### [AI:OPERATE] Claude Code setup in a spoke

1. Verify CLAUDE.md is a thin pointer to aiconfig.md
2. If Serena appropriate, add to .mcp.json
3. Offer standing MCP servers from hub/reference/mcp-stack.md
4. Ask whether to create .claude/skills/
5. Do not install plugins without asking

#### [AI:OPERATE] Claude Code audit in a spoke

Check:

* CLAUDE.md present and appropriately scoped
* .mcp.json reflects user's standard servers
* .claude/skills/ contains spoke-specific skills only
* .claude/commands/ contains commands not better expressed as skills
* Plugins installed are listed; flag untrusted sources
* Permissions match user's stated preferences

### Aider

Git-aware AI coding tool with strong vendor-agnostic credentials. Works with Claude, OpenAI, local models. Designed around git commits as the unit of change.

**Fit with hub-and-spoke:** strong. Operates on files in your repo, commits to git, supports custom instructions via `.aider.conf.yml`. Compatible with sovereignty model.

**When to use:**

* Working with a non-Claude model
* Want commit-per-change discipline built in
* Want lighter-weight CLI than Claude Code

**When Claude Code is better:**

* Working with skills, plugins, hooks
* MCP server integration
* Larger agentic workflows beyond code editing

**Entry-point file:** `.aider.conf.yml` (YAML; not thin pointer pattern). Document standing preferences in `hub/reference/aider-config.md`.

### Codex CLI, Gemini CLI, Cursor, others

Multiple agents read AGENTS.md and support Agent Skills. Thin-pointer pattern works for all of them. Vendor-specific features live in spoke-level configs.

For occasional use, install on demand; don't maintain configurations for agents you don't use regularly.

## Alternatives catalog

Tools deliberately not in the primary stack, with reasoning. For discovery and for when you reach the limits of the current setup.

### AI agents and assistants

**Cursor** — VS Code fork with deep AI integration. Vendor-platform-bound. Worth using for client engagements that require it.

**Windsurf** — similar to Cursor.

**GitHub Copilot** — autocomplete-focused. Most useful in enterprise contexts where already deployed.

**Continue.dev** — open-source AI assistant in VS Code/JetBrains. More flexible than Copilot. Complements rather than replaces Claude Code.

**Codeium / Codeium Forge** — competing autocomplete and agent. Cloud-coupled.

**Tabnine** — autocomplete focused.

**Amazon Q Developer** — AWS-coupled.

**OpenHands (formerly OpenDevin)** — open-source autonomous agent. Heavier than needed for solopreneur work.

**Goose (Block)** — open-source agent CLI with strong MCP support. Worth watching.

**Kiro (AWS)** — newer agent with Agent Skills support. AWS-coupled.

### Frameworks (deliberately skipped)

**SuperClaude Framework** — opinionated; bundles too much.

**claude-flow** — enterprise orchestration. Overkill for solopreneur work.

**BMAD-Method** — agile-style multi-agent. Heavy framework.

**ccpm** — project management via GitHub Issues. Useful for teams.

### Knowledge/memory services (sovereignty concerns)

**mem0** — managed memory layer. Cloud-coupled.

**Letta** — agent memory framework. Useful as framework; as-a-service conflicts with sovereignty.

**Pieces** — code snippet and context manager. Cloud-coupled.

### Documentation tools

**Mintlify** — AI-augmented docs platform. Cloud-coupled.

**README-AI** — generates READMEs. Trivial with Claude Code directly.

**Docusaurus + AI plugins** — markdown directory + static site generator is the lightweight version.

### Code understanding alternatives

**Sourcegraph Cody** — code search and AI assistance. Strong for very large codebases. Cloud-coupled (Cody Enterprise) or self-hosted.

**Greptile** — codebase chat. Cloud-coupled.

**ast-grep** — structural search and rewrite. Not AI; useful complement for refactors.

### Testing and quality

**Codium AI / Qodo** — AI test generation.

**Snyk Code** — AI-augmented security scanning. Cloud-coupled.

**DeepSource** — code quality with AI. Cloud-coupled.

### Observability and evaluation

**Langfuse** — open-source LLM observability. Self-hostable.

**Helicone** — LLM observability. Has self-hosted option.

**LangSmith** — LangChain's observability. Cloud-coupled.

**Promptfoo** — open-source prompt evaluation.

### [AI:OPERATE] Suggesting alternatives

When user describes a pain point not covered by primary stack:

1. Identify what category of tool would help
2. Check alternatives catalog for relevant entries
3. Present 1-2 options with sovereignty and accumulation tests applied
4. Do not recommend cloud-coupled tools without flagging tradeoff

## Cross-vendor portability checklist

If moving a project to a different agent:

* **aiconfig.md content:** transfers via the new agent's entry-point file
* **AGENTS.md:** read by an increasing number of agents
* **Skills in standard format:** work across 30+ tools
* **Spec Kit artifacts:** agent-portable
* **ADRs:** universal
* **MCP servers:** work across MCP-capable agents
* **Claude Code-specific** (slash commands, hooks, output styles, plugins): do not transfer

The hub repo is fully portable. Nothing in it depends on any specific vendor.

---

## Reading this document as an AI

If you are an AI being given this document as part of session context:

1. The document has four divisions: Architecture (what the system is), Operations (how it's maintained), Collaboration (how we work together), Reference (catalog material)
2. Apply guidance in `## [AI:OPERATE]` sections when relevant operations come up
3. Apply `> AI:` inline callouts as situational guidance
4. When user requests conflict with this document, follow the user but note the conflict
5. Treat decision frameworks here as defaults, not mandates; apply judgment

When in doubt, the precedence hierarchy is: aiconfig.md (canonical) overrides this document; this document overrides general defaults. If aiconfig.md isn't available, follow this document.

The user values direct engagement and minimal preamble. Do not summarize this document back unsolicited. Apply guidance when relevant; ask clarifying questions when context is missing.
