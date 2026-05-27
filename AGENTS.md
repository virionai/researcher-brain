# AGENTS.md

Operating manual for any agent (human or AI) working in this directory.
`CLAUDE.md` and `claude.md` resolve to this file via symlink — there is one source of truth.

This directory is a **generic research template**. Before doing real work,
run `R0: init` (see `program.md`) — it asks the operator about the topic,
their primary sources, and how they think about the space, and writes
`domain.md` capturing the answers. The other routines read `domain.md`
to specialize their behavior.

---

## 1. Mission

Build a knowledge base that helps the operator think about a topic more
carefully than any single source could. Three concrete things:

1. **Collect evidence** — sources, images, deep-research reports produced
   by multiple AIs, and human notes. Everything lives in `raw/`, addressed
   by stable slugs so it can be cited from anywhere without ever being
   moved.
2. **Synthesize a wiki** where every concept page links to the raw
   evidence and to neighboring concepts. The wiki is where structure
   lives; `raw/` stays flat.
3. **Answer the operator's questions** with deliverables that are
   reproducible — a reader following the citations should land on the
   same files the agent used.

If the operator enabled cross-domain mode in `domain.md` (see §1.1), the
system also actively proposes adjacencies — connections that fall between
specialties and so are likely under-explored.

### 1.1 What "cross-domain" means here

Some topics are inherently multidisciplinary. Medicine: gastroenterology
and psychiatry both touch gut–brain effects but neither owns them. ML
research: model architecture papers and benchmark papers rarely cite each
other. Law: doctrines from different jurisdictions converge on similar
problems without referencing each other. When `cross_domain: true` in
`domain.md`, `program.md` enables **R2 (cross-domain probe)**, which
walks the wiki graph looking for adjacencies the corpus has not yet
made explicit.

If the topic is single-discipline (a deep dive into one well-defined
field), leave `cross_domain: false` and R2 stays disabled. The rest of
the system works the same.

---

## 2. Directory layout

```
<root>/
├── AGENTS.md              ← this file
├── CLAUDE.md              → AGENTS.md (symlink)
├── claude.md              → AGENTS.md (symlink, case-insensitive FS)
├── program.md             ← directive runbook; see §7
├── domain.md              ← (produced by R0) customizes the template
├── domain.md.example      ← annotated reference showing what R0 produces
├── TODO.md                ← research backlog; the agent picks from this. See §10
├── raw/                   ← primary evidence, flat and slug-addressed
│   ├── sources/           ← published primary sources (papers, books, RFCs,
│   │                        case law, archives, official filings, posts …)
│   ├── images/            ← figures, diagrams, scans, screenshots
│   ├── deep-research/     ← long-form AI investigations (Claude, GPT, Gemini, …)
│   ├── notes/             ← human contributions
│   └── _inbox/            ← drop zone for unprocessed artifacts (created on demand)
├── wiki/                  ← Logseq graph synthesizing the raw corpus
│   ├── pages/             ← concept pages
│   ├── journals/          ← daily research log (yyyy-MM-dd.md)
│   ├── assets/            ← images, diagrams referenced by pages
│   └── logseq/config.edn  ← Logseq configuration
├── output/                ← deliverables answering the operator's questions
└── scripts/               ← (created when R8 produces its first tool)
```

---

## 3. The `raw/` folder

`raw/` is flat and slug-addressed. Every artifact lives in exactly one
folder, and that folder's name is its **slug** — a stable identifier
that never changes. The wiki and outputs cite slugs; the folder name
on disk is the slug. No symlinks, no topic folders, no reorganization.

Organization (by concept, by sub-field, by question) is the wiki's job,
expressed through tags and `[[wiki-links]]`. Multiple lenses over the
same artifact cost zero on disk.

### 3.1 Four artifact types

| Type             | Subfolder         | What it holds                                              |
| ---------------- | ----------------- | ---------------------------------------------------------- |
| Source           | `sources/`        | Published primary sources of any form.                     |
| Image            | `images/`         | Figures, diagrams, scans, screenshots, photographs.        |
| Deep research    | `deep-research/`  | Long-form AI investigations (Claude, GPT, Gemini, …).      |
| Note             | `notes/`          | Human contributions: anecdotes, observations, ideas.       |

