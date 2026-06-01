# Contributing to chevan-quickstarts

This repo is public-domain. Improvements and corrections are welcome.

## Where canonical content lives

Most inhabitants in this umbrella are derivatives of content authored upstream in the maintainer's working repos.

- **CWOS** (in `cwos/`): canonical source is currently the maintainer's `chevan-content` repo (private). Changes to `cwos/CWOS.md`, `cwos/CWOS-SETUP.md`, the universal skills, and the reference docs flow from chevan-content downstream to this starter. Suggestions for improvement can be filed here; they'll be evaluated upstream and reflected back.

After the CWOS canonical files stabilize (probably after 2-3 more real-world deployments), the source-of-truth may flip to this starter, with chevan-content referencing it as a submodule. Until then, treat the starter as a derivative.

## How to suggest improvements

### File a PR

For typos, broken links, formatting fixes, or small clarifications:

1. Fork the repo
2. Make the change in the appropriate inhabitant subdirectory
3. Open a PR with a clear title (`fix(cwos): typo in CWOS-SETUP.md Section 3` or similar)

The PR will be reviewed; for changes that touch canonical CWOS content, the upstream chevan-content will be updated first and the change propagated here.

### File an issue

For structural suggestions, new inhabitants, or anything bigger than a typo fix:

1. Open an issue describing what you'd change and why
2. The maintainer evaluates and decides whether to do the change, ask for more detail, or decline

## Commit message convention

Conventional Commits format: `<type>(<scope>): <description>`. Types in use:

- `feat`: new content (new inhabitant, new section in an existing inhabitant)
- `fix`: corrections (typos, broken links, factual errors)
- `docs`: documentation-only changes
- `refactor`: structural changes that don't alter content
- `chore`: maintenance (dependency updates, license updates, etc.)

Scope is the inhabitant or umbrella component, e.g., `(cwos)`, `(readme)`.

See [`cwos/operations/cwos/reference/conventional-commits.md`](cwos/operations/cwos/reference/conventional-commits.md) for the full reference.

## Changelog flow

This repo follows a two-level changelog convention so contributors and the maintainer have a clear place to record each kind of change:

- **Umbrella `CHANGELOG.md`** at the repo root tracks structural changes: adding or removing inhabitants, top-level renames, license changes, cross-cutting refactors. Major (`X.0.0`) versions get tagged in git.
- **Per-inhabitant `CHANGELOG.md`** inside each subdirectory (`cwos/CHANGELOG.md`, future inhabitants get their own) tracks changes specific to that starter. Each inhabitant has its own version number, independent of the umbrella.

When you submit a PR, add an entry to the **Unreleased** section at the top of the relevant changelog (create the Unreleased section if it doesn't exist yet). On release, the maintainer rolls Unreleased into the next versioned section and tags accordingly.

If your PR touches both the umbrella and an inhabitant, update both changelogs.

## What this repo is not

- **Not a community project.** This is a personal umbrella. PRs are reviewed when the maintainer has time; no SLA.
- **Not a substitute for tool documentation.** Each inhabitant points at the canonical tool/standard it covers (CWOS spec, Anthropic Agent Skills standard, etc.). Read those for authoritative information.
- **Not a marketing site.** No branding, no commercial framing. If you find it useful, take it; if you don't, no hard feelings.

## License compatibility

All content is contributed under the Unlicense. By submitting a PR, you agree your contribution is also released to the public domain.

If a future contribution introduces content with a different license (e.g., MIT-licensed code from another project), declare it clearly in the PR and we'll evaluate compatibility.
