---
title: Wiki Log
created: 2026-04-30
updated: 2026-04-30
type: meta
tags: [meta]
---

# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: ingest, update, query, lint, create, archive, delete
> When this file exceeds 500 entries, rotate: rename to `log-YYYY.md`, start fresh.

## [2026-04-30] create | Wiki initialized
- Domain: AI/ML research
- Structure created: raw/{articles,papers,transcripts,assets}, entities/, concepts/, comparisons/, queries/, _archive/
- SCHEMA.md, index.md, log.md created
- Vault path: ~/Obsidian_Vault_Xing
- WIKI_PATH configured in ~/.hermes/.env
- post-commit hook: auto push to GitHub on every commit
