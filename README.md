# chevan-quickstarts

Public-domain umbrella for setup, configuration, and distribution starters. No IP restrictions, no commercial claims. Use freely.

Each inhabitant of this repo is a self-contained starter for a specific tool, framework, or operating system. Pick the one you need; ignore the rest.

---

## Inhabitants

### [`cwos/`](cwos/) — Conversational Work Operating System starter

A vendor-agnostic architecture for AI-augmented work. Layered configuration / state / procedure split so any AI tool (Claude Code, Codex, Cursor, Aider) can plug into the same repo and operate consistently.

**New to CWOS?** Read [`cwos/WHY-CWOS.md`](cwos/WHY-CWOS.md) for the narrative intro: what CWOS is, the friction it solves, the approach, and why it's free.

Use when: setting up a new repo for AI-augmented reasoning, content production, or project management. Works as a hub repo, a single-repo, or a spoke; covered scenarios in [`cwos/CWOS-SETUP.md`](cwos/CWOS-SETUP.md).

For an existing repo with the older `operations/conversational-work/` predecessor infrastructure, see [`cwos/operations/cwos/skills/cwos-migrate-from-conversational-work/SKILL.md`](cwos/operations/cwos/skills/cwos-migrate-from-conversational-work/SKILL.md) for the clean-break migration recipe.

---

## How to consume an inhabitant

Three patterns, pick the one that fits your situation:

### 1. Raw GitHub URL fetches (one-off, simplest)

Pull individual files directly into your target repo. No clone needed. Works because this repo is public.

```bash
RAW="https://raw.githubusercontent.com/chevan-nanayakkara/chevan-quickstarts/main"
curl -s "$RAW/cwos/CWOS.md" -o CWOS.md
# ... etc. for each file you need
```

Best for: one-off installs where you don't want a local clone.

### 2. Sparse checkout (single inhabitant only)

Clone just the subdirectory you care about:

```bash
git clone --filter=blob:none --no-checkout https://github.com/chevan-nanayakkara/chevan-quickstarts.git ~/git/chevan-quickstarts
cd ~/git/chevan-quickstarts
git sparse-checkout init
git sparse-checkout set cwos
git checkout main
```

Best for: persistent local copy of one inhabitant without the rest of the repo.

### 3. Full clone (multi-use)

```bash
git clone git@github.com:chevan-nanayakkara/chevan-quickstarts.git ~/git/chevan-quickstarts
```

Best for: regular consumers who reference the umbrella across multiple target repos or multiple sessions.

---

## License

Unlicense. All content in this repo is public domain — copy, modify, redistribute freely, with or without attribution. See [LICENSE](LICENSE).

The Unlicense applies to the content of this repo. Tools and standards this repo references (Claude Code, Anthropic Agent Skills standard, Conventional Commits, etc.) have their own licenses; consult those projects for their terms.

---

## Status and maintenance

This umbrella is maintained by [Chevan Nanayakkara](https://github.com/chevan-nanayakkara) for personal use. Public visibility is incidental — if any of it helps you, take it.

Improvements and corrections are welcome via PR or issue. See [CONTRIBUTING.md](CONTRIBUTING.md).

For the structural change log of the umbrella itself, see [CHANGELOG.md](CHANGELOG.md). Each inhabitant has its own change log inside its subdirectory.

---

## Future inhabitants

Things that might land here over time, not promises:

- Vendor-specific quickstarts (Cursor, Codex, Aider configuration starters)
- Dotfiles / shell-config templates
- Generic agent-skills library (skills not tied to CWOS)
- Other AI-adjacent setup patterns that earn their place

Inhabitants get added when they're battle-tested enough to be worth sharing, not speculatively.
