---
title: Wiki Schema
created: 2026-04-30
updated: 2026-04-30
type: meta
tags: [meta]
---

# Wiki Schema

## Domain
AI/ML research — models, architectures, training techniques, papers, labs, and emerging technologies.

## Conventions
- File names: lowercase, hyphens, no spaces (e.g., `transformer-architecture.md`)
- Every wiki page starts with YAML frontmatter (see below)
- Use `[[wikilinks]]` to link between pages (minimum 2 outbound links per page)
- When updating a page, always bump the `updated` date
- Every new page must be added to `index.md` under the correct section
- Every action must be appended to `log.md`
- **Provenance markers:** On pages that synthesize 3+ sources, append `^[raw/articles/source-file.md]`
  at the end of paragraphs whose claims come from a specific source.
- **Immutable raw/:** Files in `raw/` are never modified. Corrections go in wiki pages.

## Frontmatter
```yaml
---
title: Page Title
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity | concept | comparison | query | summary | meta
tags: [from taxonomy below]
sources: [raw/articles/source-name.md]
# Optional quality signals:
confidence: high | medium | low
contested: true
contradictions: [other-page-slug]
---
```

## Tag Taxonomy
[Define 10-20 top-level tags. Add new tags here BEFORE using them.]

- **Models:** model, architecture, benchmark, training
- **People/Orgs:** person, company, lab, open-source
- **Techniques:** optimization, fine-tuning, inference, alignment, data
- **Meta:** comparison, timeline, controversy, prediction
- **Raw sources:** articles, papers, transcripts, assets

## Page Thresholds
- **Create a page** when an entity/concept appears in 2+ sources OR is central to one source
- **Add to existing page** when a source mentions something already covered
- **DON'T create a page** for passing mentions, minor details, or things outside the domain
- **Split a page** when it exceeds ~200 lines
- **Archive a page** when fully superseded — move to `_archive/`, remove from index

## Update Policy
When new information conflicts with existing content:
1. Check the dates — newer sources generally supersede older ones
2. If genuinely contradictory, note both positions with dates and sources
3. Mark in frontmatter: `contradictions: [page-name]`
4. Flag for review in the lint report
