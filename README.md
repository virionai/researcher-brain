# Researcher-Brain

> A second brain for research — built for the era when AI doesn't just
> retrieve knowledge, it produces it.

---

## Three events that make this template worth setting up properly

**March 2026.** Andrej Karpathy released `autoresearch` on GitHub — a
coding agent pointed at a small ML training setup, told to read the
training code, propose a change, run a 5-minute training job, measure
whether the result improved, commit the change if it did, roll it back
if it didn't, and repeat. His own two-day run produced 700 experiments
and stacked twenty additive improvements that dropped a "Time to
GPT-2" benchmark from 2.02 hours to 1.80. The repo crossed 66,000
stars in a month. Fortune called the underlying methodology
**"the Karpathy Loop."** Forks of the pattern have since landed in
domains far from ML.

**April 2026.** Karpathy posted about a parallel shift in his personal
workflow: he had largely stopped using LLMs to write code and started
using them to organize knowledge. He described a three-layer system: a
folder of immutable raw sources, an AI-maintained wiki of summaries
and concept pages with backlinks between related ideas, and a small
schema file that tells the AI how the wiki is supposed to be
organized. His pithy version of why this works: *humans abandon wikis
because the maintenance burden grows faster than the value; LLMs don't
get bored, don't forget to update a cross-reference, and can touch
fifteen files in one pass.* On a single topic, his wiki had grown to
roughly a hundred articles and four hundred thousand words — past the
size where the AI could answer non-trivial questions about the corpus
with very little extra work.

**May 2026.** OpenAI announced that a general-purpose reasoning model
had produced an original proof disproving an 80-year-old conjecture in
discrete geometry — the unit distance problem, posed by Erdős in 1946.
The proof ran hundreds of pages. It was independently verified by a
group of mathematicians who published a companion paper explaining the
argument and its significance. What made the result land was not the
specific theorem; it was that *the model was not fine-tuned for math,
not scaffolded toward this problem, and not aimed at this conjecture.*
A general reasoning system, asked to think hard, advanced a field.

Lay those three events next to each other and a thesis falls out:

> The Karpathy Loop — propose, run, measure, commit-or-rollback,
> repeat — turns out to be the same shape whether you're optimizing
> training code or growing a knowledge base. Karpathy demonstrated it
> twice in 2026, once in each domain. And once you have a corpus
> structured well enough to run the loop on it, the bottleneck stops
> being "can the AI reason about this" and starts being "is my
> knowledge structured well enough that the AI can reason *with* me
> on it." The answer to the second question is almost always no,
> because knowledge bases have historically been built to be read by
> humans, not collaborated on by humans and models together. OpenAI's
> Erdős result showed what a general reasoner can produce when the
> structure is right.

**This template is that structure.**

---

## What this is

`Researcher-Brain` is a generic, reusable scaffold for building a
collaborative research wiki on any topic — medicine, ML, history,
law, design, cooking, anything where evidence accumulates and
adjacencies matter. It assumes you'll work with it the way Karpathy
described his system: drop in raw material, ask an AI agent to read,
organize, summarize, cross-link, and surface things you wouldn't see
alone.

The skeleton is three folders and three documents:

```
Researcher-Brain/
├── AGENTS.md              ← how any AI agent should behave in here
├── program.md             ← named routines (R0–R8) the agent runs
├── domain.md              ← (produced by R0) customization for YOUR topic
├── raw/                   ← primary evidence, immutable, slug-addressed
│   ├── sources/           ← published primary sources (papers, books,
│   │                        RFCs, case law, archives, official filings, …)
│   ├── images/            ← figures, diagrams, scans, screenshots
│   ├── deep-research/     ← long-form AI investigations (Claude, GPT, Gemini)
│   └── notes/             ← human contributions
├── wiki/                  ← Logseq graph, AI-maintained
│   ├── pages/             ← concept pages with [[wiki-links]] and ((slug-cites))
│   ├── journals/          ← daily research log
│   └── assets/
└── output/                ← deliverables answering questions you actually asked
```

The three layers map cleanly onto Karpathy's:

