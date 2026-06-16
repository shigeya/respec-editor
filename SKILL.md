---
name: respec-editor
description: Edit W3C ReSpec-format HTML specification documents and prepare pull requests against them. Use this skill whenever the user wants to reflect a design discussion into a ReSpec spec, add or modify a section, definition, or normative statement in a `.html` ReSpec document, fix cross-references such as data-cite, data-lt, and dfn elements, or produce a diff or PR for a spec repository. Trigger this even when the user only says "spec", "ReSpec", "respecConfig", "W3C draft", "CG/WICG draft", "dfn", or "data-cite" without naming this skill explicitly — ReSpec has many non-obvious authoring constraints, so consult this skill before editing any ReSpec HTML by hand. Do NOT use for non-ReSpec HTML, Bikeshed sources (.bs), or generic Markdown.
---

# ReSpec spec editor

This skill edits W3C ReSpec-format specification documents (ReSpec is the JS
tool that turns annotated HTML into a styled W3C-looking spec at load time) and
prepares pull requests against the repository that holds them.

ReSpec authoring has many constraints that are easy to break silently — broken
cross-references, hand-writing sections that ReSpec generates automatically,
disturbing the normative/informative split. The job of this skill is to make
those edits safely and reproducibly, and to keep repository-specific knowledge
out of this shared skill so the same skill works across many spec repositories.

## Two-layer model (read this first)

This skill is the **shared layer**: it knows ReSpec in general and never
contains knowledge specific to one document or repository.

Each spec repository carries its own **repository-specific layer** in a file at
the repository root named `.respec-skill.md`. That file is typically a symlink
to a centrally-managed config and is git-ignored, so it is never committed to
the upstream repository. It holds the things that differ per spec: which
external specs are referenced via `data-cite` and under which keys, which
`respecConfig` fields may be edited, the terminology conventions, and the PR
conventions for that repository.

**Always start by loading the repository-specific layer.** Without it, do not
guess repository conventions — ask the user.

### Startup procedure

1. Look for `.respec-skill.md` at the repository root. Read it (it resolves
   transparently whether it is a real file or a symlink).
2. If it exists, treat its contents as authoritative for everything
   repository-specific: `data-cite` keys, editable `respecConfig` fields,
   terminology rules, branch/PR conventions, normative-vs-informative criteria.
3. If it does **not** exist, do not invent conventions. Tell the user it is
   missing, offer to scaffold one from `templates/respec-context.template.md`
   (see "Onboarding a new repository" below), and ask for the few facts needed
   to fill it (spec lineage, external `data-cite` targets, PR conventions).
4. Then load the relevant reference file(s) under `reference/` for the specific
   edit being made.

## What this skill does NOT do (scope)

Stating exclusions explicitly is part of the spec. Unless the user explicitly
asks otherwise, do not:

- **Touch `respecConfig` metadata** — `editors`, `github`, `publishDate`,
  `shortName`, `specStatus`, `group`, etc. These are owned by the repository
  maintainers. Only edit `respecConfig` fields that the repository's
  `.respec-skill.md` lists as editable.
- **Change or remove existing `id` attributes or `data-cite` reference keys.**
  Cross-references and permalinks depend on them; changing one silently breaks
  links elsewhere in the document and in other specs that cite it.
- **Hand-write anything ReSpec generates at runtime** — the table of contents,
  the references/bibliography section, the index of definitions, section
  numbers, and back-references. Author the source `<section>` and `<dfn>`
  elements; let ReSpec produce the derived artifacts.
- **Flip the normative/informative status of a section** (e.g. adding or
  removing `class="informative"`, or moving content between normative and
  informative blocks). The criteria for that classification live in the
  repository's `.respec-skill.md`; follow them, do not decide unilaterally.
- **Break the `<dfn>` ↔ reference correspondence.** A term defined with `<dfn>`
  (possibly with `data-lt` for alternate forms) is auto-linked from every `<a>`
  that matches. Renaming a definition or its `data-lt` without updating the
  references orphans the links.
- **Commit, push, merge, or open the PR without explicit confirmation.** Prepare
  the branch and the diff and show them; perform the irreversible git/PR step
  only after the user says yes.

## Editing workflow

1. **Load context.** Run the startup procedure above. Read `.respec-skill.md`,
   then the relevant `reference/` file for the kind of edit.
2. **Locate the insertion/edit point.** Find the right `<section>` by its `id`.
   Respect ReSpec's section nesting (sections nest to create the heading
   hierarchy; do not use raw `<h1>`–`<h6>` for structure — ReSpec derives
   heading levels from nesting). See `reference/respec-markup.md`.
3. **Make the edit in ReSpec idioms.** Use `<dfn>` for new terms, `<a>` for term
   references (ReSpec auto-links them — do not hand-write `href` for internal
   terms), `data-cite` for references into external specs using the keys the
   repository defines, `<code>`/`<var>` per the spec's house style.
4. **Check the cross-reference integrity.** Every new `data-cite` target must be
   a key the repository actually declares (in `respecConfig.localBiblio` or a
   recognized shortname). Every new term reference must resolve to a `<dfn>`.
5. **Preview mentally / validate.** Confirm no auto-generated content was
   hand-written, no `id` was changed, normative status is unchanged unless asked.
6. **Prepare the diff.** Make the change on a feature branch following the
   repository's branch-naming convention from `.respec-skill.md`. Show the diff.
7. **PR.** Draft the PR title/body using the repository's PR template. Confirm
   with the user before creating the branch remotely or opening the PR. See
   `reference/pr-workflow.md`.

## Reference files

Load the one(s) relevant to the current edit:

- `reference/respec-markup.md` — ReSpec markup rules: section nesting, `<dfn>`,
  `data-lt`, internal term references, `data-cite` for external refs, common
  inline elements, and the auto-generated parts to leave alone.
- `reference/respec-config.md` — `respecConfig` field meanings and the general
  edit/do-not-edit split (the repository file overrides specifics).
- `reference/pr-workflow.md` — preparing the branch, building the diff, writing
  the PR, and the confirmation gate before any irreversible git action.

## Onboarding a new repository

When `.respec-skill.md` is missing and the user wants to set it up:

1. Copy `templates/respec-context.template.md` to a central, version-controlled
   location of the user's choice (NOT inside the upstream repo working tree),
   e.g. `~/respec-skill-configs/<spec-name>/respec-context.md`, and fill it in
   with the user.
2. Symlink it into the repository root as `.respec-skill.md`, and add the link
   to git's ignore list **without modifying the upstream repo's tracked files**
   by writing to `.git/info/exclude` (local-only, never committed). Concretely:

   ```bash
   # from the spec repo root
   ln -s ~/respec-skill-configs/<spec-name>/respec-context.md .respec-skill.md
   printf '/.respec-skill.md\n' >> .git/info/exclude
   ```

   Use `.gitignore` instead of `.git/info/exclude` only if the user owns the
   repository (e.g. a personal fork) and is fine committing the ignore rule.
3. Before writing the config, inventory the document's existing `data-cite`
   targets so the reference keys captured in the config are accurate:

   ```bash
   grep -oE 'data-cite="[^"]+"' index.html | sort -u
   ```
