# Changelog

Umbrella-level structural change log. Each inhabitant maintains its own change log inside its subdirectory.

Format: [Keep a Changelog](https://keepachangelog.com/en/1.1.0/) loosely followed; versioning [SemVer](https://semver.org).

---

## [2.0.0] — 2026-06-01

### Changed

**Repurposed from `chevan-claude-code-quickstart` / `chevan-ai-quickstart` into `chevan-quickstarts`** — an umbrella for public-domain setup, configuration, and distribution starters. Hard structural break from the v1.x Claude-Code-quickstart content.

### Added

- **`cwos/`** — first inhabitant: Conversational Work Operating System starter. Vendor-agnostic architecture for AI-augmented work. Canonical spec, bootstrap guide, AGENTS / AICONFIG / CLAUDE templates, four universal Agent Skills (`run-prompt-protocol`, `conversation-archiving`, `cwos-migrate-from-conversational-work`, `_template`), four universal reference docs (taxonomy, conventional-commits, agent-skills-standard, mcp-stack), memory templates. See [`cwos/README.md`](cwos/README.md).
- **Umbrella `README.md`** — purpose, inhabitants index, three consumption patterns, status.
- **`CONTRIBUTING.md`** — how to suggest improvements, upstream-relationship note.
- **`CHANGELOG.md`** (this file) — structural change log.

### Removed

- All v1.x Claude-Code-quickstart content (5 guides, claude/shell/skills templates). The Feb 2026 content was dated and tied to a narrower "set up Claude Code well" scope that's superseded by the broader CWOS architecture.

### Migration notes

If you referenced `v1.0.0` of this repo for Claude Code setup content, that content remains accessible via the `v1.0.0` tag or via the git history at the deleted-files commits. The new `v2.0.0` is a structural break with no backward compatibility.

GitHub auto-redirects from the old URLs (`chevan-claude-code-quickstart`, `chevan-ai-quickstart`) to `chevan-quickstarts`, so existing references resolve correctly.

---

## [1.0.0] — 2026-02-01

Initial release as `chevan-claude-code-quickstart` (later renamed `chevan-ai-quickstart`). Claude Code setup guides and templates. Superseded by v2.0.0.
