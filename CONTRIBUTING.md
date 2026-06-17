# Contributing to respec-editor

Thanks for your interest in improving this skill. It is a [Claude Code](https://claude.com/claude-code)
skill for editing W3C ReSpec specifications — see the [README](README.md) for
what it does and how it is structured.

## Scope: keep the shared layer general

This repository is the **shared layer**: general ReSpec authoring knowledge that
works across every spec repository. Anything specific to one document or
repository (its `data-cite` keys, editable `respecConfig` fields, terminology,
PR conventions) belongs in that repository's own `.respec-skill.md`, **not**
here. Contributions that hard-code one spec's conventions into the shared layer
will be asked to move that knowledge into the per-repo template instead.

A good shared-layer contribution is one of:

- A ReSpec markup rule or constraint that applies to ReSpec in general
  (`reference/respec-markup.md`).
- A clarification of `respecConfig` semantics or the edit / do-not-edit split
  (`reference/respec-config.md`).
- A safer PR / diff workflow step (`reference/pr-workflow.md`).
- A new field or clearer guidance in the per-repo template
  (`templates/respec-context.template.md`).

## Making a change

1. Open an issue first for anything non-trivial, so we can agree the change
   belongs in the shared layer.
2. Fork and create a branch (`respec/<short-topic>` is a fine default).
3. Keep edits focused and small; prefer many small files over large ones.
4. When you add a ReSpec rule, cite the authority for it — the
   [ReSpec documentation](https://respec.org/docs/) or a concrete spec example —
   in the PR description.
5. Open a pull request describing the problem and the change.

## Testing your change

There is no build step. To sanity-check a change to the skill:

1. Install your branch as a skill (see [README — Installation](README.md#installation)).
2. Start a fresh Claude Code session so it reloads the skill.
3. Ask Claude to perform the kind of edit your change affects against a real or
   sample ReSpec document (`examples/sample-section.html` is a starting point)
   and confirm it behaves as intended — including stopping at the confirmation
   gate before any irreversible git/PR action.

## License

By contributing, you agree that your contributions are licensed under the
project's [MIT License](LICENSE).
