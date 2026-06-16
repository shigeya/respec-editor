# Repository-specific ReSpec context

<!--
  This file is the REPOSITORY-SPECIFIC layer for the respec-editor skill.
  Keep it OUTSIDE the upstream repo's tracked files: store the real file in a
  central location (e.g. ~/respec-skill-configs/<spec-name>/respec-context.md)
  and symlink it into the repo root as `.respec-skill.md`, git-ignored via
  .git/info/exclude. Fill in every section below for this specific document.
-->

## Document identity

- Spec name / shortName:
- Repository (org/repo):
- Main spec file path (e.g. `index.html`):
- Lineage: <!-- W3C REC-track / WG note / Community Group draft / WICG / independent -->
- ReSpec profile / specStatus in use:

## Base branch and branch naming

- Base branch for PRs (e.g. `main`, `gh-pages`):
- Branch naming convention (e.g. `respec/<topic>`, `feature/<issue>`):

## External references (`data-cite` keys)

<!--
  Inventory the document's existing data-cite targets:
    grep -oE 'data-cite="[^"]+"' index.html | sort -u
  List each external spec, the key used, and whether normative.
-->

| Key | External spec | Normative? | Notes |
|-----|---------------|-----------|-------|
|     |               |           |       |

- Are new external references allowed without maintainer sign-off? (yes/no):
- Where new references are declared (`localBiblio` / ReSpec bibliography):

## respecConfig: editable vs locked

- Fields the skill MAY edit for this repo:
- Fields that are strictly locked (default: all metadata — editors, github,
  publishDate, shortName, specStatus, group, license):

## Terminology conventions

- How terms are defined here (`<dfn>` usage, `data-lt` style):
- Naming/casing conventions for defined terms:
- Any terms that must NOT be redefined locally (imported via data-cite instead):

## Normative vs informative

- How this document marks informative content (`class="informative"`, etc.):
- Criteria for what is normative here:
- RFC 2119 keyword convention (wrapped? plain? styled by ReSpec?):

## PR conventions

- PR title format:
- PR body template / required sections:
- Required reviewers / CODEOWNERS to expect:
- CI checks that must pass (link checker, ReSpec lint, etc.):

## Anything else specific to this document

<!-- House style notes, sections that are sensitive, areas under active change. -->
