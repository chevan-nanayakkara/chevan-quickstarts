# Conventional Commits Reference

**Purpose:** quick reference for the Conventional Commits convention used in this repository.

**External source:** the Conventional Commits spec lives at `conventionalcommits.org`. This file is a working reference for how we use it here.

---

## Format

```
<type>(<scope>): <description>

[optional body]

[optional footer]
```

## Types

- **feat** — a new feature or capability
- **fix** — a bug fix
- **docs** — documentation only changes
- **chore** — maintenance work (dependency bumps, gitignore tweaks, cleanups)
- **refactor** — code change that neither fixes a bug nor adds a feature
- **style** — formatting, white-space, etc. (not code changes)
- **test** — adding or correcting tests
- **perf** — performance improvements
- **build** — build system or dependency changes
- **ci** — CI configuration changes
- **revert** — reverting a previous commit

## Scope

Optional but recommended. Indicates the area of the codebase:

- `feat(slcdems): ...` — scoped to slcdems work
- `docs(cwos): ...` — CWOS docs
- `docs(operations): ...` — operations folder docs
- `chore(config): ...` — repo configuration
- `refactor(aiconversations): ...` — aiconversations restructure

## How this repo enforces it

Required when the user asks for "conventional commit messages" (verbatim or close paraphrase). The AI uses Conventional Commits format and ignores any older `MAJOR:` / `MINOR:` patterns from this repo's pre-2026 history.

Codified in [`/AICONFIG.md`](../../AICONFIG.md) `## CORE INSTRUCTIONS` → "When I Ask for Git Work" → bullet on commit message format.

## Commit message body conventions for this repo

- Subject line under 70 characters when practical
- Body wraps at ~72-80 characters, explains the *why* not just the *what*
- Co-author trailer included for AI-assisted commits:

```
Co-Authored-By: AI Assistant <noreply@anthropic.com>
```

(Or `Co-Authored-By: Chevan Nanayakkara` for user-authored commits.)

## Breaking changes

For breaking changes, append `!` after type or scope:

- `feat!: ...` — breaking feature change
- `refactor(api)!: ...` — breaking refactor scoped to api

And/or include a `BREAKING CHANGE:` footer with details.

This repo rarely has breaking changes in the strict semver sense (mostly documentation), but the convention is available.

## Examples from this repo's history

- `docs(operations/cwos): add CWOS specification (Conversational Work Operating System)`
- `feat(slcdems): add tech-committee folder with Google Drive mirror`
- `chore(slcdems): regenerate drive-mirror docx files and update scripts`
- `refactor(cwos): move CWOS.md to repo root and add AGENTS.md thin pointer`

---

[End of Document]
