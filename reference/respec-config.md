# respecConfig fields

`respecConfig` (often assigned to `var respecConfig = {...}` in a `<script>` in
the document head) configures ReSpec. This reference explains the common fields
and the general edit / do-not-edit split. **Repository-specific overrides in
`.respec-skill.md` take precedence over the defaults here.**

## Default stance: do not edit metadata

Treat the following as maintainer-owned. Do not change them unless the user
explicitly asks and the repository's `.respec-skill.md` permits it:

- `specStatus` — the document's standing (e.g. `unofficial`, `CG-DRAFT`, `ED`,
  `WD`). Changing it alters conformance boilerplate and the whole document's
  legal/process meaning.
- `shortName` — the spec's shortname; part of its identity and citation key.
- `editors`, `authors` — credited people.
- `github`, `edDraftURI`, `previousMaturity`, `previousPublishDate` — repository
  and publication wiring.
- `group`, `wg`, `wgURI`, `wgPatentURI` — working/community group identity and
  patent policy.
- `publishDate` — the dated-version date.
- `license` — the document license.

Editing any of these silently changes the document's process status or
provenance, which is not a content edit.

## Fields more commonly safe to touch (still check the repo file)

These relate to references and local content rather than identity/process, but
confirm against `.respec-skill.md`:

- `localBiblio` — locally-defined bibliography entries. Adding an entry here is
  how you make a new external reference key available to `data-cite` / `[[...]]`
  when ReSpec's bibliography database does not already know it. Adding a
  *needed* reference is reasonable; do it deliberately and mention it in the PR.
- `xref` — controls automatic cross-referencing against external specs. Changing
  its targets affects how unresolved terms are looked up across specs.
- `lint` — lint settings; usually leave as configured.

## How config interacts with body edits

- A new `data-cite="KEY#..."` only works if `KEY` is resolvable — either a
  shortname ReSpec knows, or present in `localBiblio`. If you introduce a
  reference to a spec the document has never cited, you likely also need a
  `localBiblio` entry; treat that as a coordinated change and call it out.
- A new normative reference uses `[[!KEY]]`; ensure the corresponding
  bibliography entry exists or is resolvable.

When in doubt about whether a given field is editable for a given repository,
defer to `.respec-skill.md`; if that file does not say, ask the user rather than
assuming.
