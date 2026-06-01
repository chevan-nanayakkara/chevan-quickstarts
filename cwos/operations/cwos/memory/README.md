# operations/cwos/memory/ — Accumulated State (templates only in this starter)

The memory layer holds the **state** dimension of CWOS's three-layer split: decisions, project status, accumulated learnings, feedback memories. Read on demand by topic.

In this starter, only the templates are present. Real memory content is per-repo and lives in each target repo's own `operations/cwos/memory/` directory.

---

## Templates

- **[`decisions/_template.md`](decisions/_template.md)** — Architecture Decision Record (ADR) template. Michael Nygard's 2011 convention. Copy when capturing a cross-cutting decision: `decisions/001-cwos-architecture.md`, `decisions/002-spoke-extraction-pattern.md`, etc. Zero-padded three-digit sequential numbering.
- **[`projects/_template.md`](projects/_template.md)** — Re-entry brief template. The highest-value artifact for the long-break problem. Per-project; one re-entry brief per active or paused initiative. Copy to `projects/<spoke>/re-entry.md`.

## What goes in a target repo's memory/

After CWOS deployment, the target repo's `operations/cwos/memory/` typically contains:

- **`MEMORY.md`** — index of feedback memories. Auto-loaded at session start (small file; tracks pointers, not content).
- **`always.md`** (optional) — content that always loads in every session. Use sparingly; every always-loaded item is per-session token cost.
- **`feedback_*.md`** — individual feedback memory files. Capture user preferences, corrections, validated approaches. Indexed in MEMORY.md.
- **`decisions/`** — ADRs (NNN-slug.md format). `decisions/001-cwos-architecture.md` is conventional for the first ADR capturing the migration / bootstrap decision.
- **`projects/<spoke>/`** — per-project re-entry briefs. One folder per active project; one `re-entry.md` inside.
- **`archive/`** — completed or paused content. Memories that are no longer load-bearing but should be preserved for reference.

## Loading patterns

- **MEMORY.md, always.md, and individual feedback_*.md files referenced from MEMORY.md** typically auto-load at session start (via Claude Code's per-project memory mechanism, or via cold-start prompts for other vendors).
- **decisions/<file>.md, projects/<spoke>/re-entry.md, archive/<file>.md** are loaded on demand — when the topic comes up or the user explicitly references them.

The Claude Code per-project memory path is `~/.claude/projects/<slug>/memory/`. CWOS-aligned repos symlink this to `operations/cwos/memory/` so Claude continues to auto-load while the canonical location lives in the repo.

## Authoring guidance

- **Feedback memories**: capture surprising or non-obvious user preferences. Skip the obvious. Tag with `metadata: type: feedback`.
- **ADRs**: capture decisions with consequences. Skip trivial choices. Include context, options, decision, consequences. Follow the template structure.
- **Re-entry briefs**: keep them current. Update at the end of each meaningful work session on the project. Future-you reads these to resume; stale = useless.

The decision tree for "should this be a memory?" is in [`../reference/taxonomy.md`](../reference/taxonomy.md) and in `/AICONFIG.md` `## CANONICAL SOURCE NOTE`.

---

[End of Document]
