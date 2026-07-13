---
title: Mamba vs Transformer
created: 2026-07-13
updated: 2026-07-13
type: comparison
tags: [comparison, architecture, model]
sources: [raw/papers/mamba-2023.md, raw/papers/mamba2-2024.md, raw/papers/jamba-2024.md]
confidence: high
---

# Mamba vs Transformer

## 根本差异

| 维度 | Transformer (Attention) | Mamba (SSM) |
|------|------------------------|-------------|
| **核心操作** | Q·K^T pairwise attention | Hidden state recurrence |
| **记忆方式** | 显式：每个 token 访问所有历史 token | 隐式：压缩进固定大小的隐藏状态 |
| **训练复杂度** | O(N²) | O(N) |
| **推理复杂度** | O(N) per token（KV cache 增长） | O(1) per token（固定状态大小） |
| **推理内存** | 随序列长度线性增长（KV cache） | 恒定（仅隐藏状态） |
| **长程依赖** | ✅ 直接 pairwise，理论完美 | ⚠️ 依赖状态压缩质量 |
| **并行训练** | ✅ 天然并行（attention matrix） | ⚠️ recurrence 固有顺序性 |
| **GPU 效率** | ✅ FlashAttention 充分利用 Tensor Core | ⚠️ 需要自定义 scan kernel |

## 性能对比（语言建模，~300B tokens 训练）

| Benchmark | Mamba-2.8B | Pythia-2.8B (Transformer) | Mamba-2-2.7B | GPT-Neo-2.7B |
|-----------|-----------|---------------------------|-------------|-------------|
| LAMBADA | ~64% | ~61% | ~66% | ~58% |
| HellaSwag | ~57% | ~55% | ~59% | ~50% |
| PIQA | ~74% | ~73% | ~75% | ~73% |
| ARC-Easy | ~65% | ~63% | ~67% | ~63% |
| WinoGrande | ~61% | ~59% | ~62% | ~56% |

*结论：在 3B 以下规模，Mamba 与 Transformer 表现相当。更大规模的对比数据仍在收集中。*

## Mamba 的优势

1. **长序列推理：** 内存恒定，不像 Transformer 需要 O(N) KV cache。
   128K token 时 Mamba 内存仅需 Transformer 的 5-10%。

2. **高吞吐推理：** 每个 token 的推理计算量恒定，batch inference 效率极高。
   Jamba 论文中：256K 上下文时吞吐量是纯 Transformer-MoE 的 3×。

3. **训练扩展性好：** O(N) 训练理论上可以处理任意长序列，而 Transformer 的 O(N²)
   在高序列长度时内存爆炸。

4. **"in-context learning" 表现：** Mamba-2 在 copy/reversal/retrieval 等
   上下文任务上已经追平 Transformer（之前 Mamba-1 在此类任务较弱）。

## Mamba 的劣势

1. **训练速度（Mamba-1）：** 自定义 scan kernel 不如 FlashAttention 成熟，
   大规模训练吞吐量较低。Mamba-2 的 SSD 缓解了这个问题。

2. **生态成熟度：** Transformer 生态（HuggingFace, vLLM, TensorRT-LLM）远超 Mamba。
   推理部署、量化、微调工具链不完善。

3. **规模化不确定性：** 10B+ 参数时 Mamba 是否还能匹敌 Transformer 尚无定论。
   现有公开 Mamba 模型最大约 2.8B。

4. **Copy/Retrieval 任务：** Mamba-1 在需要精确复制/检索上下文的任务上弱于
   Attention。Mamba-2/Mamba-3 和混合架构（Jamba）改善了这个弱点。

## 混合架构：第三条路

实际生产中最有前景的方向可能不是 "Mamba vs Transformer" 而是 **"Mamba + Transformer"**：

| 架构 | 代表 | 策略 |
|------|------|------|
| **Jamba** (AI21, ICLR 2025) | 94B MoE | 交替 Mamba + Attention 层 |
| **Mamba-2-ATTN** (Dao & Gu) | 2.7B | 部分 SSD 层替换为 Self-Attention |
| **TransMamba** | - | Mamba 层中嵌入 Attention 子模块 |

核心洞察：Attention 擅长 copy/in-context learning，Mamba 擅长长序列效率。
交替使用两者可以鱼与熊掌兼得。

## 何时选 Mamba

- 需要处理 32K+ token 的长序列
- 推理延迟/吞吐量是关键指标
- 内存受限的部署环境（边缘设备、consumer GPU）
- 语言建模之外的领域：DNA、音频、视频（天然长序列）

## 何时选 Transformer

- 需要 10B+ 参数的大规模模型（生态成熟）
- 需要 in-context learning / few-shot 的高质量表现
- 需要利用现成的推理优化基础设施
- 团队已有 Transformer 训练/部署经验

## 何时选混合架构

- 既要长上下文效率，又要强 in-context learning
- 愿意投入架构搜索 / 调参
- 从零训练而不是微调已有模型

## See Also

- [[state-space-models]] — SSM 数学原理
- [[mamba-architecture]] — Mamba 家族详细架构
- [[jamba-hybrid]] — Jamba 混合架构
