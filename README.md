# respec-editor

*English | [日本語](README.ja.md)*

A [Claude Code](https://claude.com/claude-code) **skill** for safely editing
[W3C ReSpec](https://respec.org/)-format HTML specification documents and
preparing pull requests against them.

ReSpec turns annotated HTML into a styled, W3C-looking specification at load
time. Authoring it has many non-obvious constraints that are easy to break
silently — orphaned cross-references, hand-writing sections ReSpec generates
automatically, accidentally flipping the normative/informative split, or
changing an `id` that other specs cite. This skill teaches Claude to make those
edits in ReSpec idioms, verify cross-reference integrity, and stop at a
confirmation gate before any irreversible git or PR action.

## What it does

- Adds or modifies sections, definitions (`<dfn>`), and normative statements in
  ReSpec source HTML, using correct section nesting and ReSpec markup.
- Keeps cross-references intact: `data-cite` into external specs, internal
  `<dfn>` ↔ `<a>` term linking, `data-lt` alternate forms, stable `id`s.
- Leaves auto-generated artifacts alone (table of contents, bibliography, index
  of definitions, section numbers, back-references).
- Prepares a feature branch and a diff, drafts the PR, and **waits for your
  confirmation** before creating branches remotely, pushing, or opening a PR.

It deliberately does **not** touch `respecConfig` metadata, change existing
`id`/`data-cite` keys, or flip normative/informative status unless you ask and
the repository's own config permits it.

## Two-layer model

The skill is split so the same shared skill works across many spec repositories:

- **Shared layer (this repository).** Knows ReSpec in general. Contains no
  knowledge specific to any one document or repository.
- **Repository-specific layer.** A file named `.respec-skill.md` at the root of
  each spec repo, holding the things that differ per spec: which external specs
  are referenced via `data-cite` and under which keys, which `respecConfig`
  fields are editable, terminology conventions, and PR conventions. It is
  typically a symlink to a centrally-stored config and is git-ignored, so
  nothing repository-specific is ever committed upstream.

See [`SETUP.md`](SETUP.md) for how to create and link the repository-specific
layer, and [`templates/respec-context.template.md`](templates/respec-context.template.md)
for the template to fill in.

## Installation

Pick whichever fits your workflow.

### As a plugin via the marketplace (recommended)

In Claude Code:

```
/plugin marketplace add shigeya/respec-editor
/plugin install respec-editor@respec-skills
```

Update later with `/plugin marketplace update respec-skills`. Installed this way,
the skill is namespaced as `respec-editor:respec-editor`.

### As a personal skill (available in every project)

```bash
git clone https://github.com/shigeya/respec-editor.git \
  ~/.claude/skills/respec-editor
```

### As a project skill (scoped to one repository)

```bash
git clone https://github.com/shigeya/respec-editor.git \
  /path/to/your/project/.claude/skills/respec-editor
```

Restart Claude Code (or start a new session) so it picks up the skill. You can
confirm it loaded by asking Claude to list available skills, or just by editing
a ReSpec document — the skill is written to trigger on terms like "ReSpec",
"respecConfig", "dfn", and "data-cite".

## Usage

Once installed, the skill activates automatically when you ask Claude to work on
a ReSpec spec, for example:

> Reflect this design discussion into the spec as a new normative section.

> Add a `digestSRI` definition to `index.html` and prepare a PR.

> Fix the `data-cite` reference for the VC Data Model in section 4.

On first use against a repository, the skill looks for `.respec-skill.md`. If
it is missing, it will offer to scaffold one from the template rather than
guessing your repository's conventions.

## Repository layout

```
SKILL.md                              The skill itself (runtime instructions)
.claude-plugin/plugin.json            Plugin manifest (for marketplace install)
.claude-plugin/marketplace.json       Marketplace listing this plugin
SETUP.md                              Operator notes: central store + symlink setup
reference/respec-markup.md            ReSpec markup rules (sections, dfn, data-cite, ...)
reference/respec-config.md            respecConfig fields and the edit/do-not-edit split
reference/pr-workflow.md              Branch, diff, PR, and the confirmation gate
templates/respec-context.template.md  Template for a repo's .respec-skill.md
examples/sample-section.html          Minimal ReSpec source fragment
```

## Scope

For ReSpec HTML only. Not for non-ReSpec HTML, [Bikeshed](https://tabatkins.github.io/bikeshed/)
sources (`.bs`), or generic Markdown.

## License

[MIT](LICENSE) © Shigeya Suzuki
