# PR workflow

How to turn an edit into a pull request against a spec repository. The
repository's own conventions (branch naming, PR body format, reviewers, base
branch) live in `.respec-skill.md` and override the generic guidance here.

## Confirmation gate (important)

Creating a branch remotely, pushing, opening a PR, committing, and merging are
irreversible side effects. Prepare everything locally and **show the user the
diff and the proposed PR title/body first.** Only run the irreversible git/PR
commands after the user explicitly confirms. Never merge.

## Preparing the change

1. Confirm the base branch from `.respec-skill.md` (often `main` or `gh-pages`;
   many spec repos publish the editor's draft from a specific branch — do not
   assume).
2. Create a feature branch using the repository's naming convention. If none is
   specified, a neutral default is `respec/<short-topic>`.
3. Make the edits per the editing workflow in SKILL.md. Keep the change focused;
   one logical change per PR reads better for spec review.
4. Build the diff and review it for the scope violations listed in SKILL.md —
   no changed `id`s, no hand-written auto-generated content, no unintended
   `respecConfig` metadata changes, normative status unchanged unless requested.

## Showing the diff

Present the diff (e.g. `git diff` output) to the user before any commit. Call
out explicitly:

- Which sections/terms changed.
- Any new `data-cite` keys introduced and whether they were already declared.
- Any `localBiblio` / config change (these deserve maintainer attention).
- Confirmation that no auto-generated artifact was edited.

## Writing the PR

Draft the PR using the repository's template from `.respec-skill.md` if it has
one. A generic structure when none is given:

- **Title**: concise, imperative, scoped to the change (e.g.
  "Add issuance-flow section and define issuer").
- **Body**: what changed and why; link to the design discussion if relevant;
  note any new external references added; note explicitly what was intentionally
  *not* changed if that prevents reviewer confusion.

## Executing (only after confirmation)

Once the user confirms, the irreversible steps may proceed in line with the
permission model: push the branch and open the PR. Do not enable auto-merge, do
not merge, and do not change repository settings or permissions.
