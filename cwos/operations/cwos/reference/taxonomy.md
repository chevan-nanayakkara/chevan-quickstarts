# CWOS Taxonomy

**Purpose:** definitions of CWOS terms. Read this to standardize vocabulary across sessions and across repos. When terms appear in conversation files, AICONFIG.md, or other docs, the definitive meaning lives here.

---

## Architecture concepts

**hub** — a Git repository that serves as the canonical reasoning, decision, and knowledge layer for one or more spokes. Contains AICONFIG.md, CWOS.md, memory/, skills/, and reference material. Holds context that survives tool churn and vendor changes.

**spoke** — a Git repository where development or production work happens. References a hub for cross-cutting reasoning. Contains project-specific configuration, code, and artifacts. Spokes can have their own ADRs (`docs/adr/`) for project-specific decisions.

**hub-and-spoke** — the architectural pattern where a hub and one or more spokes coordinate through Git. Variants: multi-repo (hub and spokes as separate repos), single-repo (hub and spokes as folders within one repo), and polyrepo (multiple peer repos sharing conventions without a centralized hub).

**aiconfig.md** — the canonical operational charter. Holds reasoning principles, conventions, cross-cutting constraints. Vendor-neutral text. Read by all AI tools via their entry-point files. The CWOS spec uses lowercase `aiconfig.md` as the canonical name; some implementations use uppercase `AICONFIG.md` per a capitalized top-level operating configuration convention. Both forms are valid.

**memory (CWOS layer)** — the directory at `operations/cwos/memory/` (or `hub/memory/` per CWOS spec). Holds decisions (ADRs), per-project re-entry briefs, observations, learned patterns, always-loaded context, archived content. Read on demand by topic.

**memory (Claude per-project)** — Claude Code's per-project memory directory at `~/.claude/projects/<slug>/memory/`. Auto-loaded by Claude Code at session start. CWOS-aligned repos symlink this to `operations/cwos/memory/` so the canonical location is the hub repo's memory layer; Claude continues to auto-load via the symlink.

**skill** — a reusable procedure stored as a directory containing a `SKILL.md` in the Agent Skills standard format (`name`, `description`, body). Invoked on demand when a task matches the skill's description. Codifies multi-step procedures with runtime decisions; not a single rule.

**pattern** — older term for what's now called a skill. Patterns lived at `operations/conversational-work/ai-work-patterns/` (legacy in repos that ran the predecessor infrastructure). New procedural content goes to skills/ in Agent Skills format.

**command** — usually a slash-command invocation (`/<name>`) that maps to a skill or other vendor-specific UX shortcut. Commands are vendor-specific UX; the content they trigger may be vendor-agnostic.

