# raw/

Primary evidence. Flat and slug-addressed. **Never reorganized** — slugs
are the stable identifier the wiki and outputs cite, and they never
change. Cross-concept organization is the wiki's job, not this folder's.

## Four artifact types

```
raw/
├── sources/         ← published primary sources of any form
├── images/          ← figures, diagrams, scans, screenshots
├── deep-research/   ← long-form AI investigations (Claude, GPT, Gemini, …)
├── notes/           ← human contributions
└── _inbox/          ← drop zone for unprocessed artifacts (created on demand)
```

`sources/` covers anything the operator's `domain.md` defines as a
primary source: academic papers (DOI/PMID/arXiv), books (ISBN), case
law (court-docket), internet standards (RFC), archived documents
(URL-hash), and so on. The identifier-scheme prefix in the slug
disambiguates them.

Every artifact folder contains the artifact file + `meta.yaml` +
(usually) `notes.md`. The folder's name is the artifact's slug.

## Slug conventions

| Type            | Slug pattern                                        | Example                                              |
| --------------- | --------------------------------------------------- | ---------------------------------------------------- |
| source          | `<scheme>-<id>` (DOI / PMID / arXiv / ISBN / RFC / …) | `doi-10.1038-s41586-023-12345`, `isbn-9780262035613` |
| image           | `img-<date>-<descriptor>` or `img-<sha1[:8]>`       | `img-2026-05-25-architecture-diagram`                |
| deep-research   | `<ai-source>-<date>-<topic-slug>`                   | `claude-2026-05-25-attention-mechanisms`             |
| note            | `<date>-<author>-<topic-slug>`                      | `2026-05-25-josh-observation-attention-collapse`     |

Slugs are lowercase, kebab-case, and globally unique. The scheme prefix
on source slugs (and `img-` / dated prefixes on the other types) makes
collisions impossible. The wiki cites `((slug))` and a resolver searches
all four type subfolders.

## What goes inside

```
raw/<type>/<slug>/
├── <artifact>     ← source.pdf | image.png | report.md | note.md
├── meta.yaml      ← schema in AGENTS.md §3.4
└── notes.md       ← agent's commentary (extraction, weighting, caveats)
```

For `deep-research/` and `notes/` the artifact is itself markdown, so
`notes.md` is the **agent's commentary on it** — not a duplicate.

## Adding an artifact

See `program.md` → **R3: ingest**. Drop loose files in `raw/_inbox/`
(create it as needed) and the next R3 run slugs them and files them away.

## Provenance is required

Different artifact types carry different evidence weight:

- **Primary sources** (sources/) → strongest weight. Within sources,
  weight further depends on what `domain.md` → `source_types` specifies
  (e.g., RCT > cohort > case report; monograph > blog post; etc.).
- **Deep-research syntheses** → hypothesis-grade unless every citation
  resolves to a primary source we hold.
- **Notes** → observation-grade. Useful as signal, not as proof.

`meta.yaml` must carry enough provenance to defend the weighting. For
deep-research that means the verbatim prompt and the model; for notes,
the author and the context; for sources, the canonical identifier and
the source type.

See `AGENTS.md` §3 for the full spec.