Every artifact folder contains `meta.yaml` and (almost always) `notes.md`.
The artifact itself goes alongside, named by what it is.

### 3.2 Slug conventions per type

**Sources** — use the most stable identifier the source provides, with a
prefix that names the identifier scheme so collisions are impossible:

```
raw/sources/doi-10.1038-s41586-023-12345/     ← academic paper (DOI)
raw/sources/pmid-31978346/                     ← academic paper (PubMed ID)
raw/sources/arxiv-2401.04567/                  ← preprint (arXiv)
raw/sources/isbn-9780262035613/                ← book (ISBN)
raw/sources/rfc-9110/                          ← internet standard
raw/sources/scotus-2022-dobbs/                 ← case law (court-docket)
raw/sources/url-a3f9c2b1/                      ← URL hash (sha1[:8]), last resort
```

If `domain.md` specifies a preferred identifier order, R3 follows it.
Otherwise: DOI > PMID > arXiv > ISBN > domain-specific ID > URL hash.

**Images** — content hash or descriptive slug with a date prefix.

```
raw/images/img-2026-05-25-architecture-diagram/
raw/images/img-a3f9c2b1/                ← sha1[:8] of file, when no good name
```

**Deep research** — `<source>-<yyyy-MM-dd>-<topic-slug>`. The source
identifies the AI; the date is when the investigation ran.

```
raw/deep-research/claude-2026-05-25-attention-mechanisms/
raw/deep-research/gpt5-2026-04-12-mixture-of-experts/
raw/deep-research/perplexity-2026-03-30-state-space-models/
```

**Notes** — `<yyyy-MM-dd>-<author>-<topic-slug>`.

```
raw/notes/2026-05-25-josh-observation-attention-collapse/
```

Slugs are lowercase, kebab-case, and globally unique. The wiki cites
`((slug))` without knowing the type — a resolver searches the four
type subfolders. Slug prefixes (`doi-`, `pmid-`, `img-`, `arxiv-`, dated
prefixes for deep-research and notes) make collisions impossible.

### 3.3 What goes inside an artifact folder

```
raw/<type>/<slug>/
├── <artifact file>    ← source.pdf, image.png, report.md, note.md
├── meta.yaml          ← see §3.4
└── notes.md           ← agent's extraction / commentary
```

For `deep-research/` and `notes/` the artifact is itself markdown
(`report.md`, `note.md`), so `notes.md` is the **agent's commentary on
it**, not a duplicate. Optional for these types if the artifact's own
text is already structured for citation.

### 3.4 `meta.yaml` schema

Unified across types, with type-conditional fields. The free-form fields
`concepts` and `domains` are the primary axes the wiki indexes on — what
the artifact is *about* and which sub-field(s) of the topic it belongs to.

```yaml
# Common
type: source             # source | image | deep-research | note
slug: doi-10.1038-s41586-023-12345
title: "…"
url: "https://…"
license: "CC-BY-4.0"     # if known; affects redistribution
added_at: 2026-05-25
added_by: "claude-opus"  # who put it in raw/
concepts: ["…", "…"]     # what this artifact is about (lowercase, kebab-case)
domains:  ["…"]          # which sub-fields of the topic it belongs to

# --- type: source ---
identifier_scheme: doi   # doi | pmid | arxiv | isbn | rfc | url-hash | …
authors: ["…", "…"]
year: 2023
publisher: "…"           # journal, press, organization, court, etc.
source_type: "…"         # whatever taxonomy domain.md specifies
                         # (RCT / book / case / RFC / blog post / …)
key_claims: ["…"]        # one-line summaries of what the source argues
n: 124                   # sample size (if applicable)
effect_summary: "…"      # quantitative finding (if applicable)

# --- type: deep-research ---
model: "claude-opus-4-7" # or gpt-5, gemini-2.5, perplexity, …
prompt: |
  Verbatim prompt that produced the report. Reproducibility lives here.
prompt_author: "josh"
references: ["doi-10.1038-s41586-023-12345", "pmid-31978346"]  # source slugs cited

# --- type: image ---
caption: "…"
dimensions: "1200x900"
source_artifact: "doi-10.1038-s41586-023-12345"  # slug if extracted from another artifact
captured_by: "…"                                  # if original

# --- type: note ---
author: "josh"
context: "What prompted this note (conversation, observation, hunch)."
```

