---
title: Mamba Architecture
created: 2026-07-13
updated: 2026-07-13
type: concept
tags: [architecture, model, benchmark]
sources: [raw/papers/mamba-2023.md, raw/papers/mamba2-2024.md, raw/papers/mamba3-2026.md]
confidence: high
---

# Mamba Architecture Family

Mamba 是首个在语言建模上匹敌 Transformer 的 sub-quadratic 架构。
从 2023 年的 Mamba-1 到 2026 年的 Mamba-3，三代演进持续推动 SSM 的性能边界。

## Mamba-1 (2023): Selective State Spaces

**论文：** [Mamba: Linear-Time Sequence Modeling with Selective State Spaces](https://arxiv.org/abs/2312.00752)
**作者：** Albert Gu, Tri Dao
**被引：** 12,000+

### 核心贡献

**Selective SSM**：让 SSM 参数 (Δ, B, C) 依赖于输入，实现选择性的信息流动。
关键公式：

```
Δ = softplus(Linear(x) + bias)     # 输入相关的步长
B = Linear_B(x)                     # 输入相关的投影
C = Linear_C(x)                     # 输入相关的投影
h(t+1) = e^(ΔA) * h(t) + ΔB * x(t) # 状态更新
y = C * h(t)                        # 输出
```

### Mamba Block

```
Input
  → LayerNorm
  → Linear (d_model → 2×d_model)
  → Conv1D (kernel=4)  ← 局部特征增强
  → SiLU 激活
  → Selective SSM      ← 核心创新
  → Residual Add
  → Output
```

### 关键参数

| 参数 | 默认值 | 说明 |
|------|--------|------|
| d_state | 16 | SSM 隐藏状态维度 |
| d_conv | 4 | 1D 卷积核大小 |
| expand | 2 | 隐藏层扩展倍数 |
| d_model | 可变 | 模型总维度 |

### 性能特点

- **训练：** O(N) 复杂度（N = 序列长度）
- **推理：** O(1) 每 token（固定状态大小，无 KV cache）
- **硬件：** 自定义 CUDA kernel（selective scan），类似 FlashAttention 的融合策略

## Mamba-2 (2024): State Space Duality (SSD)

**论文：** [Transformers are SSMs](https://arxiv.org/abs/2405.21060)
**作者：** Tri Dao, Albert Gu
**会议：** ICML 2024

### 核心贡献

**SSD (Structured State Space Duality)**：证明 SSM 和线性 Attention 是数学对偶的——
同一个 semi-separable matrix 变换的两种视角：

```
SSM 视角（recurrent）：    h_t = A_t h_{t-1} + B_t x_t,  y_t = C_t h_t
Attention 视角（matrix）：  y = M * x,  其中 M_{ij} = C_i A_{i:j} B_j
```

前者 O(N) 推理，后者 O(N²) 但 GPU 友好。SSD 在两种模式间切换。

### 相对 Mamba-1 的改进

1. **Multi-head SSD：** 类似 MHA，多个 head 并行处理，d_state=64/128
2. **2-8× 训练加速：** 用 Tensor Core 友好的矩阵乘法替代自定义 scan kernel
3. **更大状态：** d_state 从 16 → 64/128，模型容量大幅提升
4. **SSD 核心仅 ~30 行 PyTorch：** 极简实现

### Mamba-2-ATTN 变体

引入 **Mamba-2-ATTN**：部分层用 SSD、部分层用 Self-Attention 的混合架构，
验证了两者可以互补。

## Mamba-3 (2026): MIMO State Spaces

**论文：** [Mamba-3: Improved Sequence Modeling](https://arxiv.org/abs/2603.15569)
**作者：** Lahoti, Li, Chen, Wang, Bick, Kolter, Dao†, Gu†
**会议：** ICLR 2026

### 核心贡献

1. **MIMO SSM（Multi-Input Multi-Output）：** 打破 SISO 限制，多个 channel 共享状态空间，
   `mimo_rank` 控制耦合度。表达能力远超 SISO SSM。

2. **更好的初始化策略：** 基于 HiPPO 理论改进，训练更快收敛。

3. **更大规模：** 参数 ≈ 6×d_model²（Mamba-1 仅 3×d_model²），支持更大模型。

### 参数对比

| 特性 | Mamba-1 | Mamba-2 | Mamba-3 |
|------|---------|---------|---------|
| SSM 类型 | Selective SISO | Multi-head SSD | MIMO SSD |
| d_state | 16 | 64/128 | 128 |
| headdim | - | 64 | 64 |
| 参数规模 | 3×d² | 3×d² | 6×d² |
| 训练速度 | 慢（scan kernel） | 快（matrix乘） | 快（继承SSD） |
| ICLR 发表 | COLM 2024 | ICML 2024 | ICLR 2026 |

## 架构选择指南

| 场景 | 推荐 | 理由 |
|------|------|------|
| 长序列推理 (128K+) | Mamba-2 | 常数内存，已验证稳定 |
| 训练实验 / 消融 | Mamba-2 | 训练快，生态成熟 |
| 追求 SOTA 质量 | Mamba-3 | 更强表达能力 |
| 混合架构 | Jamba | 保留 attention 关键层 |
| 视觉任务 | Vision Mamba (ViM) | 双向扫描适配 2D |

## See Also

- [[state-space-models]] — SSM 数学基础
- [[mamba-vs-transformer]] — 与 Transformer 对比
- [[jamba-hybrid]] — Jamba 混合架构
