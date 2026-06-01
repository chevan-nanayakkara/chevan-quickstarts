# Agent Skills Standard Reference

**Purpose:** quick reference for the Agent Skills format used in `operations/cwos/skills/`. Provides format basics and authoring notes; defers to the authoritative external spec for full details.

**External source:** the Agent Skills standard was published by Anthropic in December 2025 and is now adopted by 30+ AI coding tools (Claude Code, Codex, Cursor, VS Code, Gemini CLI, JetBrains Junie, AWS Kiro, Block Goose, and others). The spec lives at the published source; this file is a working reference for how we use it here.

---

## Format basics

A skill is a directory containing at minimum `SKILL.md`:

```
my-skill/
├── SKILL.md          # required: frontmatter + procedure body
├── scripts/          # optional: executable code
├── references/       # optional: documentation
└── assets/           # optional: templates
```

SKILL.md structure:

```markdown
---
name: skill-name
description: Specific description of when this skill applies.
---

# Skill Name

Procedure body. Markdown formatting.
```

## Required frontmatter

- **`name`** — identifier. Used in invocation. Kebab-case convention.
- **`description`** — what determines whether AI invokes the skill. Should be specific enough that another AI tool reading only this description (not the body) can decide relevance.

## Optional frontmatter (not required by spec)

- `version` — if you version skills internally
- `dependencies` — if a skill depends on other skills
- `applies-to` — context restriction (e.g., "only when working in a Python project")

Keep frontmatter minimal. Required fields plus what genuinely affects invocation.

## How invocation works

At session start, AI tools read every available SKILL.md frontmatter (name + description). The procedure body is NOT loaded — only the metadata. When a task matches a skill's description, the AI calls the skill (via vendor-specific tool — Claude Code's `Skill` tool, Cursor's equivalent, etc.), which loads the full body into context for that turn.

This is the "metadata index always-loaded, full content on-demand" pattern. Scales well because many skills cost only metadata at session start.

## Authoring guidance

- **Specific descriptions.** "Scan content for AI watermark phrases" is specific. "Help with writing" is too generic — would never reliably invoke.
- **One skill per procedure.** Don't bundle related procedures into one skill; split them. Each gets its own description.
- **Reference don't duplicate.** If the skill enforces rules from AICONFIG.md, reference the section, don't copy the content. Single source.
- **Test invocation.** After authoring, give the AI a task that should trigger the skill. If it doesn't fire, the description likely needs to be more specific.

## Authoring in this repo

- Location: `operations/cwos/skills/<name>/SKILL.md`
- Template: `operations/cwos/skills/_template/SKILL.md`
- Index: `operations/cwos/skills/README.md`

When you create a new skill, update the skills index in the README so humans can find it. AI tools find skills via the metadata index automatically.

## Cross-vendor portability

The Agent Skills standard is vendor-neutral. A SKILL.md authored here works in Claude Code, Codex, Cursor, etc. (assuming the tool supports the standard).

Avoid vendor-specific extensions in your skill body (e.g., Claude-only tool calls, vendor-specific paths). When vendor-specific behavior is needed, branch the skill body conditionally or maintain separate vendor variants.

## Security considerations

A 2026 study found 36% of skills on major registries contain security flaws. Skills run with shell access and filesystem permissions. Audit skills before installing from third-party sources. Skills you author yourself: same scrutiny — what shell commands will this run, what files will it touch, what could go wrong.

---

[End of Document]