### 3.5 Provenance matters

With multiple AIs and humans contributing, every artifact must carry
enough provenance that an attentive reader can ask "where did this
claim come from, and how confident should I be?" That means:

- **Deep-research reports** carry the model, the verbatim prompt, and
  the slugs of the sources they cite. A deep-research report whose
  references can't be resolved to entries in `raw/sources/` is
  downgraded to a hypothesis, not evidence.
- **Notes** carry the author and the context. An observation is signal,
  but it's observation-grade signal — the wiki will tag it accordingly.
- **Images** carry their source. A figure extracted from a source
  references that source's slug; an original capture identifies who
  captured it.

---

## 4. The `wiki/` folder (Logseq graph)

The wiki is a Logseq graph. Pages are plain markdown files in `wiki/pages/`,
journals in `wiki/journals/`. Everything works without Logseq — but Logseq
gives a graph view, block references, and queries.

### 4.1 Page conventions

- One concept per page. Filenames are lowercase, kebab-case. Logseq
  treats `[[X]]` and `[[x]]` as the same page.
- Each page opens with a properties block:

  ```markdown
  type:: [[concept-type]]      # what kind of thing this is — defined in domain.md
  aliases:: …
  domains:: [[…]], [[…]]       # which sub-field(s) of the topic
  related:: [[…]], [[…]]
  ```

- Body uses outline-style bullets (Logseq is outliner-first). Each bullet
  that makes a factual claim **must cite** an artifact slug:

  ```markdown
  - Claim with a number or a finding. ((doi-10.1038-s41586-023-12345))
  - Claim derived from synthesis rather than primary evidence.
    ((claude-2026-05-25-some-topic))  #synthesis
  ```

  `((slug))` is Logseq's block-reference syntax; we overload it to mean
  "see the artifact in `raw/` with this slug" (any of the four type
  subfolders). Evidence weight scales by artifact type:
  **source > deep-research synthesis > human note**. The wiki page should
  make this visible — flag note- and deep-research-only claims with
  `#anecdote` or `#synthesis`.

### 4.2 Tags

Two tag families that the wiki always carries:

- **Domain tags** — the sub-fields named in `domain.md`. Used on any
  block that belongs to (or bridges) a sub-field.
- **Evidence-level tags** — `#hypothesis`, `#synthesis`, `#anecdote` for
  claims weaker than primary sources; plus any source-type tags the
  operator wants (e.g., `#rct`, `#cohort`, `#review`, `#book`, `#case-law`).

The operator can add free-form concept tags as needed; `_tags.md` lists
the canonical set after each R6 run.

### 4.3 Journals

`wiki/journals/yyyy-MM-dd.md` is a working log. When the agent ingests new
artifacts or notices a new connection, it goes here first as a dated
bullet, then graduates into a concept page once it's confirmed.

---

## 5. The `output/` folder

Outputs are deliverables generated in response to the operator's questions.
Each output gets its own folder:

```
output/2026-05-25-<question-slug>/
├── question.md     ← the question, verbatim
├── answer.md       ← the synthesized answer with inline citations
├── sources.md      ← every artifact slug cited, with one-line summaries
└── confidence.md   ← what we know, what's weak, what's missing
```

`confidence.md` is mandatory. Every answer states the evidence behind
it, where the evidence is thin, and what would change the conclusion.

---

## 6. Wiki reorganization protocol

`raw/` is never reorganized — slugs are stable and the four type
subfolders are fixed. All structural drift lives in the wiki.

When the wiki's concept tree becomes awkward:

1. **Propose** the change in `wiki/journals/<today>.md` under
   `### Proposed reorganization`. List moves, merges, splits, renames.
