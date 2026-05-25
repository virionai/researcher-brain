# output/

Deliverables generated to answer the operator's questions.

## Layout

One folder per question, named `yyyy-MM-dd-<question-slug>/`:

```
output/2026-05-25-some-question/
├── question.md     ← the question, verbatim
├── answer.md       ← the synthesized answer with inline citations
├── sources.md      ← every artifact slug cited, with one-line summaries
└── confidence.md   ← what we know, what's weak, what's missing
```

`confidence.md` is non-negotiable. Every answer states the evidence
behind it (per artifact-type weighting from `AGENTS.md` §8), where the
evidence is thin, and what would change the conclusion. Without it, an
answer is just a confident-sounding paragraph.

## Procedure

See `program.md` → **R5: answer**. The output draws from `wiki/`; the
wiki draws from `raw/`. An attentive reader should be able to follow
any claim in `answer.md` back to an artifact on disk.