| Karpathy's layer       | Here                                           | Why                                                                |
| ---------------------- | ---------------------------------------------- | ------------------------------------------------------------------ |
| Raw sources (immutable) | `raw/` (flat, slug-addressed, never reorganized) | Stable IDs let citations from anywhere keep working forever.       |
| AI-maintained wiki      | `wiki/` (Logseq markdown graph)                | Plain markdown so it survives the death of any single tool.        |
| Schema / conventions    | `AGENTS.md` + `program.md` + `domain.md`       | Three files because conventions, routines, and topic differ.       |

---

## What makes this different from "just a wiki"

A normal personal wiki is a place a single mind puts things. This one
is built to be a workbench for multi-source collaboration — your
notes, multiple AIs' deep-research reports, primary sources, and
images, all carrying enough provenance that you can ask the question
that actually matters at 2 a.m.: *where did this claim come from, and
how confident should I be?*

Six design choices that earn their keep:

**Provenance is first-class.** Every artifact in `raw/` declares its
type, its source, and — for AI-generated investigations — the verbatim
prompt that produced it. A deep-research report from Claude whose
citations don't resolve to primary sources we actually hold gets
downgraded to a hypothesis. A note from a human collaborator carries
the human's name and the context in which they wrote it. The wiki
makes these gradients visible.

**Slug-addressed evidence, never reorganized.** Every artifact gets a
stable slug (`doi-10.1038-s41586-023-12345`, `isbn-9780262035613`,
`rfc-9110`, `claude-2026-05-25-attention-mechanisms`,
`2026-05-25-josh-observation-x`). The wiki cites slugs. Slugs don't
move. The wiki can be reorganized freely without ever breaking a
citation. This is the difference between "I'll fix the links later"
and a corpus that compounds for years.

**The loop is built in, not bolted on.** `R0: init` produces a
`TODO.md` of specific research questions in your topic. `AGENTS.md`
§10 describes the loop the agent walks: pick an item, mark it
in-progress with the slug it will become, produce a deep-research
artifact, land it in `raw/`, journal a one-line breadcrumb, close
the item, and feed any follow-on questions the investigation surfaced
back into the queue. This is the Karpathy Loop applied to knowledge
rather than to training code — propose, research, measure whether the
corpus is richer, commit the artifact, repeat. Concept-page synthesis
batches weekly (§10.4) so the synthesizer can spot adjacencies across
multiple new artifacts at once. Recurring chores graduate into
scripts via `R8: build-wiki-tool`. The loop is what makes a research
corpus grow without your having to push it.

**The wiki is allowed to be wrong, and the audit trail proves it.**
Routine `R6: improve-wiki` runs periodically. It re-clusters concepts,
merges tag synonyms, prunes dead stubs, and re-checks whether claims
backed only by deep-research or notes are still labeled as
hypotheses rather than findings. Wikis decay; this one is supposed to.

**Cross-domain probe (opt-in).** The most ambitious routine, `R2`,
walks the wiki graph looking for *under-explored adjacencies* —
concept pairs that bridge two sub-fields where the corpus suggests a
connection exists but no direct evidence has been cited. In medicine,
this is how you notice that probiotics keep showing up next to mood
disorders. In ML, that an architectural change keeps appearing
adjacent to a benchmark improvement nobody's claimed credit for. In
history, that a doctrine moved between jurisdictions a decade earlier
than the standard narrative says. This routine is the reason the
template exists.

**Outputs include their own confidence file.** Every deliverable in
`output/` includes a `confidence.md` stating what we actually know,
what's weak, and what would change the conclusion. Without this an
answer is a confident-sounding paragraph; with it, it's a position.

---

## R0: the init interview

The template is generic. The first time you (or an agent) open the
folder, you run `R0: init` — a six-question interview that produces
`domain.md`, the small schema file that customizes everything for
your actual topic.

The questions:

1. **What are you researching?** One or two sentences. The right
   scope is "a field where reasonable people would disagree about
   which sub-fields it contains."
2. **What kinds of things do you study?** 2–5 categories. Medicine
   has *interventions* and *physiological systems* and *conditions*;
   ML has *techniques* and *models* and *benchmarks*; law has
   *doctrines* and *cases* and *jurisdictions*.
3. **What sub-fields cut across this topic?** 3–8 specialties or
   schools where experts would disagree about who owns what.
