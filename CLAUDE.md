# Pavel-lab — Project Wiki

Obsidian-compatible wiki of all vibe-coded projects.
One markdown page per project in `projects/` or `ideas/`.

## Structure

```
projects/{slug}.md   — one page per real project (has a repo)
ideas/{slug}.md      — concepts not yet started
_templates/          — copy these when creating new pages
index.md             — Obsidian home with [[wikilinks]]
README.md            — GitHub landing page (regenerated, never edit by hand)
```

## Rules

- Frontmatter is required on every page — it's the data layer
- `README.md` is always regenerated from frontmatter — never edit by hand
- `index.md` uses `[[wikilinks]]` — keep slugs matching filenames exactly
- Status values: `idea` / `active` / `shipped` / `abandoned` only
- Commit format: `catalogue: {action} [[{slug}]]`

## Operations

| What | How |
|------|-----|
| New project | `/catalogue add` — called by `/init` automatically |
| Status change | `/catalogue status {slug} {new-status}` |
| Session note | `/catalogue session` — called by `/handoff` automatically |
| New idea | `/catalogue idea "name" "description"` |
| Regenerate README | Read all `projects/` frontmatter, rebuild tables by status |

## Status Values

| Status | Meaning |
|--------|---------|
| `idea` | Concept only — no repo yet |
| `active` | Being actively built |
| `shipped` | Live / deployed / usable |
| `abandoned` | Started but not continuing |
