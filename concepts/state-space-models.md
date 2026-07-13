---
title: State Space Models (SSM)
created: 2026-07-13
updated: 2026-07-13
type: concept
tags: [architecture, model]
sources: [raw/papers/mamba-2023.md, raw/papers/mamba2-2024.md]
confidence: high
---

# State Space Models (SSM)

## 什么是 State Space Model

SSM 源自控制理论——描述一个系统如何通过内部状态 (state) 将输入信号映射到输出信号。
在深度学习中，SSM 作为序列建模的替代方案，提供了一条不同于 Attention 的路径。

数学形式：

```
h'(t) = A h(t) + B x(t)   (状态更新方程)
y(t)  = C h(t)             (输出方程)
```

- **x(t)**：输入信号
- **h(t)**：隐藏状态（压缩的历史信息）
- **A**：状态转移矩阵（控制信息如何随时间演进）
- **B**：输入投影矩阵（控制新信息如何进入状态）
- **C**：输出投影矩阵（控制状态如何映射到输出）

## 为什么 SSM 对深度学习重要

Transformer 的 Attention 是 O(N²) 复杂度——每个 token 要 attend 到所有其他 token。
SSM 是 O(N) 训练、O(1) 推理——用一个固定大小的隐藏状态来压缩全部历史，
推理时不需要 KV cache，内存恒定。

## 从 S4 到 Mamba 的演进

| 模型 | 年份 | 核心改进 |
|------|------|---------|
| **S4** (Gu et al.) | 2022 | 结构化 SSM，HiPPO 初始化，证明 SSM 可以处理长序列 |
| **H3** (Fu et al.) | 2023 | 引入门控机制，SSM 不再需要单独的门 |
| **Hyena** (Poli et al.) | 2023 | 用隐式卷积替代 SSM，进一步加速 |
| **Mamba** (Gu & Dao) | 2023 | **Selective SSM**：参数变成输入相关的，SSM 学会选择性遗忘 |
| **Mamba-2 / SSD** (Dao & Gu) | 2024 | 发现 SSM 和线性 Attention 的数学对偶性，显著加速训练 |
| **Mamba-3** | 2026 | MIMO SSMs，更强的表达能力，ICLR 2026 |

## 关键概念

### Selectivity（选择性）

Mamba-1 的核心创新。之前的 SSM 参数 (A, B, C, Δ) 是时间不变的（对所有输入都一样），
Mamba 让 Δ（step size）、B、C 都变成输入相关的：

- Δ 大 = 更多关注当前输入，忽略历史
- Δ 小 = 更多依赖历史状态，忽略当前输入

这使得模型可以学会 "选择性遗忘"——对不重要的 token 直接丢弃，对重要的 token 保留在状态中。
类似于 Attention 的 softmax 选择哪些 token 相关，但 Mamba 是在 "记忆写入时" 就做选择。

### Structured State Space Duality (SSD)

Mamba-2 的突破。证明了 SSM 的 recurrent 形式（O(N) 推理）和矩阵乘法的 attention 形式
（O(N²) 但 GPU 友好）是**同一个数学对象的两种视角**（semi-separable matrix）。

这使得 Mamba-2 可以：
- 训练时用 matrix 形式（利用 Tensor Core，2-8× 快于 Mamba-1）
- 推理时切换回 recurrent 形式（常数内存）

### MIMO SSMs

Mamba-3 引入的 Multi-Input Multi-Output SSM——之前的 SSM 每个 channel 独立处理（SISO），
MIMO 让多个 channel 共享状态空间，用秩 (rank) 参数控制耦合度，表达能力更强。

## 关键人物

- **Albert Gu** (CMU)：SSM 理论的主要推动者，从 S4 到 Mamba-3 贯穿始终
- **Tri Dao** (Together AI / Princeton)：FlashAttention 作者，负责 Mamba 的硬件高效实现

## See Also

- [[mamba-architecture]] — Mamba 家族完整演进
- [[mamba-vs-transformer]] — Mamba 与 Transformer 对比
- [[jamba-hybrid]] — 混合架构