4. **Where does the best primary evidence live?** 2–5 source
   databases / archives / repositories. PubMed, arXiv, JSTOR, an
   official archive, a specific RFC index — whichever applies.
5. **Cross-domain mode on or off?** On for genuinely
   multi-disciplinary topics; off for deep single-discipline dives.
6. **What's the first thing you want this system to help with?**
   Optional. Seeds the first real R1 or R5 after init.

The agent reads your answers back, lets you correct, writes
`domain.md`, and creates a stub `wiki/pages/_index.md` listing your
sub-fields and categories as empty pages. From that point on, every
other routine reads `domain.md` and behaves accordingly.

See `domain.md.example` for three filled-in versions across
medicine, ML, and history.

---

## The routine catalog

| Routine                       | What it does                                                                  |
| ----------------------------- | ----------------------------------------------------------------------------- |
| `R0: init`                    | The interview. Produces `domain.md`. Run once per project.                    |
| `R1: continue-on`             | Deepen coverage of a concept the operator already cares about.                |
| `R2: cross-domain-probe`*     | Find under-explored adjacencies between sub-fields. **The signature routine.** |
| `R3: ingest`                  | Slug, metadate, file, and index a new artifact (source / image / deep-research / note). |
| `R4: synthesize`              | Promote journal bullets into durable concept pages.                           |
| `R5: answer`                  | Produce an `output/<date>-<question-slug>/` deliverable with a confidence file. |
| `R6: improve-wiki`            | Tidy. Re-cluster concepts, normalize tags, audit provenance, prune stubs.     |
| `R7: knowledge-graph`         | Render the current concept graph; flag hubs, bridges, and isolates.           |
| `R8: build-wiki-tool`         | When a manual chore has recurred ≥2×, turn it into a script in `scripts/`. The only routine that produces code. |

\* R2 is gated on `cross_domain: true` in `domain.md`.

The full procedural detail for each routine lives in `program.md`.
The loop that consumes `TODO.md` is described in `AGENTS.md` §10.

---

## Quickstart

```bash
# 1. Clone or copy this template into a new folder for your topic.
cp -r Researcher-Brain MyResearchProject
cd MyResearchProject

# 2. Open the folder in your AI assistant (Claude, Cursor, Cowork, etc.).
#    Tell it: "Read AGENTS.md and run R0."

# 3. Answer the six R0 questions honestly. Don't pre-shape your topic
#    to sound impressive — the system pays off on precision, not breadth.

# 4. When R0 finishes, drop a few primary sources into raw/_inbox/
#    and say: "Run R3 on the inbox."

# 5. Open TODO.md. R0 seeded it with sections derived from your
#    concept_categories. Add a handful of specific items if you want
#    to steer the early corpus, or leave it and let the loop steer.

# 6. Say: "Run the loop." The agent picks a queued item, produces a
#    deep-research artifact in raw/, journals a breadcrumb, closes
#    the item, and adds any follow-on questions the investigation
#    surfaced back into TODO.md. This is the Karpathy Loop applied
#    to your topic. Let it run as long as you want.

# 7. Once you have ~10 artifacts in raw/, ask the agent your first
#    real question. It runs R5 and you get an answer with citations
#    and a confidence file.

# 8. After a few weeks, ask: "Run R2." If your domain.md has
#    cross_domain: true, the agent walks the graph and tells you
#    what adjacencies you've accidentally built evidence for without
#    naming them yet.
```

The first few weeks are slow. The compounding starts somewhere
between a hundred and three hundred artifacts — roughly where
Karpathy noticed his own wiki started returning more than it cost to
maintain.

---

## What this isn't

It isn't a tool to outsource your thinking. The wiki, the routines,
the cross-domain probe — all of them are designed to make *you* think
more carefully, not less. A confidence file forces you to admit what
you don't know. A cross-domain bridge surfaces a question; you still
have to answer it. A deep-research report from an AI is hypothesis
until its citations resolve.

It isn't a literature-review service. R1 deepens coverage, but the
goal is never to summarize a field exhaustively. The goal is to
surface what matters and (if you opted into R2) what's adjacent.

It isn't a substitute for domain expertise. You still have to know
what you're looking at. The system makes your expertise more
leveraged, not optional.

