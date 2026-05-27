# program.md

The directive runbook for this research project. `AGENTS.md` defines
the **structure**; this file defines the **routines** the agent runs
against that structure.

When the operator says "run the program," the agent picks the routine
that matches the request, executes it, and logs what it did in
`wiki/journals/<today>.md`.

**Before anything else:** if `domain.md` does not exist in the project
root, run **R0: init** first. The other routines read `domain.md` to
specialize for the operator's topic; without it they fall back to
defaults that are probably not what the operator wants.

---

## Routine catalog

| Name                          | Trigger                                                          | What it does                                                                 |
| ----------------------------- | ---------------------------------------------------------------- | ---------------------------------------------------------------------------- |
| `R0: init`                    | First session, or "init", or `domain.md` missing                 | Interview the operator. Produce `domain.md`. Seed the wiki index.            |
| `R1: continue-on`             | "continue researching X", "go deeper on X"                       | Expand the corpus around an existing concept.                                |
| `R2: cross-domain-probe`*     | "find adjacencies", "what crosses fields", "what am I missing"   | Walk the graph for under-studied pairs. **Opt-in via `domain.md`.**          |
| `R3: ingest`                  | "I've added artifacts", file appears in `raw/_inbox/`            | Slug, metadate, file, and index a new artifact.                              |
| `R4: synthesize`              | "update the wiki", "synthesize what we've learned about X"       | Promote journal bullets into concept pages.                                  |
| `R5: answer`                  | a question from the operator                                     | Produce an `output/<date>-<slug>/` deliverable.                              |
| `R6: improve-wiki`            | "tidy up", run on cadence                                        | Re-cluster concepts, prune stubs, normalize tags, audit provenance.          |
| `R7: knowledge-graph`         | "show me the graph", "what connects to X"                        | Render the current concept graph; flag isolates and bridges.                 |
| `R8: build-wiki-tool`         | "build a tool for X", or a manual chore that recurs ≥2×          | Convert recurring wiki-maintenance pain into scripts in `scripts/`.          |
| `R9: consolidate-todo`        | "consolidate", "tighten the TODO", daily cadence                 | Close `TODO.md` items already covered by `raw/`; cluster similar items into batched research targets for the next research session. |

\* R2 is only available when `domain.md` sets `cross_domain: true`. If
`cross_domain: false`, R2 stays disabled and the agent declines the
trigger phrases with a one-line explanation pointing at `domain.md`.

---

## R0: init

**Goal.** Customize this generic template for the operator's actual
topic. Produced artifact: `domain.md` at the project root.

This is an interactive interview. The agent asks one question at a
time, listens to the answer, and only moves on when the answer is
concrete enough to act on. Six questions, plus a confirmation step.

### Q1 — The topic

> "In one or two sentences, what are you researching?"

