# Researcher-Brain

> A second brain for research — built for the era when AI doesn't just
> retrieve knowledge, it produces it.

---

## Two events that make this template worth setting up properly

**April 2026.** Andrej Karpathy posted that he had largely stopped using
LLMs to write code and started using them to organize knowledge. He
described a three-layer system: a folder of immutable raw sources, an
AI-maintained wiki of summaries and concept pages with backlinks
between related ideas, and a small schema file that tells the AI how
the wiki is supposed to be organized. His pithy version of why this
works: *humans abandon wikis because the maintenance burden grows
faster than the value; LLMs don't get bored, don't forget to update a
cross-reference, and can touch fifteen files in one pass.* On a single
topic, his wiki had grown to roughly a hundred articles and four
hundred thousand words — past the size where the AI could answer
non-trivial questions about the corpus with very little extra work.

**May 2026.** OpenAI announced that a general-purpose reasoning model
had produced an original proof disproving an 80-year-old conjecture in
discrete geometry — the unit distance problem, posed by Erdős in 1946.
The proof ran hundreds of pages. It was independently verified by a
group of mathematicians who published a companion paper explaining the
argument and its significance. What made the result land was not the
specific theorem; it was that *the model was not fine-tuned for math,
not scaffolded toward this problem, and not aimed at this conjecture.*
A general reasoning system, asked to think hard, advanced a field.

Put those two events next to each other and a thesis falls out:

> The bottleneck for serious thinking is no longer "can an AI help me
> reason about this." It's "is my knowledge structured well enough
> that an AI can reason *with* me on it." The answer to the second
> question is almost always no, and the reason is that knowledge bases
> have historically been built to be read by humans, not collaborated
> on by humans and models together.

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
├── program.md             ← named routines (R0–R7) the agent runs
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

Five design choices that earn their keep:

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

\* R2 is gated on `cross_domain: true` in `domain.md`.

The full procedural detail for each routine lives in `program.md`.

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

# 5. Once you have ~10 artifacts in raw/, ask the agent your first
#    real question. It runs R5 and you get an answer with citations
#    and a confidence file.

# 6. After a few weeks, ask: "Run R2." If your domain.md has
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
reasoning model can disprove an 80-year-old Erdős conjecture, and
where the public-facing builder community has independently
converged on a three-layer architecture for collaborating with
those models, the highest-leverage thing a curious person can do
is build the corpus *their* thinking will compound on. Most people
won't. The few who do will find that the things their corpus shows
them are not summaries of what's known but pointers at what isn't.

That's what this template is for.

---

## Credits and influence

- **Andrej Karpathy** — the April 2026 second-brain post that crystallized
  the three-layer pattern for many of us, and the
  *"LLMs don't get bored"* observation that justifies AI-maintained wikis
  in the first place.
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

- [OpenAI: Model disproves a discrete geometry conjecture (May 2026)](https://openai.com/index/model-disproves-discrete-geometry-conjecture/)
- [TechCrunch: OpenAI claims it solved an 80-year-old math problem — for real this time](https://techcrunch.com/2026/05/20/openai-claims-it-solved-an-80-year-old-math-problem-for-real-this-time/)
- [Scientific American: AI just solved an 80-year-old Erdős problem and mathematicians are amazed](https://www.scientificamerican.com/article/ai-just-solved-an-80-year-old-erdos-problem-and-mathematicians-are-amazed/)
- [Karpathy's instructions for building an AI-driven second brain (Techstrong.ai)](https://techstrong.ai/features/karpathys-instructions-for-building-an-ai-driven-second-brain/)
- [Andrej Karpathy's LLM Wiki: Build a Self-Updating AI Second Brain with Obsidian in 1 Hour (MindStudio)](https://www.mindstudio.ai/blog/andrej-karpathy-llm-wiki-obsidian-ai-second-brain)
- [The Complete Guide to Karpathy's Second Brain (aibyaakash)](https://www.aibyaakash.com/p/karpathy-second-brain)
