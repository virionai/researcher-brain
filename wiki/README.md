# wiki/

A Logseq graph synthesizing the corpus in `raw/`. Plain markdown — works
without Logseq, better with it.

**Division of labor.** `raw/` is flat and never reorganized; slugs are
stable. All structural change happens here — concept pages get merged,
split, renamed, retagged. The wiki is where the corpus becomes
navigable, where adjacencies surface, and where the operator's
questions get answered from.

## Layout

- `pages/` — one concept per file. Names and structure are customized
  by `domain.md` (`concept_categories:` shapes the `type::` values; the
  operator's sub-fields shape `domains::` tags).
- `journals/` — daily research log, `yyyy-MM-dd.md`. First place new
  observations land before they graduate into concept pages.
- `assets/` — images, diagrams, anything embedded in a page.
- `logseq/config.edn` — Logseq configuration.

## Opening in Logseq

Point Logseq at this directory (not at `pages/`). Logseq reads
`logseq/config.edn` and treats `pages/` and `journals/` as expected.

## Conventions

See `AGENTS.md` §4. Key rules:

- Filenames lowercase, kebab-case. Logseq treats `[[X]]` and `[[x]]` as one page.
- Every factual bullet cites an artifact slug: `((doi-10.1038-...))`.
- Bullets that bridge two sub-fields carry both domain tags.
- Page properties (`type::`, `aliases::`, `domains::`, `related::`) go at the top.

## Index pages

A few `_`-prefixed pages serve as the graph's table of contents.
They're created by routines as needed:

- `_index.md` — concept directory by category (seeded by R0).
- `_tags.md` — canonical tag list and merges (maintained by R6).
- `_bridges.md` — concepts whose co-occurrences span multiple
  sub-fields (output of R2; only when `cross_domain: true`).
- `_hypotheses.md` — cross-sub-field hypotheses awaiting evidence
  (output of R2).
