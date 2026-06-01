# CLAUDE.template.md — Claude Code Bootstrap Template

> **Template note:** copy this file to `/CLAUDE.md` at the root of your target repo when bootstrapping CWOS. Replace `{{REPO_NAME}}` with the target repo's name. Bump `Version:` and `Last Updated:` as you make changes. Keep the file short — it's a thin pointer, not a content store.

---

**Read and apply /AICONFIG.md for all behavioral standards, conventions, and project context.**

# Claude Code Configuration — {{REPO_NAME}}

Repository: {{REPO_NAME}}

Version: 1.0.0

Last Updated: {{DATE}}

---

## What this file is

This is a Claude Code-specific thin pointer. Claude Code reads it automatically at session start; it just redirects to the canonical operational charter at [`/AICONFIG.md`](AICONFIG.md).

All behavioral rules, writing standards, file conventions, git workflows, and project context live in `/AICONFIG.md` (vendor-agnostic). This file exists because Claude Code expects to find `CLAUDE.md` at repo root.

For the operating-system specification (CWOS), see [`/CWOS.md`](CWOS.md).

For non-Claude vendors (Codex, Cursor, Aider, others reading AGENTS.md), see [`/AGENTS.md`](AGENTS.md).

---

## 📋 REFERENCE

### Changes Log

**Recent updates:**

- {{DATE}} (v1.0.0): **MINOR: Initial CWOS bootstrap.** Repository now uses the Conversational Work Operating System (CWOS) architecture. Canonical spec at `/CWOS.md`, vendor pointers (`CLAUDE.md`, `AGENTS.md`) thin-redirect to `/AICONFIG.md`. Implementation layer at `/operations/cwos/` (memory, skills, reference). See `CWOS-SETUP.md` for the bootstrap procedure that produced this state.

(Add subsequent version entries above this one in descending date order.)

---

[End of Document]
