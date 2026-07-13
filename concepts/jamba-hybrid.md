---
title: Jamba — Hybrid Transformer-Mamba
created: 2026-07-13
updated: 2026-07-13
type: concept
tags: [architecture, model, hybrid]
sources: [raw/papers/jamba-2024.md]
confidence: high
---

# Jamba: Hybrid Transformer-Mamba Language Model

**厂商：** AI21 Labs
**论文：** [Jamba: A Hybrid Transformer-Mamba Language Model](https://arxiv.org/abs/2403.19887)
**发表：** ICLR 2025

## 核心思想

Jamba 是第一个**大规模生产级**的 Transformer-Mamba 混合架构。
它交替使用 Transformer (Attention) 层和 Mamba (SSM) 层，结合 Mixture-of-Experts (MoE)，
在维持 Transformer 推理能力的同时，利用 Mamba 的长序列效率。

## 架构

```
Layer 1:  Transformer (Attention + MoE FFN)
Layer 2:  Mamba (SSD)
Layer 3:  Transformer
Layer 4:  Mamba
...
Mix of attention and SSM layers throughout
```

- **Jamba-1.5-Large：** 94B active / 398B total params
- **Jamba-1.5-Mini：** 12B active / 52B total params
- **上下文窗口：** 256K tokens（发布时最大 open-weight 模型）
- **专家：** 16 experts, top-2 routing

## 为什么混合有效

实验发现：
1. **纯 Mamba 在某些任务上弱：** 特别是精确复制 (copying) 和 in-context 检索
   ——这些任务需要 "记住具体 token 位置"，Attention 天然擅长。
2. **纯 Transformer 在长上下文效率低：** KV cache 随长度线性增长，内存/吞吐下降。
3. **交替层 = 互补：** Attention 层提供 "硬记忆"，Mamba 层提供 "压缩记忆"，
   两者配合达到最佳效果。

## 关键创新：ExpertsInt8

Jamba 引入 **ExpertsInt8**——一种专门的 MoE 量化技术，
可以将 94B 模型 + 256K 上下文放进 8×80GB GPU 的机器上，且质量无损。
这是混合架构能够大规模部署的关键使能技术。

## 训练配置

- 数据：约 3T tokens（公开 + 专有混合）
- 优化器：AdamW
- 上下文长度：训练时 256K

## 与纯架构的对比

| | Jamba | 纯 Transformer | 纯 Mamba |
|---|---|---|---|
| In-context learning | ✅ | ✅ | ⚠️ |
| 长序列效率 | ✅ | ⚠️ | ✅ |
| Copy/Retrieval | ✅ | ✅ | ⚠️ |
| 训练速度 | ✅ | ✅ | ⚠️ (Mamba-1) |
| 推理吞吐 (长序列) | ✅ | ⚠️ | ✅ |
| 生态成熟度 | ⚠️ | ✅ | ⚠️ |

## See Also

- [[mamba-architecture]] — Mamba 系列架构
- [[mamba-vs-transformer]] — Mamba 与 Transformer 深入对比
- [[state-space-models]] — SSM 基础