2. **Apply** in the wiki only. Renaming a page means updating every
   `[[wiki-link]]` to it and every page in its `related::` properties.
3. **Tag normalization.** If two tags refer to the same concept,
   pick one and rewrite the other across the wiki. Record the
   canonical list in `wiki/pages/_tags.md`.
4. **Verify** no dangling links: every `[[wiki-link]]` resolves to a
   page or journal entry; every `((slug))` resolves to an artifact in
   `raw/`.
5. **Log** the change in today's journal.

---

## 7. `program.md` and `domain.md`

- **`program.md`** — the directive runbook the agent follows when the
  operator invokes a routine. Methodology, not code. Scripts that
  emerge from repeated manual work go in `scripts/` and `program.md`
  documents them.
- **`domain.md`** — the customization layer. Produced by `R0: init`.
  Captures the topic, the sub-fields, the primary sources, and the
  conventions specific to this instance. Other routines read it to
  specialize their behavior.

If `domain.md` doesn't exist yet, the agent's default move is to
suggest running R0 before doing real research work.

---

## 8. Citation and provenance rules

- Every factual claim in `wiki/` and `output/` MUST cite an artifact slug.
  Claims without citations are flagged with `#unsourced` and treated as
  hypotheses, never as evidence.
- When summarizing a source, distinguish what the source **measured /
  argued / documented** from what it **claimed in framing**. Abstracts
  and press releases overreach; we don't.
- Quantitative details (sample size, effect size, dates, jurisdictions,
  scope) belong in the bullet itself, not buried in the source. A
  reader skimming the wiki should see those details inline.
- When two artifacts disagree, both are cited, and the disagreement is
  named in the wiki page. Disagreement is signal, not noise.
- Different artifact types carry different evidence weight:
  **source > deep-research synthesis > human note**.
  A claim supported only by deep-research or notes is a working
  hypothesis, not a finding — flag it as such (`#hypothesis`,
  `#synthesis`, `#anecdote`).

---

## 9. What this project is NOT

- Not a substitute for expertise in the operator's topic. The wiki
  helps the operator think; it does not replace their judgment.
- Not an exhaustive review. The point is to surface what matters and
  (optionally) what's adjacent, not to summarize the entire field.
- Not a place to store material the operator doesn't have rights to.
  If a source can't be stored locally, save `meta.yaml` and `notes.md`
  with a URL and skip the artifact file.

---

## 10. The research loop (`TODO.md`)

`TODO.md` is the queue of research topics waiting to be ingested into
`raw/`. The agent works off this list whenever the operator says
"run the loop", "pick the next one", "do some research", or similar —
and may also work off it proactively between operator requests if asked
to keep the corpus growing.

### 10.1 The loop

1. **Read** `TODO.md`. The list is grouped by axis (mechanisms/bridges,
   concept-category sections customized from `domain.md`, meta/methodology).
   Status legend is in the file header.

2. **Pick** one `[ ]` item. Default heuristic, in order:
   - An item the operator has named explicitly wins over everything else.
   - Otherwise, when `domain.md` has `cross_domain: true`, prefer items
     that bridge two sub-fields already present in `raw/` — these create
     the most adjacency value per unit work.
   - Break further ties by section order (A → B → C → D → E).
   - If multiple items in the chosen section tie, pick the topmost.

   State the pick and the reason in one sentence before starting.

3. **Mark in-progress** by flipping `[ ]` → `[~]` in `TODO.md` and
   appending the slug the artifact will land at, e.g.
   `[~] **Topic name** — … (claude-2026-05-25-topic-slug)`.

4. **Research.** Produce a deep-research report. Sources of research,
   in preference order:
   - The agent's own web search and primary-source fetching (preferred
     when feasible — gives the agent control over what gets cited).
     Use the databases listed in `domain.md` → `primary_sources:` first.
   - A prompt the operator runs in an external deep-research tool
     (Claude.ai, GPT, Gemini, Perplexity), with the output pasted back.

   In either case, the verbatim prompt is recorded in `meta.yaml`.

