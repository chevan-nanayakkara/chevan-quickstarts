# operations/cwos/skills/ — Universal CWOS Skills

Agent Skills (per Anthropic's December 2025 standard) for the CWOS architecture. Each skill is a directory containing a `SKILL.md` with YAML frontmatter (`name`, `description`) and a procedural body.

Universal skills only — these apply to any CWOS-aligned repo regardless of its content domain. Repo-specific skills (essay publishing, dashboard refresh schemas, etc.) belong in the target repo's own `operations/cwos/skills/` directory, not here.

For the Agent Skills format spec, see [`../reference/agent-skills-standard.md`](../reference/agent-skills-standard.md).

---

## Skills in this starter

- **[`run-prompt-protocol/`](run-prompt-protocol/SKILL.md)** — Execute a "run prompt" instruction by reading a specified prompt at a timestamp in a conversation file, executing the instruction, then writing the response back as an `[AI Assistant | TIMESTAMP]` entry plus a `[User | +2min]` stub. The core run-prompt workflow used in any repo with the `aiconversations/` extension.
- **[`conversation-archiving/`](conversation-archiving/SKILL.md)** — Archive content from a conversation file that has exceeded the size threshold. Use when a conversation file is over 75KB and needs to be split into historical archive chunks while preserving an active working file under threshold.
- **[`cwos-migrate-from-conversational-work/`](cwos-migrate-from-conversational-work/SKILL.md)** — Migrate a repository from the older `operations/conversational-work/` infrastructure to the CWOS architecture. Clean-break approach: build `operations/cwos/` fresh, mark predecessor LEGACY. Three-session structure (Foundation / Skills + reference / Migration + cleanup).
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