Push back if the answer is too broad ("AI") or too narrow ("the loss
function on line 47 of this paper"). The right granularity is a
**field of inquiry where reasonable people would disagree about which
sub-fields it contains** — that's the size of corpus this system pays
off on.

Record verbatim. Becomes `domain.md` → `topic:`.

### Q2 — The concept categories

> "What kinds of things do you study in this topic? Name 2–5
> categories. To give you a feel: medicine has *interventions* and
> *physiological systems* and *conditions*; ML research has
> *techniques* and *models* and *benchmarks*; law has *doctrines*
> and *cases* and *jurisdictions*."

The operator names 2–5 categories. These become the values used in
wiki page properties (`type:: [[<category>]]`) and tag families.

Record as `domain.md` → `concept_categories:`.

### Q3 — The sub-fields

> "What sub-fields, specialties, or schools of thought cut across this
> topic? Where would experts disagree about who owns what? Aim for 3–8."

The operator names sub-fields. These become the **domain tags** that
R2 (if enabled) uses to find bridges. Even when R2 is off, these tags
help structure the wiki.

Record as `domain.md` → `subfields:`.

### Q4 — The primary sources

> "Where does the best primary evidence live for this topic? Name the
> 2–5 places you'd actually look first."

Could be PubMed for medicine, arXiv for ML, JSTOR for humanities,
Westlaw or CourtListener for law, official archives for history,
GitHub or specific RFC indices for engineering, etc.

For each, capture: name, URL pattern, preferred identifier scheme
(DOI, ISBN, RFC number, case-docket, …).

Record as `domain.md` → `primary_sources:`.

### Q5 — Cross-domain mode

> "Some topics pay off when you look for connections **between**
> sub-fields — findings in one specialty that have implications for
> another but aren't cited there yet. Other topics are deep dives in
> a single well-defined field. Which is this?"

If yes → set `cross_domain: true`, R2 is enabled.
If no → set `cross_domain: false`, R2 stays off.
If unsure → default `true` and explain you can disable it later.

### Q6 — The seed question

> "What's the first question you actually want this system to help
> you with? Or: what's the hypothesis you most want to investigate?"

This is optional but useful — it gives the agent a concrete starting
point for R1 or R5 once init finishes.

Record as `domain.md` → `seed_question:`. If the operator skips, leave
blank.

### Confirmation and writeback

After Q6, the agent:

1. Reads the answers back to the operator in plain language. ("So
   you're studying X, with categories A/B/C, sub-fields ɑ/β/ɣ/δ,
   primary sources at S1/S2, cross-domain mode is on, and the first
   thing you want to look at is Q.")
2. Asks "anything to change?" — only writes when the operator confirms.
3. Writes `domain.md` at the project root. (Format: see
   `domain.md.example` for the exact schema.)
4. Creates `wiki/pages/_index.md` with a stub listing the sub-fields
   and concept categories as initial empty pages.
5. Customizes `TODO.md`: renames sections B–D to match
   `concept_categories:`, deletes any placeholder section the domain
   doesn't need, and (if a seed question was given in Q6) adds it as
   the first `[ ]` item in the most relevant section. See `AGENTS.md`
   §10 for the loop that consumes this file.
6. Logs the init in `wiki/journals/<today>.md`.
7. Offers the seed question via R1 or R5 if it's set.

### When to re-run R0

Not often. Re-run only when the topic itself changes (the operator
pivots), or when sub-fields or concept categories were so wrong they
need a re-do. Tag normalization and page reorganization are R6's job,
not R0's.

---

## R1: continue-on

**Goal.** Deepen coverage of a concept the operator already cares about.

1. Read the concept page in `wiki/pages/<concept>.md`. Note its
   `domains::` and `related::` properties, and the slugs already cited.
2. Enumerate the **questions the page does not yet answer**: open
   mechanisms, contradictions between cited sources, weak claims,
   sub-fields not yet represented.
3. Search primary sources. Use the list in `domain.md` →
   `primary_sources:` first; fall back to general web search only when
   primary sources don't apply.
4. For each promising hit, run **R3: ingest**.
5. Update the concept page with new bullets, each citing the new slug.
6. If new bullets create connections to other concepts, add
   `[[wiki-links]]` and tags. If three or more bullets point at a
   concept that has no page, create the page (stub is fine).
7. Log in journal: artifacts added, claims strengthened, claims weakened.

---

## R2: cross-domain-probe (opt-in)

**Available only when `domain.md` → `cross_domain: true`.**

**Goal.** Find adjacencies that a specialist would miss because they
sit at the edge of two sub-fields.

The procedure is graph-walking, not literature-searching:

1. **Build a co-occurrence table** from the wiki. For each concept,
   list the other concepts it co-occurs with in cited claims, weighted
   by the strength of evidence (source > deep-research > note).
2. **Find bridge concepts.** A concept is a "bridge" when its
   co-occurrences span ≥2 `domains::` (sub-fields). List bridges
   sorted by how many sub-fields they span.
3. **Find sparse cells.** For each bridge concept, find pairings that
   are *missing* from the corpus but where the surrounding row and
   column are dense. Those are the under-studied pairs.
4. **Test against the literature.** For each candidate sparse cell,
   search the primary sources in `domain.md`. Three outcomes:
   - Evidence exists but we missed it → run R3 on the new artifacts.
   - Evidence is thin or absent → record as a **hypothesis** in
     `wiki/pages/_hypotheses.md` with reasoning and adjacent evidence.
   - Evidence contradicts the adjacency → record as a **null** and
     note why the obvious adjacency failed (often interesting on its own).
5. **Report to the operator.** Top 5 bridges and the most interesting
   sparse cells with confidence levels.

Heuristics for what makes an adjacency worth flagging:

- The concepts share a mechanism that's already known to influence both.
- One side of the adjacency is high-impact in the operator's value
  judgment (high prevalence, high stakes, high cost-to-test).
- The connection is cheap or low-risk to investigate further.

Heuristics for what to deprioritize:

- Adjacencies driven by a single low-quality source or speculation.
- Connections so general they have no predictive content.

---

## R3: ingest

**Goal.** Get a new artifact into `raw/` correctly so the rest of the
system can use it. Works for all four artifact types.

1. **Identify the type.** PDF or source URL → `source`. Image file or
   screenshot → `image`. Long-form markdown produced by an AI
   investigation → `deep-research`. Free-form markdown from a human →
   `note`.
2. **Slug it** per `AGENTS.md` §3.2. For sources, use the identifier
   scheme `domain.md` specifies (DOI / PMID / arXiv / ISBN / RFC /
   case-docket / URL-hash) — prefix the slug with the scheme name so
   collisions are impossible (`doi-…`, `pmid-…`, `isbn-…`, `rfc-…`).
3. **Create the folder** at `raw/<type>/<slug>/`. Drop the artifact
   alongside as `source.<ext>`, `image.<ext>`, `report.md`, or `note.md`.
   If rights don't allow storing the artifact, keep only `meta.yaml`
   with the URL.
4. **Write `meta.yaml`** per `AGENTS.md` §3.4. Be honest about scope,
   sample size, and methodology. For deep-research, capture the
   verbatim prompt and the model. For notes, name the author and the
   context.
5. **Write `notes.md`** (the agent's commentary). 3–10 bullets:
   - For sources: key claims, methods/evidence, quantitative details,
     caveats the framing glosses over.
   - For deep-research: which claims are well-supported by cited
     sources and which are speculative; where it overlaps or
     conflicts with the existing wiki.
   - For images: what the image shows, why it matters, which bullets
     it should be embedded next to.
   - For notes: how to weight the observation and what would convert
     it into stronger evidence.
6. **Update the wiki.** Add a bullet to every concept page that should
   know about this artifact. Each bullet cites the slug. If a new
   concept emerges, create a stub page.
7. **Journal entry.** Today's journal gets:
   `- Ingested ((<slug>)) [type] — one-line summary`.

No symlinks, no topic folders under `raw/`. Cross-concept organization
lives in the wiki via tags and `[[wiki-links]]`.

---

## R4: synthesize

**Goal.** Turn raw notes and journal bullets into durable concept
pages.

1. Pick a concept (the operator names one, or pick the page with the
   most stale unintegrated bullets).
2. Pull every bullet from `notes.md` files across `raw/` that's tagged
   with this concept, plus every journal entry mentioning it.
3. Group bullets by claim type: mechanism, observed effect, scope,
   counterexample, contradiction.
4. Rewrite each group as a tight outline section on the concept page.
   Keep every citation. Where multiple artifacts agree, cite all of
   them; where they disagree, name the disagreement.
5. Update `related::` and tags. Add `[[wiki-links]]` for every concept
   that already has a page; create stub pages for the rest.
6. Update the wiki index page (`wiki/pages/_index.md`).

---

## R5: answer

**Goal.** Produce a defensible deliverable answering the operator's
question.

1. Restate the question. If it's ambiguous, ask before proceeding.
2. Identify which wiki concepts the question touches.
3. Read those pages and the cited artifacts (sources, deep-research,
   images, notes). Pull every relevant claim, and note which artifact
   type backs each claim — that's input to the confidence file.
4. If the wiki is thin in this area, run **R1** first.
5. Create `output/<yyyy-MM-dd>-<question-slug>/` with the four files
   described in `AGENTS.md` §5.
6. The `confidence.md` file is mandatory. State what we know (with
   evidence level), what's weak, and what's missing — and what would
   change the answer.

---

## R6: improve-wiki

**Goal.** Keep the wiki navigable as it grows. `raw/` is flat and
slug-addressed — it doesn't get reorganized, so this routine touches
only the wiki.

Run when any of these is true: tag drift; stub pages accumulating;
concept pages that should be merged or split; `((slug))` references
that don't resolve; or the operator asks for a tidy.

1. **Resolve check.** Every `((slug))` in the wiki should point at a
   real folder in `raw/*/`. Find unresolved ones, fix or report.
2. **Tag normalization.** Build a tag inventory across the wiki.
   Merge near-duplicates. Record the canonical list in
   `wiki/pages/_tags.md`.
3. **Concept re-clustering.** Where two pages describe the same
   concept under different names, merge (follow `AGENTS.md` §6).
   Where one page has grown into two distinct sub-concepts, split.
4. **Stub pruning.** A stub page (properties block only, no bullets)
   that's older than 30 days and has no inbound links is dead weight.
   Either flesh it out (run R1) or delete it.
5. **Bridge index** (only if `cross_domain: true`). Regenerate
   `wiki/pages/_bridges.md` listing every concept whose co-occurrences
   span ≥2 sub-fields (output of R2 step 2).
6. **Provenance audit.** Spot-check claims supported only by
   deep-research or notes — they should be tagged `#hypothesis`,
   `#synthesis`, or `#anecdote`, not presented as findings. Fix tags
   where the weight is overstated.
7. **Log everything.** Today's journal gets a `### R6 ran` block with
   moves, merges, deletions, retag operations, and (if applicable) new
   bridges.

---

## R7: knowledge-graph

**Goal.** Render the current concept graph so the operator can see
the shape of the corpus.

1. Walk `wiki/pages/`. For each page, parse `related::` and inline
   `[[wiki-links]]`. Build an adjacency list.
2. Render as a Mermaid graph saved to `output/<date>-graph/graph.md`,
   and as a simple list:
   - **Hubs** — pages with the most inbound links (the concepts the
     corpus orbits around).
   - **Bridges** — pages whose neighbors split into two otherwise-
     disconnected components (rare, valuable concepts).
   - **Isolates** — pages with zero links (probably stubs to prune
     or elaborate).
3. Offer R2 (if enabled) against the bridges and R1 against the
   isolates as follow-ups.

---

## R8: build-wiki-tool

**Goal.** Convert recurring wiki-maintenance pain into reusable scripts
in `scripts/`. The wiki is plain markdown; most maintenance chores are
mechanical enough to script, and once scripted they stop costing
attention.

This is the **only** routine that produces code. R0–R7 are procedures
the agent executes by hand; R8's deliverable is a script another
routine can call.

### Procedure

1. **Name the pain.** Write one paragraph describing the recurring
   manual task: what the agent does by hand, how often, what's
   brittle about it. If the pain hasn't appeared at least twice, the
   pattern isn't real yet — defer (this respects the self-improvement
   directive at the bottom of this file).

2. **Spec the tool** in `scripts/<tool-name>/SPEC.md`:
   - **Input:** which files/folders it reads.
   - **Output:** stdout, a tracking file, or a journal entry.
   - **Side effects:** does it write anywhere? Default: no.
   - **Non-goals:** what it intentionally does not do.

3. **Prototype** in `scripts/<tool-name>/<tool-name>.py` (or `.sh` if
   a shell one-liner truly suffices). Stdlib + ripgrep only unless an
   external dep is justified in the spec.

4. **Dry-run first.** First invocation never writes to `raw/` or
   `wiki/`. It prints a plan or writes to its own folder. A `--apply`
   flag enables write-mode; the operator approves before the first
   apply.

5. **Document in the tool catalog below.** Name, status, callers,
   one-line purpose. The script is invocable independent of this
   document — the catalog is the contract.

6. **Track maturity.** A tool starts as `experimental` (called by
   hand), matures to `routine` (called from another R-routine), and
   graduates to `scheduled` (runs on cadence). Update its status in
   the catalog as it moves.

### How to invoke a tool

All tools live under `scripts/<tool-name>/` with a `<tool-name>.py`
entry point. A small dispatcher at `scripts/<project-name>` routes
invocations:

```
scripts/<project-name> list                          # show available tools
scripts/<project-name> <tool-name> [args]            # run a tool
scripts/<project-name> --help                        # usage
```

The repo root is passed to every tool via the `<PROJECT>_REPO` env
var, so tools work regardless of the caller's cwd. The first time R8
runs, it creates `scripts/README.md` and the dispatcher; subsequent
runs only add new tools. The default dispatcher name comes from the
operator's project folder name — if your project is in
`MyResearchProject/`, the dispatcher is `scripts/myresearchproject`
and the env var is `MYRESEARCHPROJECT_REPO`. See `scripts/README.md`
for the full convention once it exists.

### Tool catalog

| Tool                       | Status         | Called by   | Purpose                                                              |
| -------------------------- | -------------- | ----------- | -------------------------------------------------------------------- |
| *(empty — populated as R8 produces tools)* |   |   |   |

### Worked example (illustrative): `find-orphan-citations`

This example is offered as a model of the kind of pain that justifies
a tool, not as a tool you're required to build.

**Pain.** Deep-research reports in `raw/deep-research/` cite primary
sources, often by their identifier (DOI, PMID, arXiv, RFC, ISBN,
case-docket, URL-hash), but the actual sources usually aren't in
`raw/sources/`. Per `AGENTS.md` §3.5, a deep-research report whose
references can't be resolved to entries in `raw/sources/` is
downgraded to a hypothesis, not evidence. Right now nothing tells us
which citations need collecting — we'd have to read every report and
cross-check by hand.

**Spec.**
- **Input:**
  - `raw/deep-research/*/meta.yaml` — the `references:` list.
  - `raw/deep-research/*/report.md` — inline identifier extraction
    via regex (DOI, PMID, arXiv, RFC, ISBN, case-docket, URL).
  - `wiki/pages/**/*.md` and `wiki/journals/**/*.md` — `((slug))`
    patterns.
- **Cross-reference** against `ls raw/sources/` to determine which
  referenced slugs already have folders.
- **Output:** `scripts/find-orphan-citations/MANIFEST.md` — a markdown
  table of orphan citations with columns:
  *slug (proposed)* · *citation source* (which report or wiki page
  mentions it) · *identifier* (DOI / PMID / URL / …) · *status*
  (`pending` / `collected` / `paywalled` / `broken-link` / `deferred`)
  · *notes*.
- **Side effects (dry-run):** none — writes only to its own MANIFEST.md.
- **Side effects (--apply):** invokes a companion `collect-source`
  tool for each pending entry, which tries domain-appropriate
  retrieval (Unpaywall for DOIs, PMC for PMIDs, arXiv API for
  arXiv IDs, etc.) and on failure creates
  `raw/sources/<slug>/meta.yaml` with the URL only (per
  `AGENTS.md` §9 — material we don't have rights to is recorded but
  not stored). Status is written back to MANIFEST.md.
- **Non-goals:** does not interpret sources, does not write to the
  wiki, does not infer citations from prose claims (only structured
  identifiers / `((slug))` references).

**Why this is the right first tool for almost any domain.** Citation
completeness is usually the single biggest gap in the corpus —
deep-research reports cite sources we don't have. The wiki's
evidence-weighting rules (§8) literally depend on those references
being resolvable. Closing this gap once, then keeping it closed on
cadence, is foundational to everything else.

---

## R9: consolidate-todo

**Goal.** Keep `TODO.md` working as a real queue. Two problems compound
as research progresses: items already covered by artifacts in `raw/`
stay open and get re-picked; near-duplicate items proliferate as each
investigation surfaces follow-ons that overlap with each other and with
the original sections. R9 fixes both by closing what's already done
and merging what should be researched together, leaving the next
research session a dense, batched queue instead of a long list of
marginals.

This is the only routine that destructively rewrites `TODO.md`. The
audit trail lives in `TODO-batched-archive.md` — nothing is ever
deleted without being archived there verbatim first.

The routine touches only `[ ]` (queued) items. `[~]` (in progress) and
`[x]` (complete) items are read-only.

### Procedure

1. **Inventory the queue.** Parse `TODO.md` for every `[ ]` item.
   For each, extract its named **elements** (whatever the domain's
   `concept_categories:` in `domain.md` calls them — interventions,
   techniques, doctrines, …), **targets** (the things acted on —
   systems, models, jurisdictions, …), and any **concept anchors** —
   phrases that name an existing wiki concept page or a shared umbrella
   across multiple items. The `Surfaced by` provenance attached to
   follow-on items is part of the extraction — it identifies which
   artifacts the item already cross-references.

2. **Scan `raw/` for closure candidates.** For each queued item, walk
   `raw/deep-research/`, `raw/sources/`, and `raw/notes/` and compute
   coverage against each artifact's `meta.yaml` (`elements:`, `targets:`)
   and `report.md` / `notes.md` body. Three confidence tiers:
   - **High confidence — auto-close.** Item's elements ⊆ artifact's
     elements, item's targets ⊆ artifact's targets, AND the artifact's
     body directly addresses the item's specific question (not merely
     adjacent to it). Flip `[ ]` → `[x]` and append `(<slug>)`. Write
     the closure to the archive (step 5).
   - **Partial — annotate, leave open.** Some overlap, but the item
     names a sub-question, angle, or methodology the artifact doesn't
     address. Append ` → partial coverage: ((slug))` to the item's
     text and leave it `[ ]`. Carries forward into clustering in step 3.
   - **No match.** Item stays untouched; eligible for clustering in step 3.

3. **Cluster the remaining `[ ]` items.** Pairwise similarity is judged
   on three axes: shared elements, shared targets, and shared concept
   anchors. Two items belong in the same cluster only if **a single,
   well-scoped deep-research session could close both without
   compromising the specificity of either question.** Heuristic
   threshold: ≥3 shared anchors OR both items explicitly name the same
   wiki concept page. Stop clustering at 8 sub-items per batch — beyond
   that the eventual research prompt becomes unwieldy; split into two
   sub-themed batches instead.

4. **Write the consolidated batch item.** For each cluster of size ≥2,
   replace the cluster's items in `TODO.md` with a single `[ ]` batch
   item of this shape:

   ```
   - [ ] **<Umbrella topic — concrete, not vague>** — <one-sentence framing of what the batch investigates and why these belong together>.
     - <Sub-question 1, verbatim from the original item, including its `Surfaced by` and any `→ partial coverage` annotation>.
     - <Sub-question 2, verbatim>.
     - …
   ```

   This is what "without losing uniqueness of the query" means in
   practice: the parent line gives the researcher the umbrella for
   scoping, and the sub-bullets preserve every original question's
   specific framing so the eventual research prompt covers every angle.
   The batch lands in the section most of its constituents belonged to;
   ties go to the section listed earliest in `TODO.md`. Standalone
   items (no cluster) stay in their original section, unchanged.

5. **Archive everything replaced.** Append to `TODO-batched-archive.md`
   under a dated heading (`## <yyyy-MM-dd> — R9 run`), with two
   subsections:

   ```
   ### Auto-closed
   - [original item text, verbatim] → ((slug)) — <one-line evidence statement>

   ### Batched
   #### Batch: <umbrella topic name>
   Rationale: <one-sentence why these clustered together>.
   Replaced items:
   - [original item text 1, verbatim with its `Surfaced by`]
   - [original item text 2, verbatim]
   - …
   ```

   Recovery is by copy-paste from this file if a closure or batch is
   judged wrong on review.

6. **Journal the run.** Add to today's `wiki/journals/<today>.md` under
   `### R9 ran`:
   - Counts: items auto-closed, clusters formed, items consolidated into
     batches, partial-coverage annotations added.
   - Patterns worth flagging to the operator (e.g., "5 closed items all
     cite the same source — consider promoting it to its own concept
     page", or "the X cluster has reached 11 sub-items across two
     batches — the concept is ready for a dedicated wiki page in the
     next synthesis pass").

### Safety rails

- **Never delete without archiving.** Every replaced or auto-closed
  item appears verbatim in `TODO-batched-archive.md` before `TODO.md`
  is rewritten. If the archive write fails, abort the rewrite.
- **Auto-close requires a resolvable `((slug))`.** The cited artifact
  folder must exist in `raw/` at run time. Citations that don't resolve
  downgrade the item to partial-match (annotation only).
- **No vague umbrellas.** If the best name for a cluster's umbrella
  would be generic ("various methodology items", "miscellaneous open
  questions"), the cluster is too broad. Leave the items separate; let
  them cluster more sharply on a future run as the corpus grows.
- **`[~]` and `[x]` are read-only.** R9 never modifies an item that
  isn't `[ ]`. In-progress and completed items are out of scope.
- **Provenance is preserved.** Every sub-bullet in a batch carries the
  original item's `Surfaced by` reference. This is the chain that
  lets the next investigator reconstruct why this question is on the
  list at all.

### When to skip an item

The follow-on items surfaced by prior investigations (queued at the
bottom of `TODO.md` per `AGENTS.md` §10.1 step 8) are the volatile
zone where this routine does most of its work. Items in the
operator-curated top sections of `TODO.md` should cluster more
conservatively: only batch them with each other, and prefer leaving
them standalone unless two clearly name the same umbrella concept.

---

## Cadence

If the operator wants this running on a schedule, the recommended
rhythm is:

- **Once** (first session or topic change): R0.
- **Daily:** R9 (consolidate-todo) — runs ahead of any research session so the queue it picks from is already closed-out and batched.
- **Weekly:** R6 (improve-wiki), R7 (knowledge-graph), and the
  weekly synthesis pass described in `AGENTS.md` §10.4.
- **On any new artifact batch:** R3 per artifact; full concept-page
  synthesis (R4) batches into the weekly pass.
- **On operator request:** R1, R2, R5, R9, and "run the loop" (which
  consumes `TODO.md` per `AGENTS.md` §10).
- **As-needed (≥2× manual pain):** R8 to convert recurring chores
  into scripts.

---

## Self-improvement directive

This program is allowed (and encouraged) to edit itself. When a
routine proves awkward, ambiguous, or insufficient in practice, the
agent should:

1. Note the friction in a journal entry under `### program.md feedback`.
2. Propose a concrete edit to this file.
3. Apply the edit only after the operator confirms, OR after the same
   friction appears three times across sessions — patterns matter more
   than one-offs.

Likewise, if a useful script emerges (a domain-source search wrapper,
a tag re-clusterer, a Logseq query generator), it goes in `scripts/`
and gets named in the relevant routine here. We will not pre-write
scripts on speculation; we'll write them when the manual version has
been done at least twice and the pattern is obvious.
