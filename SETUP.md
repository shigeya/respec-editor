# Setup & symlink operations (for the human operator)

This skill keeps repository-specific config OUT of the upstream repo by storing
it centrally and symlinking a git-ignored link into the repo root. This file is
operator notes — not part of the skill's runtime instructions.

## One-time central store

Pick a central, version-controlled location for all your per-spec configs:

```bash
mkdir -p ~/respec-skill-configs
```

## Onboarding one repository

```bash
# 1. Create the per-spec config from the template and fill it in.
mkdir -p ~/respec-skill-configs/<spec-name>
cp templates/respec-context.template.md \
   ~/respec-skill-configs/<spec-name>/respec-context.md
# ...edit it (inventory data-cite keys, set PR conventions, etc.)

# 2. From the spec repo root, link it in as .respec-skill.md
cd /path/to/<spec-repo>
ln -s ~/respec-skill-configs/<spec-name>/respec-context.md .respec-skill.md

# 3. Git-ignore the link WITHOUT modifying the upstream repo's tracked files.
#    .git/info/exclude is local-only and never committed.
printf '/.respec-skill.md\n' >> .git/info/exclude

# 4. Verify the link is ignored and resolves.
git check-ignore .respec-skill.md   # prints the path => ignored
cat .respec-skill.md                 # resolves through the symlink
```

Use `.gitignore` instead of `.git/info/exclude` ONLY for repos you own (e.g. a
personal fork) where committing the ignore rule is acceptable.

## Inventory existing data-cite keys (fills the config)

```bash
grep -oE 'data-cite="[^"]+"' index.html | sort -u
```

## Why this layout

- The skill (shared layer) is installed once and reused across every spec repo.
- Per-spec knowledge lives centrally in `~/respec-skill-configs/` and is linked
  in, so nothing repository-specific is committed upstream.
- The link is the only thing in the working tree, and it is git-ignored, so the
  upstream repo stays clean.
