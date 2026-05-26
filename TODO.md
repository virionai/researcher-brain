# TODO — Research backlog

High-level list of research topics queued for ingestion into `raw/`.
Each unchecked item is a candidate for a deep-research investigation.
Workflow lives in `AGENTS.md` §10.

**Status legend**
- `[ ]` queued
- `[~]` in progress (a draft / partial report exists in `raw/deep-research/`)
- `[x]` complete (artifact landed in `raw/`; wiki synthesis happens
  separately on the weekly pass — see AGENTS.md §10.4)

Each item should be specific enough that an agent can write a single
research prompt from it without further clarification. Vague items get
split before being picked up.

> **Customizing sections.** Sections A and E below are universal — every
> domain has mechanisms/bridges and methodology questions. Sections B–D
> should be renamed to your `domain.md` → `concept_categories:`. For
> example, a medical project might use *Interventions* / *Targets* /
> *Conditions*; an ML-research project might use *Techniques* / *Models*
> / *Benchmarks*; a legal project might use *Doctrines* / *Cases* /
> *Jurisdictions*. `R0: init` produces an initial customized version of
> this file; `R6: improve-wiki` may suggest renames as the corpus
> develops.

---

## A. Mechanisms & bridges
*Topics that name the underlying machinery — the "how X connects to Y."
Highest cross-domain leverage per unit work.*

- [ ] (Example) **The mechanism / pathway / chain of reasoning that links
  two phenomena already in the corpus.** Specifically: which steps, which
  intermediaries, which evidence types, and where the current literature
  is contested.

## B. {concept_category 1 from domain.md}
*Rename this section to your first concept category.*

- [ ] (Example) **A specific entity in this category, with the precise
  question your investigation will answer.** Not "this category in
  general" — a single named entity and a single question.

## C. {concept_category 2 from domain.md}
*Rename this section to your second concept category.*

- [ ] (Example) **A specific entity in this category, with the precise
  question your investigation will answer.**

## D. {concept_category 3 from domain.md}
*Rename this section to your third concept category — or delete this
section if your domain has only two.*

- [ ] (Example) **A specific entity in this category, with the precise
  question your investigation will answer.**

## E. Meta / methodology
*Not a single topic but a tool the wiki needs to interpret everything else.
How to read evidence in this domain, what tests/measurements mean, what
taxonomies the field uses, where the field disagrees about methodology.*

- [ ] (Example) **A measurement, test, or methodological convention that
  the wiki will repeatedly need to interpret.** What it measures, what it
  doesn't, where it's reliable, where it isn't, and how to weigh it in
  the citation hierarchy from `AGENTS.md` §8.

---

## Follow-ons surfaced by prior investigations
*Items the agent surfaced while doing other research; reslot into A–E
during the weekly synthesis pass.*

<!-- New items land here as `[ ]` entries with a trailing note like
"Surfaced by `((slug-of-investigation))`". The weekly synthesis pass
moves them up into A–E. -->

---

## How to use this list

1. Pick a `[ ]` item (see AGENTS.md §10 for the picking heuristic).
2. Flip it to `[~]` and append the slug it will receive in
   `raw/deep-research/` in parentheses, e.g.
   `[~] **Topic name** — … (claude-2026-05-25-topic-slug)`.
3. When the research artifact lands in `raw/`, flip to `[x]`. Wiki
   synthesis is NOT required to close an item — that batches into the
   weekly synthesis pass (AGENTS.md §10.4).
4. Append any new topics surfaced during the investigation to the
   "Follow-ons" section above (or to the relevant A–E section if the fit
   is obvious).