5. **Land the artifact** in `raw/deep-research/<slug>/` per §3.2–3.4:
   - `report.md` — the research output.
   - `meta.yaml` — `type: deep-research`, the model, the verbatim prompt,
     `concepts:`, `domains:`, and `references:` (slugs of any sources
     cited; if those sources aren't already in `raw/sources/`, queue them).
   - `notes.md` — agent's commentary, including what surprised it and
     what cross-sub-field connections it noticed.

6. **Log a journal breadcrumb.** Add a single dated bullet to
   `wiki/journals/<today>.md`:
   `- Ingested ((slug)): <one-sentence summary>` — optionally with a
   `#bridge:X-Y` tag if a cross-sub-field connection jumped out during
   research. This is the *only* wiki touch per item. Full concept-page
   synthesis (creating/updating pages, citing every claim, tagging)
   batches into the **weekly synthesis pass** (§10.4) so the
   synthesizer can see multiple new artifacts at once and notice
   cross-artifact adjacencies. Per-item synthesis would miss those.

7. **Close the loop** by flipping `[~]` → `[x]` in `TODO.md`.

8. **Feed the queue.** Every investigation surfaces follow-on questions
   — mechanisms that weren't fully resolved, neighboring concepts,
   methodology gaps. Append these as fresh `[ ]` items to the
   "Follow-ons" section of `TODO.md` (or to a relevant A–E section if
   the fit is obvious). The backlog should grow faster than it's
   consumed early on, then stabilize.

### 10.2 Rules for TODO items

- An item must be specific enough to write a research prompt from
  without further clarification. Vague items get split before being
  picked up. (Bad: "Inflammation." Better: "CRP vs hs-CRP vs IL-6 as
  clinical inflammation markers." Bad: "Attention." Better:
  "Multi-query attention vs grouped-query attention: published
  efficiency vs accuracy trade-offs." Bad: "Sovereignty." Better:
  "The doctrine of cuius regio between Augsburg (1555) and Westphalia
  (1648): what changed and what didn't.")
- Items name an entity from one of `domain.md` → `concept_categories:`,
  a mechanism, or a bridge between two of those.
- Methodology items (how to interpret a test, measurement, or
  taxonomy) belong in section E and are picked up at the same cadence
  as substantive topics.

### 10.3 What ends the loop

The loop is never "done." It pauses when the operator redirects, when
the queue is empty (rare — every investigation should refill it), or
when the corpus reaches a size where re-synthesis of the wiki is more
valuable than new ingestion (see §6).

### 10.4 Weekly synthesis pass

Concept-page work batches once a week rather than running per-item.
A weekly cadence lets the synthesizer see every artifact added that
week at once and notice **adjacencies across them** — two reports
that, taken together, imply something neither stated alone. That is
the whole point of the project. Per-item synthesis would miss it.

Triggered on a scheduled cadence (see scheduled tasks).

The pass does this:

1. **Find what's new.** Walk `wiki/journals/<every-day-since-last-pass>.md`
   for `- Ingested ((slug))` bullets. That set is the week's batch.
2. **Open each artifact.** Read its `meta.yaml` (`concepts`, `domains`,
   `references`) and its `report.md` / `note.md`.
3. **Update concept pages.** For each concept named, create or update
   the corresponding page in `wiki/pages/` per §4.1. Every factual
   claim cites `((slug))`. Flag deep-research-only or note-only claims
   with `#hypothesis`, `#synthesis`, or `#anecdote` per §8.
4. **Tag cross-sub-field bullets** per §4.2 so `#X AND #Y` queries
   surface them.
5. **Hunt adjacencies across the batch.** Look at the week's artifacts
   together. Anywhere two artifacts touch overlapping concepts or
   sub-fields, ask: does the combination imply something new? Write
   any findings up as a `### Adjacencies` section in the most recent
   journal entry, citing both `((slug))`s. (When `cross_domain: false`
   in `domain.md`, this step still runs but the bar for "adjacency
   worth noting" is higher — within-field synthesis only.)
6. **Reorganize if needed** (§6). New pages sometimes reveal that two
   sibling pages should merge, or one page should split.

The pass does NOT touch `raw/`. It only writes to `wiki/`.
