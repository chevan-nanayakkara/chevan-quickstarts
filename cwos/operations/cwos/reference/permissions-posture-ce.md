---
documentType: Reference
title: C+E Permissions Posture (recommended starting point for CWOS repos)
purpose: |
  Document the C+E permissions posture as a recommended starting shape for `.claude/settings.local.json`
  in CWOS-aligned repos. Three-layer model — auto-allow read tools + Edit; ask gates Write and mutating
  Bash; hard-deny destructive Bash. Codified in workspace-chevan June 14, 2026; ported to chevan-content
  and chevan-quickstarts June 16, 2026.
status: Active
updated: 2026-06-17
---

# C+E Permissions Posture

A recommended starting shape for `.claude/settings.local.json` in CWOS-aligned repos. Three layers — auto-allow / ask / deny — designed to remove file-edit friction while keeping git mutations and destructive Bash gated or denied.

## Background

The "C" in C+E refers to **auto-allowing `Read`, `Grep`, `Glob`, `LS`, and read-only Bash** plus **`Edit` and `MultiEdit` (in-place file editing)**. The "E" refers to **explicitly hard-denying destructive Bash patterns** (`rm`, `git push --force*`, `git clean*`, `git reset --hard`, etc.) as a hard backstop independent of the prompt approval flow.

The posture emerged after a March 2026 incident where Claude bulk-deleted files via `rm` in a repo with permissive `Bash(*)` allow rules. The strict-everywhere response that followed (gating Edit + Write everywhere) was friction without addressing the actual risk surface, which is destructive Bash. C+E loosens file editing back to auto-fire while explicitly closing the door on the patterns that caused the original incident.

## Three-layer shape

### Auto-allow (fires without prompting)

Tool-level:

- `Read`, `Grep`, `Glob`, `LS`
- `Edit`, `MultiEdit`
- `WebSearch`

Read-only Bash:

- Git read-only: `git status:*`, `git log:*`, `git diff:*`, `git branch:*`, `git show:*`, `git remote:*`, `git stash list:*`, `git rev-parse:*`, `git ls-files:*`
- Shell read-only: `ls:*`, `cat:*`, `head:*`, `tail:*`, `find:*`, `grep:*`, `awk:*`, `sed:*`, `wc:*`, `sort:*`, `uniq:*`, `diff:*`, `file:*`, `date:*`, `md5:*` (or `md5sum:*`), `du:*`, `pwd`, `which:*`, `printf:*`, `test:*`, `true`, `false`, `cd:*`, `echo:*`
- `gh` read-only: `gh pr view:*`, `gh pr list:*`, `gh pr diff:*`, `gh pr checks:*`, `gh issue view:*`, `gh issue list:*`, `gh repo view:*`, `gh api repos*`, `gh auth status:*`

### Ask (gated — prompts the operator)

Tool-level:

- `Write`, `NotebookEdit`, `WebFetch`

Mutating git:

- `git add:*`, `git commit:*`, `git push:*`, `git pull:*`, `git fetch:*`
- `git checkout:*`, `git switch:*`, `git merge:*`, `git rebase:*`
- `git restore:*`, `git reset:*` (the non-`--hard` variants are gated; `--hard` is denied — see below)
- `git tag:*`, `git mv:*`, `git rm:*`, `git stash:*`, `git config:*`

Mutating `gh`:

- `gh pr create:*`, `gh pr merge:*`, `gh pr edit:*`, `gh pr close:*`, `gh pr reopen:*`, `gh pr comment:*`, `gh pr review:*`
- `gh issue create:*`, `gh issue edit:*`, `gh issue close:*`
- `gh release:*`

Filesystem mutations:

- `mv:*`, `cp:*`, `mkdir:*`, `rmdir:*`, `touch:*`, `chmod:*`, `chown:*`, `ln:*`

Package + network:

- `npm:*`, `pnpm:*`, `yarn:*`, `brew:*`, `curl:*`, `wget:*`

### Hard-deny (refused at the tool layer even on operator approval)

- All `rm` shapes: `rm:*`, `rm -r:*`, `rm -rf:*`, `rm -fr:*`, `rm -f:*`
- All `git push --force*`: `git push --force:*`, `git push -f:*`, `git push --force-with-lease:*`
- All `git clean*`: `git clean:*`, `git clean -f:*`, `git clean -fd:*`, `git clean -fdx:*`
- `git reset --hard:*`
- `git branch -D:*`
- `git checkout --:*`
- `git restore --staged:*`
- `git filter-branch:*`
- `git filter-repo:*`
- `git update-ref -d:*`
- `sudo:*`

The deny list is the "E" in C+E. Its purpose is to close the prompt-fatigue path: even if the operator taps "allow" on a destructive prompt while tired, the rule refuses outright. Legitimate one-off destructive operations are intentionally inconvenient: edit `settings.local.json` to temporarily soften, run the command, revert; or run the command in the terminal outside Claude.

## Per-repo customization

The three layers above are the shared shape across CWOS repos. Per-repo allow-list additions are appropriate and expected:

- A repo that runs Python content scripts may add `Bash(python3:*)` to allow.
- A repo with publication mirroring may add `WebFetch(domain:example.com)` rules.
- A repo with starter README structure displays may add `Bash(tree:*)`.
- A repo with keychain interactions may add narrow `Bash(security ...:*)` patterns.

Per-repo *ask + deny* customizations are rare and should be deliberate. The ask/deny lists are the safety floor; treat them as a shared standard.

## What NOT to do

- **Don't re-add `rm` to allow or ask.** The hard-deny is the entire reason this posture exists. If a legitimate `rm` is needed, edit the settings file or run outside Claude.
- **Don't re-add mutating git (`commit`/`push`/`add`/`checkout`/etc.) to the allow list.** The standing rule across CWOS-aligned repos is "commit / push only on explicit per-batch operator authorization." Auto-allowing breaks this.
- **Don't propose an OS / hooks integration as the enforcement mechanism.** Per `feedback_no_os_integration_in_ai_rules` in workspace-chevan, behavioral rules belong in AICONFIG + memory, not in `settings.json` hooks or other harness-level enforcement. The C+E posture is the *one* layer that's enforced at the tool-call layer, and that's deliberate — it's about closing the destructive-Bash path, not about general behavior enforcement.

## Verification probes after activation

When this posture is freshly installed in a repo, verify with these probes on first session restart:

- `Bash(date)` — should fire without prompt.
- `Bash(gh pr list)` — should fire without prompt.
- An `Edit` to any markdown file — should fire without prompt.
- `Bash(rm /tmp/nonexistent-test-file)` — should be hard-denied at the tool layer: *"Permission to use Bash with command rm ... has been denied."*
- A `Bash(git commit -m "...")` — should prompt the operator for approval.

If any of the five behaves differently than expected, the JSON pattern in `settings.local.json` needs adjustment.

## Cross-repo source of truth

The canonical full description and rationale live in workspace-chevan's `operations/cwos/memory/feedback_oversight_no_runaway_automation.md`. Memory files in adopting repos (chevan-content's `feedback_permissions_posture_ce.md`) are the local pointers and per-repo customization notes. Update both this reference doc and the memory files when the shared shape evolves.
