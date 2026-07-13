---
title: Vision Mamba
created: 2026-07-13
updated: 2026-07-13
type: concept
tags: [architecture, model, vision]
sources: []
confidence: medium
---

# Vision Mamba

Mamba 在视觉领域的应用正在快速增长。核心挑战是：Mamba 的 causal recurrence 为 1D 序列设计，
而图像是 2D 结构。解决方案是**双向/多向扫描**。

## 代表性工作

### Vision Mamba (ViM) — 2024

将图像 patch 按多种扫描路径 (raster, reverse, etc.) 展开成 1D 序列后送入 Mamba block，
模拟 2D 感受野。性能对标 ViT。

### VideoMamba — 2024

扩展到 3D 时空数据（视频），使用时空选择性扫描处理时间维度。

### MFuser — CVPR 2025

用 Mamba 融合 Vision Foundation Model (VFM) 和 Vision-Language Model (VLM) 的特征。
利用 Mamba 的线性复杂度处理大量 patch token 的融合，在 domain-generalized
semantic segmentation 上达到 SOTA。

## Mamba 在视觉中的优势

- **线性复杂度：** 高分辨率图像产生大量 patch token，Attention 的 O(N²) 成为瓶颈，
  Mamba 的 O(N) 在此天然有优势
- **长程依赖：** Mamba 的状态压缩可能更适合捕获全局上下文

## 研究趋势

- 2024-2025 年大量 survey: "A Survey of Mamba", "Vision Mamba: A Comprehensive Survey"
- 医学图像分析、遥感图像分析是 Mamba 视觉应用的热门领域
- Mamba + 多模态是快速发展的方向

## See Also

- [[mamba-architecture]]
- [[state-space-models]]