It isn't a place for material you don't have rights to. If a source
can't be stored, save `meta.yaml` and `notes.md` with the URL and
skip the artifact file.

---

## The bet

Here is the bet the design makes: in a year where a general
reasoning model can disprove an 80-year-old Erdős conjecture, where
the same person who taught a generation of practitioners how to
train neural networks has now shown the same propose-run-measure
loop works for code optimization *and* for knowledge curation, and
where the public-facing builder community has independently
converged on a three-layer architecture for collaborating with
those models — the highest-leverage thing a curious person can do
is build the corpus *their* thinking will compound on, and let the
loop run on it.

Most people won't. The few who do will find that the things their
corpus shows them are not summaries of what's known but pointers at
what isn't.

That's what this template is for. If you have questions or want to help improve the work feel free to submit a PR or contact me at virion.ai/initiate

---

## Credits and influence

- **Andrej Karpathy** — twice over. The March 2026 `autoresearch`
  release showed that a coding agent in a tight loop can produce
  real, stacking improvements; Fortune called the methodology
  **"the Karpathy Loop."** The April 2026 second-brain post
  crystallized the three-layer pattern for AI-maintained wikis and
  contributed the *"LLMs don't get bored"* observation that
  justifies them in the first place. This template combines both —
  Karpathy's loop applied to Karpathy's three layers.
- **OpenAI** — the May 2026 unit-distance / Erdős announcement that
  proved a general reasoning model, given the right structure, can
  contribute novel knowledge rather than merely retrieve it.
- **Tiago Forte** — *Building a Second Brain* (2022), the original
  CODE framework (Capture, Organize, Distill, Express). This template
  is essentially CODE for the multi-agent era.
- **Logseq** — the outliner-first knowledge graph this wiki targets.
  Plain markdown so the corpus outlives any single tool.
- **The DOI, PMID, arXiv, ISBN, RFC, and case-docket systems** —
  every stable identifier this template uses already existed. The
  template's contribution is treating them as first-class slugs and
  refusing to ever reorganize them.

---

## Sources

**Karpathy's `autoresearch` (March 2026)**

- [karpathy/autoresearch on GitHub](https://github.com/karpathy/autoresearch)
- [VentureBeat: Karpathy's new open source autoresearch lets you run hundreds of AI experiments a night](https://venturebeat.com/technology/andrej-karpathys-new-open-source-autoresearch-lets-you-run-hundreds-of-ai)
- [Fortune: Why everyone is talking about Karpathy's autonomous AI research agent ("The Karpathy Loop")](https://fortune.com/2026/03/17/andrej-karpathy-loop-autonomous-ai-agents-future/)
- [Shopify Engineering: Autoresearch isn't just for training models](https://shopify.engineering/autoresearch)
- [DataCamp: A Guide to Karpathy's AutoResearch](https://www.datacamp.com/tutorial/guide-to-autoresearch)

**Karpathy's second-brain pattern (April 2026)**

- [Karpathy's instructions for building an AI-driven second brain (Techstrong.ai)](https://techstrong.ai/features/karpathys-instructions-for-building-an-ai-driven-second-brain/)
- [Andrej Karpathy's LLM Wiki: Build a Self-Updating AI Second Brain with Obsidian in 1 Hour (MindStudio)](https://www.mindstudio.ai/blog/andrej-karpathy-llm-wiki-obsidian-ai-second-brain)
- [The Complete Guide to Karpathy's Second Brain (aibyaakash)](https://www.aibyaakash.com/p/karpathy-second-brain)

**OpenAI's Erdős unit-distance result (May 2026)**

- [OpenAI: Model disproves a discrete geometry conjecture](https://openai.com/index/model-disproves-discrete-geometry-conjecture/)
- [TechCrunch: OpenAI claims it solved an 80-year-old math problem — for real this time](https://techcrunch.com/2026/05/20/openai-claims-it-solved-an-80-year-old-math-problem-for-real-this-time/)
- [Scientific American: AI just solved an 80-year-old Erdős problem and mathematicians are amazed](https://www.scientificamerican.com/article/ai-just-solved-an-80-year-old-erdos-problem-and-mathematicians-are-amazed/)
