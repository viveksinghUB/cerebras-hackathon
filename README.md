# Cerebras Hackathon Wiki

> An LLM-maintained knowledge base about Cerebras: their wafer-scale AI hardware, software ecosystem, models, and research.

This repository follows the [LLM Wiki pattern](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) by Andrej Karpathy — a three-layer architecture where raw sources are transformed into a structured, interlinked wiki that an AI agent maintains.

## What's inside

```
cerebras-hackathon/
├── CLAUDE.md          # Schema — tells the agent how to maintain the wiki
├── README.md          # This file
├── sources/           # Raw materials (papers, docs, links) — immutable
├── wiki/              # Derived markdown — agent-maintained
│   ├── index.md           curated entry points
│   ├── overview.md        narrative tour
│   ├── log.md             change log
│   ├── concepts/          ideas and technologies
│   ├── entities/          companies, products, models, papers
│   ├── syntheses/         multi-source essays
│   ├── comparisons/       side-by-side analyses
│   └── questions/         open questions
└── derived/           # Optional: charts, exports
```

## How to use this wiki

### Reading
- **Start with [`wiki/index.md`](wiki/index.md)** for curated entry points
- **Browse [`wiki/overview.md`](wiki/overview.md)** for a narrative tour
- **Check [`wiki/log.md`](wiki/log.md)** to see what's new
- Open in [Obsidian](https://obsidian.md) for a graph view of connections

### Contributing
1. Drop source materials into `sources/` (PDFs, markdown clippings, links)
2. Ask the agent to `ingest` the new sources
3. The agent reads, synthesizes, and updates the wiki
4. Review changes in `wiki/log.md`

### Agent operations
- **`ingest`** — process new sources, create/update wiki pages
- **`query`** — answer questions from the wiki
- **`lint`** — check for broken links, orphan pages, missing citations
- **`recompile`** — rebuild the entire wiki from sources

## Sources being ingested

This wiki is built from:
- **Website:** https://www.cerebras.net/
- **GitHub:** https://github.com/Cerebras/modelzoo
- **Models:** https://huggingface.co/cerebras
- **AI Model Studio:** https://www.cerebras.net/product-cloud/
- **Papers:** https://www.cerebras.net/publications/

## Schema

The wiki structure and agent behavior are defined in [`CLAUDE.md`](CLAUDE.md). This file tells the agent:
- How to organize pages (concepts, entities, syntheses, comparisons, questions)
- What templates to use
- How to handle citations and cross-references
- Style rules and conventions

## License

MIT
