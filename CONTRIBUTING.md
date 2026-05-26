# Contributing

Researcher-Brain grows in two directions, and you can push on either.

---

## 1. Show what you built

The fastest, highest-signal contribution: **put your live research brain on the
map.** If you've cloned the template and started growing a real corpus — even a
small one — add it to the [Examples table](./README.md#examples).

1. Fork this repo.
2. Add one row to the table under **Examples** in `README.md`:
   ```
   | [Project name](https://github.com/you/your-repo) | [@you](https://github.com/you) | your domain | one-line description |
   ```
3. Open a pull request titled `showcase: <project name>`.

What makes a good showcase entry:

- A **public** repo that is a recognizable Researcher-Brain — a populated
  `wiki/` and `raw/`, and a `domain.md`.
- It's **live**: the corpus is actually being grown, not an empty clone.
- The description is **one line**. Let the repo speak for itself.

No corpus is too niche. A 30-page wiki on a topic you actually care about is
more useful to a newcomer than a polished-but-abstract demo.

---

## 2. Improve the template

The template itself — the routines, the conventions, the scaffolding — is open
to contributions. Good places to help:

- **Routines** (`program.md`, `AGENTS.md`) — sharper procedures, a new routine
  that earns its place in the catalog, clearer loop mechanics.
- **`domain.md.example`** — a well-filled example for a field that isn't yet
  represented (it currently ships medicine, ML, and history).
- **Scripts** (`scripts/`) — tools that follow the R8 contract: dry-run by
  default, stdlib + ripgrep only unless justified, documented in the catalog.
- **Docs** — anything that makes the first hour with the template clearer.

Ground rules that keep the template coherent:

- **Stay tool-agnostic.** Plain markdown, stable slugs, no hard dependency on
  any single app. The corpus must outlive any tool.
- **Every routine lives in `program.md`.** `AGENTS.md` describes principles;
  `program.md` describes the steps. New behavior gets documented in both.
- **Provenance stays first-class.** Don't add anything that lets a claim lose
  track of where it came from.
- **No fabricated sources.** Every citation resolves to something that exists.
- **Match the voice.** Direct, concrete, no hype.

### Opening a PR

1. Fork and branch: `git checkout -b your-feature`.
2. Make the change. Keep PRs focused — one idea per PR.
3. Open the PR with a short note on *why*, not just *what*.

---

## Questions

Open an issue, or reach the maintainer at **[virion.ai/initiate](https://virion.ai/initiate)**.
