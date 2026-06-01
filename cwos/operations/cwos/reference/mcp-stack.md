# MCP Server Reference

**Purpose:** documentation of Model Context Protocol (MCP) server usage in this repository.

**Status:** placeholder. No MCP servers configured here yet. This file is the reference doc for when configuration begins.

For full Agent Skills format details, MCP server protocols, and current vendor support, see external authoritative sources (Anthropic MCP spec, vendor-specific docs).

---

## What MCP is

Model Context Protocol (MCP) standardizes how AI agents connect to external tools and data sources. MCP servers run locally or proxy to user-controlled services. Compatible with the sovereignty principle (state stays in user-controlled systems, not vendor platforms).

Vendor support: Claude Code, Cursor, Codex, others increasingly. Each vendor reads `.mcp.json` (project-level) or equivalent for configured servers.

## Where `.mcp.json` lives

- **Project-level:** `.mcp.json` in repo root. Applies to AI sessions in this repo.
- **User-level / global:** vendor-specific (e.g., `~/.claude.json` for Claude Code). Applies across projects on this machine.
- **Plugin-bundled:** if a plugin provides MCP server configs.

This repo currently has no `.mcp.json` at the root. When you add one, document the included servers below.

## Common MCP server types

- **Filesystem MCP** — structured file access beyond built-in capabilities
- **Git MCP** — git operations as tool calls; more reliable for complex git workflows
- **Sequential thinking MCP** — structured reasoning scratchpad
- **Database MCPs** (Postgres, SQLite, etc.) — query and inspect databases directly
- **GitHub/GitLab MCP** — issue tracking, PR management, repo operations
- **Linear / Notion / Obsidian MCP** — connect to existing knowledge or task tools
- **Browser/Playwright MCP** — browser automation
- **Vendor service MCPs** — Google Workspace (Drive, Calendar, Gmail), Microsoft 365, etc.

## Vendor-specific notes

### Google connectors (Claude AI web/app)

Claude AI (anthropic.com web/app) supports Google Workspace connectors (Drive, Calendar, Gmail) via OAuth in the user's profile settings. These are managed by Anthropic, not configured per-repo. Useful for Claude AI conversations but not directly read by Claude Code sessions.

### Claude Code MCP

Claude Code reads `.mcp.json` at project root for project-level MCP servers, plus `~/.claude.json` (or equivalent) for user-level. The plugin marketplace can also bundle MCP server configs.

To install: in a Claude Code session, run `/plugins` for a UI, or edit `.mcp.json` directly.

### Microsoft (future)

If/when Microsoft 365 connectors become available via MCP, document setup specifics here. As of mid-2026, less established than Google connectors.

### Codex / Cursor / others

Each vendor reads MCP configurations via their own mechanism. Generally `.mcp.json` at project root works, but check vendor-specific docs.

## Sovereignty checklist for MCP servers

Before adopting an MCP server:

1. Does the server keep state local (or in user-controlled storage)?
2. Does it require cloud auth or send data through vendor-managed services?
3. If the MCP server's vendor disappears, does workflow continue?

Servers that pass these tests align with the CWOS sovereignty principle. Servers that fail can still be useful but should be evaluated against the value they provide.

## Authoring updates to this file

When MCP servers get configured in this repo:

- Add to the "Active MCP servers" section (to be created)
- Document the server's purpose, installation, configuration
- Note sovereignty considerations
- Update the sovereignty checklist if the server passes/fails specific criteria

---

[End of Document]
