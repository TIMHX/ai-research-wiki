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

## [2026-08-25] create | CS6475 Computational Photography course page
- Domain: OMSCS Fall 2026 course onboarding
- pages created: concepts/cs6475-computational-photography.md — course policies, Fall 2026 schedule, tech topics, links
- index.md: updated (6→7 pages)
- 详细操作信息在 skill cs6475-computational-photography；tickets 在 github.com/TIMHX/CS6475 issues

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

## 2026-09-10

- Pages created: 1
  - comparisons/rerank-score-calibration.md（四个 rerank 模型在同一中文 query 上排序全部正确，
    但噪音项绝对分数从 0.006 到 0.278 不等，jina-reranker-v3 直接输出负 logit——默认 threshold=0.1
    会把结果全丢弃。结论：选 rerank 要看噪音落点而非排序指标，版本更新不等于更适用。
    另记 MiniMax-M3 的 <think> 只能用 `"thinking":{"type":"disabled"}` 关闭，M2.7 完全无视该参数）
