# AGENTS.md — AI Agent Entry Point

**This file is a vendor-agnostic entry point for AI coding agents (Codex, Cursor, Aider, others reading AGENTS.md as a cross-vendor convention).**

For all behavioral standards, conventions, and project context, read [`/AICONFIG.md`](AICONFIG.md).

For the operating-system specification covering hub-and-spoke architecture, vendor-agnostic principles, and the implementation layer at `operations/cwos/`, see [`/CWOS.md`](CWOS.md).

For setting up this architecture in a new repository, see [`/CWOS-SETUP.md`](CWOS-SETUP.md).

For repo overview and primary navigational artifact, see [`/README.md`](README.md).

---

**On entry-point file proliferation:** this repository follows the layered configuration pattern:

- **AICONFIG.md** is the canonical, vendor-agnostic operational charter. All behavioral rules live there.
- **CLAUDE.md** is Claude Code's native entry point (thin pointer to AICONFIG.md).
- **AGENTS.md** (this file) is the cross-vendor entry point for tools that read AGENTS.md (thin pointer to AICONFIG.md).
- Future vendor entry points (`.cursorrules`, `.windsurfrules`, etc.) follow the same thin-pointer pattern when added.

The thin-pointer pattern keeps content single-source. Vendor entry points provide tool-specific discoverability; AICONFIG.md provides the actual rules.

---

**Template note:** this file is generic. When deployed in a target repo, the links above resolve to that repo's actual files. If a target repo doesn't yet have `AICONFIG.md` or `CWOS.md` at the expected paths, see [`CWOS-SETUP.md`](CWOS-SETUP.md) for the bootstrap procedure.

[End of Document]
