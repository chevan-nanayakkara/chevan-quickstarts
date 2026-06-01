---
name: frontmatter-validate
description: Validate that every structural pointer in every conversation file's YAML frontmatter (companionTo, archives, parentConversation, subConversations, relatedDocuments, location, companionTasks) resolves to an existing path on disk. Reports dangling references file-by-file without auto-fixing. Use when the user asks to validate frontmatter pointers, audit the conversation graph, run a frontmatter sweep, or check for stale pointers after a folder rename or restructure.
---

# Frontmatter Validate

Walk every conversation file in `aiconversations/`, parse its YAML frontmatter, check each structural pointer against the filesystem, and produce a per-file report of dangling references. Pure detection: this skill does not modify files. It surfaces the maintenance work; the user (or a follow-up skill / pass) applies the fixes.

This skill exists because the CWOS architecture treats frontmatter as the canonical navigation graph (see your repo's CWOS migration ADR, typically at `operations/cwos/memory/decisions/001-*.md`). Drift accumulates whenever folders are renamed, files relocate, or conversations get archived. Without periodic validation, drift becomes invisible until a fresh AI session (cold-start, cross-vendor, programmatic traversal) hits a dangling pointer and either reports it or hallucinates context.

## When to invoke

- After any non-trivial folder rename, file relocation, or conversation deletion
- Quarterly per the CWOS periodic review schedule (see `CWOS.md` "Periodic review")
- When the user asks to "validate frontmatter," "audit pointer staleness," "check conversation graph coherence," or anything matching that semantic
- Before a major migration where frontmatter correctness is a prerequisite (e.g., before importing the hub into a new vendor's tooling)

## When NOT to invoke

- Mid-conversation as a normal interactive task — running this skill is operational maintenance, not session-level work
- When the user is asking about a single specific pointer (just check that one directly)
- When the user wants to *fix* pointers, not just detect them — this skill only detects; apply fixes via the 4-rule maintenance pattern (Rule A: cross-system path migrations; Rule B: root-folder relocations; Rule C: stale archive folders; Rule D: sibling renames + dangling drops). The reference worked example is in the workspace-chevan repo's operations conversation file (entries dated 2026-05-31 evening) where the 4-rule pattern was applied to ~30 files; that's the canonical worked example for the maintenance pattern this skill detects.

## Inputs

Optional `$ARGUMENTS`:

- A specific subfolder path (e.g., `aiconversations/<folder>/`) to limit the scan
- `--include-tasks` flag to also scan `-tasks.md` files (default: scan only `-conversation.md` files plus `_system/` and the root-level `0-*.md` files)

If no arguments, scan every `*.md` file under `aiconversations/` except the `archives/` subtree (archives are historical snapshots — their frontmatter is intentionally frozen).

## Structural fields to validate

For each conversation file's YAML frontmatter, check these fields when present:

- `companionTo` — single path; must point at an existing file or directory
- `companionTasks` — single path (or YAML list); each must exist
- `parentConversation` — single path; must exist
- `subConversations` — YAML list of paths; each must exist
- `relatedDocuments` — YAML list of paths; each must exist
- `relatedResearch` — YAML list of paths; each must exist
- `archives` — single path (directory); must exist
- `previousConversation` — single path; must exist
- `location` — single path; must match the actual file path (this is a self-reference; useful for catching files where the location field wasn't updated after a rename)
- `clientFolder` — single path; must exist (empty scaffold directories are acceptable; a missing directory is not)
- `companionRepo` / `companionPath` — if the repo uses the cross-repo pointer convention (Option α; see your AICONFIG.md), validate by combining the spoke-registry's absolute path with the relative `companionPath` and checking that the result exists

Fields that are NOT structural pointers and should be skipped: `documentType`, `version`, `versionNote`, `lastUpdated`, `status`, `purpose`, `archivesNote`, `owner`, `conversationType`, `conversationScope`, `systemFileNote`, `companionDocuments` (this one is a description of related content, not a single resolvable target), `audience`, `tags`.

If a field is present but its value is `null` or empty, it's not dangling — it's an intentional empty state. Skip those.

## Procedure

1. **Identify scan scope** based on `$ARGUMENTS`. Default: `aiconversations/**/*.md` excluding `aiconversations/_system/archives/**` and excluding `*.md` files inside `aiconversations/archives/**` if that path exists.

2. **For each file in scope:**

   a. Read the file. Extract the YAML frontmatter block (everything between the leading `---` and the next `---` on its own line at the start of the file).

   b. Parse the YAML. If the file has no frontmatter, skip it with a one-line note "no frontmatter — skipped."

   c. For each structural field listed in the "Structural fields to validate" section above, if the field is present and non-null:

      - If the value is a single path: check that the path resolves (file exists for file paths; directory exists for directory paths). Path resolution rule: if the path starts with `/`, treat as repo-root-relative; otherwise treat as relative to the conversation file's directory.
      - If the value is a YAML list: iterate each item; check each.
      - If the field is `location:`, additionally check that the path matches the file's actual location.
      - If the field is `companionRepo:` + `companionPath:`, resolve via the spoke-registry and check the combined path exists.

   d. Collect dangling references — record `<field>: <value>` for each one.

3. **Produce the report.**

   - Top of report: total files scanned, total dangling references, breakdown by field type.
   - Body of report: per file (only files with at least one dangling ref), list each dangling pointer with its field name. Group files by directory so the operator can scan domain-by-domain.
   - Footer: recommended next actions — apply the 4-rule maintenance pattern (Rule A: cross-system path migrations; Rule B: root-folder relocations; Rule C: stale archive folders; Rule D: sibling renames + dangling drops).

   Do not write the report to disk by default. Print it back to the AI session for the operator to review. If `$ARGUMENTS` contains `--write-report`, also write to `working/.ai.working.frontmatter-validate.{YYYY-MM-DD-HHMM}/report.md` for archival.

4. **Stop.** This skill does not apply fixes. Direct the operator to do one of:

   - Manual sweep using the 4-rule pattern
   - Invoke `post-restructure-sweep` skill (if authored later) to handle a specific set of source→target mappings
   - Accept some dangling refs as intentional (e.g., body-text references to content that stayed in a spoke; these would only appear if `--include-bodies` is enabled, which is out of scope for this skill)

## Output format

```
# Frontmatter Validation Report

Scanned: <N> files in aiconversations/
Dangling references: 0

✓ All structural pointers resolve.
```

Or:

```
# Frontmatter Validation Report

Scanned: <N> files in aiconversations/
Dangling references: <M>
  companionTo: <count>
  archives: <count>
  parentConversation: <count>
  subConversations: <count>
  location: <count>

## aiconversations/<folder>/

### <conversation-name>-conversation.md
  companionTo: <stale-path>
  archives: <stale-path>

... (continue per folder)

## Recommended next actions

Apply the 4-rule maintenance pattern (see your repo's CWOS migration ADR
and the maintenance pass record at workspace-chevan's operations conversation
file as the canonical worked example):

- Rule A: cross-system path migrations (e.g., spoke-originated paths → in-hub equivalents)
- Rule B: root folder relocations (e.g., /old-root/ → new location after a restructure)
- Rule C: stale archives — repoint to era-folder location or drop the dangling field
- Rule D: sibling renames, dangling parentConversations, internal location fixes
```

## Don't do

- **Don't modify files.** This is a detection-only skill. Modifications happen via the manual maintenance pattern or a separate `post-restructure-sweep` skill.
- **Don't fail silently if YAML parsing breaks.** A malformed YAML block is itself a finding — report it as `parse error: <reason>` for that file rather than skipping or aborting.
- **Don't validate body-text references.** Body sweeps are a separate, larger-scope task with different rules (frontmatter must always resolve; body text can validly retain historical references). Frontmatter is what CWOS treats as authoritative.
- **Don't auto-archive or auto-prune files** based on findings. That's a separate decision.

## Common adaptations

- **Run before a planned restructure** to capture baseline state, then re-run after to see what the restructure broke. The diff is the work list.
- **Scope to a subfolder** when working in a specific domain — useful for targeted audits.
- **Include `-tasks.md` files** via `--include-tasks` when validating a domain where companion tasks files are heavy on cross-references.

## References

- Your repo's CWOS migration ADR at `operations/cwos/memory/decisions/001-*.md` — the architectural decision; explains why frontmatter is canonical
- `operations/cwos/reference/taxonomy.md` — CWOS vocabulary including the "navigation graph" concept
- Companion skill: `conversational-maintenance-review` — the orchestrator that invokes this skill as Phase 1 of a periodic review

---

[End of Skill]
