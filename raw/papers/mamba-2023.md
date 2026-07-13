---
source_url: https://arxiv.org/abs/2312.00752
ingested: 2026-07-13
sha256: TBD
---

# Mamba: Linear-Time Sequence Modeling with Selective State Spaces

**Authors:** Albert Gu (CMU), Tri Dao (Princeton)
**Published:** December 2023 (arXiv)
**Citations:** 12,000+
**Venue:** COLM 2024

## Summary

Mamba introduces a new class of **selective state space models (SSMs)** that achieve
Transformer-quality language modeling with linear-time complexity in sequence length.

## Key Contributions

1. **Selection Mechanism:** Unlike prior SSMs (S4, H3, Hyena) which had time-invariant
   parameters, Mamba makes SSM parameters (Δ, B, C) input-dependent, allowing the model
   to selectively propagate or forget information along the sequence.

2. **Hardware-Aware Algorithm:** The selective mechanism breaks the convolution view of SSMs,
   making efficient training harder. The authors design a hardware-aware parallel scan
   algorithm (selective scan) that fuses recurrent computation into a single GPU kernel,
   similar in spirit to FlashAttention's kernel fusion.

3. **Simplified Architecture:** Mamba strips away attention and MLP blocks entirely,
   using only the selective SSM layer in a homogeneous deep architecture.

## Architecture Details

- **Mamba Block:** Input → Linear Projection → 1D Convolution (kernel=4) → SiLU → Selective SSM
  → Residual Connection → Output
- **State dimension (d_state):** 16 (default)
- **Local convolution width:** 4
- **Expansion factor:** 2 (hidden dim = 2 × d_model)
- **SSM parameters:** Δ (step size, input-dependent via projection), B/C (learnable + input-dependent),
  A (HiPPO matrix, learned)

## Performance

Matches or exceeds Transformers of similar size on:
- Language modeling (Pile, SlimPajama, 300B tokens)
- DNA sequence modeling
- Audio generation (raw waveforms)

**Scaling:** 130M → 2.8B parameters. Linear O(N) training cost, O(1) inference cost per token
(constant-size state, unlike Transformer's growing KV cache).

## Trained Models

| Size | Layers | d_model |
|------|--------|---------|
| 130M | 24 | 768 |
| 370M | 48 | 1024 |
| 790M | 48 | 1536 |
| 1.4B  | 48 | 2048 |
| 2.8B  | 64 | 2560 |

Note: Mamba layers double Transformer layers since 2 Mamba blocks ≈ 1 MHA+MLP pair.

## Code

- Repo: https://github.com/state-spaces/mamba
- Package: `pip install mamba-ssm`
- License: Apache 2.0
