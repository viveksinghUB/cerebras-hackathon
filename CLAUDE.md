# CLAUDE.md — LLM Wiki Schema

> This file is the **schema**. It tells the agent how this repo is
> laid out, what files mean, and which workflows to run. Humans read
> the wiki; the agent maintains it.
> Pattern adapted from
> [Andrej Karpathy's *llm-wiki* gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).

## TL;DR for the agent

> *"You are the maintainer of a wiki about Cerebras: their AI hardware, software, models, research papers, and ecosystem. This wiki documents Cerebras technology, capabilities, and best practices."*

The user curates raw material into `sources/`. You compile it into a
clean, interlinked set of pages under `wiki/`. You never modify
`sources/`. You always update `wiki/log.md`.

## Three layers

```
<vault root>/
├── CLAUDE.md       # Layer 3 — schema (this file)
├── README.md       # human entry point
├── sources/        # Layer 1 — raw, immutable inputs (the user owns this)
├── wiki/           # Layer 2 — derived markdown (you own this)
│   ├── index.md
│   ├── overview.md
│   ├── log.md
│   ├── concepts/   # ideas, techniques, terms
│   ├── entities/   # people, orgs, models, products, papers, datasets
│   ├── syntheses/  # multi-source thematic write-ups
│   ├── comparisons/# side-by-side: X vs. Y
│   └── questions/  # open questions, FAQ-style entries
└── derived/        # optional: charts, slides, exports built from wiki/
```

### Layer 1 — `sources/`  (immutable)
- The user drops in PDFs, papers, screenshots, transcripts, blog
  dumps, notebooks, links collected as `.md` clippings, etc.
- **You read but never write here.** If a source needs cleanup, copy
  it, don't mutate it.
- Each source should ideally have a sibling `*.meta.md` with: title,
  author, url, date, why-it-matters. If missing, create it on first
  ingest.

### Layer 2 — `wiki/`  (the wiki — you own it)
- One concept, entity, synthesis, comparison, or question per file.
- File names: `kebab-case.md` (e.g. `wafer-scale-engine.md`).
- Cross-link liberally with **wiki-style links**:
  `[[wafer-scale-engine]]`, `[[entities/cerebras]]`. Every page should
  link out to ≥ 2 others.
- Every page must end with a `## Sources` section listing the
  `sources/` files (or external URLs) it draws from.

### Layer 3 — `CLAUDE.md`  (the schema)
- This file. Update it when conventions evolve. Treat changes here
  as schema migrations — note them in `wiki/log.md`.

## Page templates

Pages are written for *humans first* and agents second. Every page
opens with a one-sentence summary as a markdown blockquote (`> …`),
followed by structured prose, and closes with a `## Continue reading`
footer that points the reader at 2–3 curated next pages. Wiki-links
use `[[wiki-link]]` syntax; every page has a `## Sources` section.

### Concept page (`wiki/concepts/<slug>.md`)
```markdown
# <Concept Name>

> <single-sentence summary that doubles as a tagline>

## What it is
<2–6 sentence explanation, plain English first>

## Why it matters
<the practical or theoretical consequence>

## Key ideas
- bullet
- bullet

## Related
- [[concepts/<other>]]
- [[entities/<paper-or-person>]]

## Open questions
- ...

## Sources
- [[sources/<file>]] — <one-line note>

## Continue reading
- **<short reader-facing label>** → [[<target-page>]]
- **<short reader-facing label>** → [[<target-page>]]
```

### Entity page (`wiki/entities/<slug>.md`)
```markdown
# <Entity Name>

> <single-sentence summary including type if useful>

## Summary
<who/what, 3–8 sentences>

## Key facts
- founded / released / authored: ...
- affiliation: ...
- notable for: ...

## Timeline
- YYYY-MM — event

## Related
- [[entities/...]]
- [[concepts/...]]

## Sources
- ...

## Continue reading
- **<label>** → [[<target>]]
- **<label>** → [[<target>]]
```

### Synthesis (`wiki/syntheses/<slug>.md`)
A short essay (300–800 words) that pulls from ≥ 2 sources to argue a
non-obvious point or summarize a thread of work. Open with a
blockquote tagline; must cite each source; end with
`## Continue reading`.

### Comparison (`wiki/comparisons/<slug>.md`)
Use a table. Always include columns for: dimension, A, B,
who-wins-when, sources. Open with a blockquote tagline; below the
table, a 1-paragraph "bottom line"; end with `## Continue reading`.

### Question (`wiki/questions/<slug>.md`)
Question pages keep an explicit `**Status:**` line — readers care
whether something is open or resolved.

```markdown
# <The Question>

**Status:** open | partially-answered | resolved

## Why I'm asking
...

## Current best answer
...

## Evidence for
- ...
## Evidence against
- ...

## Related
- ...

## Sources
- ...

## Continue reading
- **<label>** → [[<target>]]
- **<label>** → [[<target>]]
```

## Workflows

### `ingest` — new file appeared in `sources/`
1. Read the source. If it has no `*.meta.md` sibling, generate one.
2. Decide: does it primarily extend an **existing page** or warrant a
   **new** one?
   - "New" if: a new concept, entity, paper, or model not covered yet.
   - "Extend" if: deepens / contradicts / dates an existing page.
3. Apply the change. Keep prose tight; prefer linking to duplicating.
4. Add a one-line entry to `wiki/log.md` (date, change, source touched).
5. Update `wiki/index.md` if a new top-level page was created.

### `query` — user asks a question
1. Search `wiki/` first. If the answer exists, cite the page(s).
2. If partial, answer from the wiki and flag gaps as `wiki/questions/`
   entries (status: open).
3. If absent, search `sources/`. If found there but not yet on the
   wiki, answer **and** queue a "promote-to-wiki" item in
   `wiki/log.md`.
4. Always cite. Inline links:
   `[wafer-scale-engine](concepts/wafer-scale-engine.md)`.

### `lint` — periodic hygiene pass
- Every page has: H1, blockquote one-liner, ≥ 2 outgoing links, a
  `## Sources` section, and a `## Continue reading` footer (questions
  also keep `**Status:**`).
- No orphan pages: every page is reachable from `index.md` in ≤ 2 hops.
- No dead `[[wiki-links]]`: target file must exist.
- File names are `kebab-case.md`; no spaces, no upper-case.
- `wiki/log.md` is append-only and dated.
- Stubs older than 30 days get a `**TODO:**` banner.

### `recompile` — rebuild from scratch
- Treat `wiki/` as ephemeral. Delete it, then re-derive every page
  from `sources/` + `CLAUDE.md`. Useful as a sanity check.

### `capture` — ingest conversation transcripts from the queue
A Stop hook appends each session's transcript path to
`sources/conversation-queue.md` (format: `YYYY-MM-DD HH:MM | /path/to/transcript.jsonl`).

At the **start of each session**, check whether `sources/conversation-queue.md`
exists and has unprocessed entries (lines not marked `✓`):
1. Read each unprocessed transcript (JSONL — each line is a message object with
   `role` and `content` fields).
2. Extract wiki-worthy knowledge: new concepts, decisions, model recommendations,
   hardware constraints, tool comparisons, or any factual claim not yet in `wiki/`.
3. Run the normal `ingest` workflow for each piece of new knowledge.
4. Mark the processed line with `✓` at the end: `2026-06-17 15:48 | /path ✓`

The user can also say **"capture"** at any point to trigger this immediately.

## Style rules

- Prefer **short, plain sentences**. Define jargon on first use.
- One idea per page. Split when a page grows beyond ~600 lines.
- Link, don't duplicate. If you'd repeat a definition, link instead.
- Cite everything. No claim without a source link.
- Mark uncertainty explicitly: *"as of YYYY-MM, …"*,
  *"unverified:"*.
- Disagreements between sources go into a `## Contradictions`
  section, not silently averaged.
- Dates: ISO `YYYY-MM-DD`.

## What NOT to do

- Don't edit anything in `sources/`.
- Don't create pages with no sources behind them. (Personal opinion
  is fine but must be marked `> Note (user):` and dated.)
- Don't let `wiki/index.md` become a wall of links — keep it
  curated.
- Don't invent citations.
