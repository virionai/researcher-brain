# Convergence

> Why Researcher-Brain exists. The [README](./README.md) has the four-line
> version; this is the argument it was distilled from.

In three months of 2026, three events landed in three different domains —
autonomous ML research, personal knowledge management, and pure mathematics.
Laid side by side, they describe the same shape. This is that story.
<a href="./images/researcher=brain.img"><img src="https://github.com/virionai/researcher-brain/blob/main/images/convergence.png?raw=true" alt="a trio of knowledge"></a>

---

## March 2026 — the loop

Andrej Karpathy released `autoresearch` on GitHub — a coding agent pointed at
a small ML training setup, told to read the training code, propose a change,
run a 5-minute training job, measure whether the result improved, commit the
change if it did, roll it back if it didn't, and repeat. His own two-day run
produced 700 experiments and stacked twenty additive improvements that dropped
a "Time to GPT-2" benchmark from 2.02 hours to 1.80. The repo crossed 66,000
stars in a month. Fortune called the underlying methodology **"the Karpathy
Loop."** Forks of the pattern have since landed in domains far from ML.

## April 2026 — the layers

Karpathy posted about a parallel shift in his personal workflow: he had largely
stopped using LLMs to write code and started using them to organize knowledge.
He described a three-layer system: a folder of immutable raw sources, an
AI-maintained wiki of summaries and concept pages with backlinks between
related ideas, and a small schema file that tells the AI how the wiki is
supposed to be organized. His pithy version of why this works: *humans abandon
wikis because the maintenance burden grows faster than the value; LLMs don't
get bored, don't forget to update a cross-reference, and can touch fifteen
files in one pass.* On a single topic, his wiki had grown to roughly a hundred
articles and four hundred thousand words — past the size where the AI could
answer non-trivial questions about the corpus with very little extra work.

## May 2026 — the proof

OpenAI announced that a general-purpose reasoning model had produced an original
proof disproving an 80-year-old conjecture in discrete geometry — the unit
distance problem, posed by Erdős in 1946. The proof ran hundreds of pages. It
was independently verified by a group of mathematicians who published a
companion paper explaining the argument and its significance. What made the
result land was not the specific theorem; it was that *the model was not
fine-tuned for math, not scaffolded toward this problem, and not aimed at this
conjecture.* A general reasoning system, asked to think hard, advanced a field.

---

## The thesis

Lay those three events next to each other and a thesis falls out:

> The Karpathy Loop — propose, run, measure, commit-or-rollback, repeat — turns
> out to be the same shape whether you're optimizing training code or growing a
> knowledge base. Karpathy demonstrated it twice in 2026, once in each domain.
> And once you have a corpus structured well enough to run the loop on it, the
> bottleneck stops being "can the AI reason about this" and starts being "is my
> knowledge structured well enough that the AI can reason *with* me on it." The
> answer to the second question is almost always no, because knowledge bases
> have historically been built to be read by humans, not collaborated on by
> humans and models together. OpenAI's Erdős result showed what a general
> reasoner can produce when the structure is right.

**Researcher-Brain is that structure.**

---

## The bet

Here is the bet the design makes: in a year where a general reasoning model can
disprove an 80-year-old Erdős conjecture, where the same person who taught a
generation of practitioners how to train neural networks has now shown the same
propose-run-measure loop works for code optimization *and* for knowledge
curation, and where the public-facing builder community has independently
converged on a three-layer architecture for collaborating with those models —
the highest-leverage thing a curious person can do is build the corpus *their*
thinking will compound on, and let the loop run on it.

Most people won't. The few who do will find that the things their corpus shows
them are not summaries of what's known but pointers at what isn't.

That's what this template is for.

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

---

## Credits and influence

- **Andrej Karpathy** — twice over. The March 2026 `autoresearch` release
  showed that a coding agent in a tight loop can produce real, stacking
  improvements; Fortune called the methodology **"the Karpathy Loop."** The
  April 2026 second-brain post crystallized the three-layer pattern for
  AI-maintained wikis and contributed the *"LLMs don't get bored"* observation
  that justifies them in the first place. This template combines both —
  Karpathy's loop applied to Karpathy's three layers.
- **OpenAI** — the May 2026 unit-distance / Erdős announcement that proved a
  general reasoning model, given the right structure, can contribute novel
  knowledge rather than merely retrieve it.
- **Tiago Forte** — *Building a Second Brain* (2022), the original CODE
  framework (Capture, Organize, Distill, Express). This template is essentially
  CODE for the multi-agent era.
- **Logseq** — the outliner-first knowledge graph this wiki targets. Plain
  markdown so the corpus outlives any single tool.
- **The DOI, PMID, arXiv, ISBN, RFC, and case-docket systems** — every stable
  identifier this template uses already existed. The template's contribution is
  treating them as first-class slugs and refusing to ever reorganize them.
