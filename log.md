---
title: Wiki Log
created: 2026-04-30
updated: 2026-07-13
type: meta
tags: [meta]
---

# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive, delete
> When this file exceeds 500 entries, rotate: rename to `log-YYYY.md`, start fresh.

## [2026-07-13] ingest | Mamba Architecture Research — batch
- Domain: Mamba SSM family (architecture research for CS7643 ablation project)
- raws created: 4 papers
  - raw/papers/mamba-2023.md — Mamba-1 (Gu & Dao, 2023, COLM 2024)
  - raw/papers/mamba2-2024.md — Mamba-2 / SSD (Dao & Gu, ICML 2024)
  - raw/papers/mamba3-2026.md — Mamba-3 / MIMO (Lahoti et al., ICLR 2026)
  - raw/papers/jamba-2024.md — Jamba hybrid (AI21, ICLR 2025)
- pages created: 5
  - concepts/state-space-models.md — SSM fundamentals + evolution
  - concepts/mamba-architecture.md — Mamba-1/2/3 full architecture
  - concepts/jamba-hybrid.md — Jamba hybrid Transformer-Mamba
  - concepts/vision-mamba.md — Vision Mamba applications
  - comparisons/mamba-vs-transformer.md — Head-to-head comparison
- index.md: updated (0→6 pages)
- Topics covered: selective SSM, SSD duality, MIMO SSMs, linear-time training, constant-memory inference

## [2026-04-30] create | Wiki initialized
- Domain: AI/ML research
- Structure created: raw/{articles,papers,transcripts,assets}, entities/, concepts/, comparisons/, queries/, _archive/
- SCHEMA.md, index.md, log.md created
- Vault path: ~/Obsidian_Vault_Xing
- WIKI_PATH configured in ~/.hermes/.env
- post-commit hook: auto push to GitHub on every commit