**vendor entry point** — a thin pointer file at the root of a repo that an AI vendor expects to read. Examples: `CLAUDE.md` (Claude Code), `AGENTS.md` (Codex, Cursor, others reading the cross-vendor AGENTS.md convention), `.cursorrules` (Cursor's older format), `.windsurfrules` (Windsurf), `.aider.conf.yml` (Aider).

**thin pointer** — a minimal vendor entry point file that says "read AICONFIG.md (or equivalent)" and doesn't duplicate behavioral content. Keeps content single-source. Vendor entry points provide tool-specific discoverability; AICONFIG.md provides the actual rules.

**discovery mirror** — a folder at `operations/{vendor}-configs/` that uses symlinks to present a single-folder view of a vendor's runtime configuration. Pattern is documented for historical context; most CWOS-aligned repos use the canonical memory location in the hub instead and don't need the discovery-mirror pattern.

## The three-layer split

**configuration** — always-on guidance. "Always do X." "Never do Y." Lives in AICONFIG.md. Read at session start; token cost amortized across the session.

**state** — accumulated facts about what happened, was decided, was learned. Lives in `operations/cwos/memory/`. Read on demand by topic.

**procedure** — reusable multi-step workflows with runtime decisions. Lives in `operations/cwos/skills/` as Agent Skills. Loaded on demand when invoked.

This three-way split is the core of the CWOS three-layer framework. The split helps decide where new behavioral content goes: rule → AICONFIG.md, decision → memory/decisions/, procedure → skills/.

## Artifact types

**ADR (Architecture Decision Record)** — a structured record of a cross-cutting decision, with context, options considered, choice, and consequences. Lives at `operations/cwos/memory/decisions/` (hub-level cross-project) or `docs/adr/` (spoke-level project-specific). Convention from Michael Nygard, 2011, widely adopted.

**re-entry brief** — a project-level summary that lets future-you or an AI session resume work after a long break. Captures last session, current state, open questions, next action, context to reload. Lives at `operations/cwos/memory/projects/<spoke>/re-entry.md`. Highest-value artifact for the long-break problem.

**conversation file** — a long-form dialogue thread between human and AI using the run-prompt protocol. Entries alternate `### [User | DATE TIME]` and `### [AI Assistant | DATE TIME]`. Lives in `aiconversations/<domain>/<name>-conversation.md` or `aiconversations/spoke-<initiative>/<name>-conversation.md`.

**tasks file** — point-in-time status companion to a conversation file. Lives next to its conversation: `<name>-tasks.md`. Lighter than re-entry briefs but at finer granularity (per-issue task lists, not project-level summaries).

**archive chunk** — a historical slice of a conversation file that's been archived to keep the active file under size threshold. Lives at `aiconversations/archives/<conversation>/<YYYY-MM-DD-HHMM>-archive.md`.

## Loading patterns

**always-loaded context** — content that enters the AI's context at session start, regardless of task. Examples: AICONFIG.md (canonical rules), MEMORY.md index. Use sparingly; every always-loaded item is per-session token cost.

**on-demand context** — content loaded when the AI's current task matches some signal. Examples: a specific decision under memory/decisions/ (loaded when its topic comes up), a skill's body (loaded when the description matches), a conversation file (loaded when working on that thread). Most content is on-demand; always-loaded is the exception.

## Operating tests

**sovereignty test** — before adopting any tool, ask: does this require state to live outside git markdown in user control? If yes, it conflicts with the CWOS sovereignty principle. Most tools worth keeping pass this test.

**accumulation test** — before keeping any tool, skill, or configuration file, ask: does this change what an agent or future-you can do reliably, or does it just exist for completeness? Things that don't earn their place become maintenance debt.

## Session lifecycle

**cold start** — a fresh AI session begins. Auto-loaded files (CLAUDE.md, MEMORY.md) are read; explicit prompt loads AICONFIG.md and README.md. The cold-start prompt template lives in `/README.md` "Session-start prompts" or in `/CWOS-SETUP.md` "Refreshing an existing session" → Scenario 1.

**post-compaction refresh** — when an AI tool compacts the conversation as context fills. Auto-loaded files remain; conversation history is compressed. Re-orient by asking the AI to re-read AICONFIG.md, README.md, and the repo's work-status dashboard.

**post-update refresh** — after editing AICONFIG.md or other config mid-session, the AI still has the pre-edit version in context. Ask the AI to re-read.

**mid-session drift** — the AI is producing output that violates rules. Specific recalibration beats full re-read: cite the specific rule and the violation.

**always-restart pattern** — operational preference: when context needs refreshing, restart the session rather than refresh in place. All reasoning lives in durable artifacts (conversation files, memory, AICONFIG.md), so restart is cheap and produces a cleaner state than refresh.

## Voice

**authentic voice** — the user's natural voice when drafting content they'll publish. Ellipses for pauses, conversational rhythm, direct tone, no AI-polished cadence. Applies to essays, social posts, anything in the user's name.

**professional voice** — the user's preference for analytical / technical / business responses. Used in general AI conversations and in operational dialogue.

## CWOS folder vocabulary

**operations/cwos/** — the CWOS implementation layer in a repo. Three subfolders: memory/, skills/, reference/.

**operations/conversational-work/** — the predecessor to operations/cwos/ (in repos that ran the older infrastructure). Legacy after the CWOS migration; preserved with a LEGACY notice on its README.

**aiconversations/** — the reasoning thread layer (CWOS extension, optional). Long-form dialogue between user and AI using the run-prompt protocol. Distinct from memory (which is decisions, state, summaries).

**aiconversations/spoke-{name}/** — folders for reasoning threads anchored to external initiatives or repos. The `spoke-` prefix signals that primary truth for the initiative lives outside this hub.

**aiconversations/0-work-status.md** — convention for the primary navigational artifact in a CWOS-aligned repo. Dashboard format varies per repo (five-table schema in chevan-content, business-model-aligned schema elsewhere).

## Vocabulary that's deprecated

- **three-bucket framework** — superseded by CWOS's three-layer split (configuration / state / procedure). Same idea, different vocabulary; the three-layer name aligns with the CWOS spec.
- **discovery mirror** — pattern documented for historical context but rarely adopted. CWOS-aligned repos typically use the canonical memory location in the hub instead.
- **conversational-work** as the active authoring layer — superseded by `operations/cwos/`. `operations/conversational-work/` remains as legacy in repos that had it installed.
- **pattern** — superseded by `skill` (Agent Skills standard).
- **0-work-priority.md** — renamed to `0-work-status.md` in the CWOS naming convention (the file shows status, not priority).

---

[End of Document]
