---
source_url: https://arxiv.org/abs/2403.19887
ingested: 2026-07-13
sha256: TBD
---

# Jamba: A Hybrid Transformer-Mamba Language Model

**Authors:** Opher Lieber, Barak Lenz, Hofit Bata, et al. (AI21 Labs)
**Published:** March 2024 (arXiv)
**Venue:** ICLR 2025

## Overview

Jamba is a production-scale hybrid architecture that **interleaves Transformer and Mamba layers**
within a Mixture-of-Experts (MoE) framework, achieving:
- Transformer-quality reasoning
- Mamba-level efficiency on long contexts
- 256K token context length (largest open-weight at release)

## Architecture

Jamba blocks are organized as:
```
[... Transformer block → Mamba block → Transformer block → Mamba block ...]
```

- **Jamba-1.5-Large:** 94B active params, 398B total (MoE)
- **Jamba-1.5-Mini:** 12B active params, 52B total
- **Expert ratio:** 1:8, each token routes to top-2 experts
- **ExpertsInt8:** Novel quantization that fits 94B model on 8×80GB GPUs at 256K context

## Key Findings

1. **Hybrid > Pure:** The interleaved architecture outperforms pure Mamba or pure Transformer
   at equal parameter counts.
2. **Layer ordering matters:** Mamba layers alone can't handle certain tasks (in-context
   learning, copying), so Transformer layers are strategically placed.
3. **MoE integration:** Each expert can be either an attention FFN or SSM-based — the router
   learns to specialize.

## Performance

Competitive with Llama-3, Mixtral, and other open models on standard benchmarks (MMLU, HellaSwag, etc.)
while offering 3× higher throughput at long contexts (128K+ tokens) vs pure Transformer-MoE models.

## Significance

Jamba was the first major production demonstration that Mamba layers can be practically
deployed at scale alongside attention, validating the hybrid approach for long-context
applications.
