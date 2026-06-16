# ReSpec markup rules

Reference for authoring the *source* HTML that ReSpec consumes. ReSpec runs in
the browser at load time and rewrites this source into the final styled spec.
The guiding rule: author the semantic source, never the derived output.

## Contents

- Section structure and headings
- Defining terms: `<dfn>` and `data-lt`
- Referencing terms internally
- Referencing external specs: `data-cite`
- Inline elements and house style
- Auto-generated parts to never hand-write

## Section structure and headings

ReSpec derives the heading hierarchy from `<section>` nesting, not from the
`<h*>` level you choose. A `<section>` contains exactly one heading element
(any of `<h2>`–`<h6>`, or just `<h2>`; ReSpec renumbers) followed by content
and nested `<section>`s.

- Give every `<section>` a stable `id`. The `id` becomes the permalink fragment
  and is referenced by `data-cite` from other specs. **Never change an existing
  `id`** — it breaks inbound links.
- Nest sections to create sub-sections rather than bumping heading numbers.
- `class="informative"` marks a section non-normative. `class="introductory"`
  marks front-matter-like sections (abstract/SOTD are special-cased). Do not
  add or remove these classes to change a section's status without authority
  from the repository's `.respec-skill.md`.
- `class="appendix"` on a top-level section makes it a lettered appendix.

```html
<section id="issuance">
  <h2>Credential issuance</h2>
  <p>...</p>
  <section id="issuance-flow">
    <h3>Issuance flow</h3>
    <p>...</p>
  </section>
</section>
```

## Defining terms: `<dfn>` and `data-lt`

A term is defined once with `<dfn>`. ReSpec indexes it and auto-links every
matching reference in the document to this definition.

- `<dfn>issuer</dfn>` defines the term "issuer".
- `data-lt` ("link text") lists alternate surface forms that should resolve to
  the same definition: `<dfn data-lt="issuers|issuing party">issuer</dfn>`.
- `data-local-lt` adds forms that only resolve within the same section.
- To export a definition so other specs can `data-cite` it, ReSpec exports
  `<dfn>`s by default in a spec; `data-noexport` suppresses that.

Breaking the term ↔ reference link is a common silent failure: renaming a
`<dfn>` or editing its `data-lt` without updating the matching `<a>` references
leaves them unresolved. If you rename a defined term, grep for all references
to the old form and update them.

## Referencing terms internally

Reference a defined term with a bare `<a>` (no `href`). ReSpec resolves the
link text against the definitions index:

```html
<p>The <a>issuer</a> signs the credential.</p>
```

- Do not hand-write `href="#..."` for internal term links; let ReSpec resolve
  them so renamed anchors do not rot.
- To force a specific definition when text is ambiguous, use
  `<a data-lt="issuer">...</a>`.

## Referencing external specs: `data-cite`

`data-cite` links into another specification's exported definitions or sections.

- `<a data-cite="RFC8555#section-7.4">...</a>` links to a section of a cited
  spec by its shortname/key and fragment.
- `<a data-cite="INFRA#string">string</a>` links to an exported term in another
  spec.
- The key before `#` (e.g. `RFC8555`, `INFRA`, `WEBIDL`) must be a reference the
  repository declares — either a well-known shortname ReSpec resolves via its
  bibliography database, or an entry in `respecConfig.localBiblio`.
- **The exact set of valid keys is repository-specific.** Use only the keys
  listed in the repository's `.respec-skill.md`. If a needed external reference
  is not yet declared, flag it — adding a new external reference is a config
  change, not a body edit, and may need maintainer agreement.

`[[RFC8555]]` / `[[!RFC8555]]` (double brackets) produce bibliography
references; the `!` form marks the reference normative. These populate the
auto-generated references section — do not also hand-write that section.

## Inline elements and house style

- `<code>` for literals; `<var>` for variables/metavariables.
- Algorithm steps: ordered lists, often with `class="algorithm"`; many specs use
  the `<ol>`/`<li>` step style. Follow the document's existing pattern.
- RFC 2119 keywords (MUST, SHOULD, MAY) appear in normative statements; some
  specs wrap them or rely on ReSpec styling. Match the surrounding text — do not
  introduce a new convention.
- Match the document's existing voice and structure rather than pulling toward a
  generic template; preserve the spec's house style.

## Auto-generated parts to never hand-write

ReSpec generates these at runtime. Writing them by hand creates duplicates and
conflicts:

- Table of contents.
- Section numbers and heading numbers.
- The References / Bibliography section (driven by `[[...]]` citations and
  `localBiblio`).
- The index of definitions / terms.
- Back-references ("Referenced in:") under definitions.
- Conformance boilerplate that ReSpec injects based on `specStatus`.
