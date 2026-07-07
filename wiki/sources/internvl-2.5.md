---
title: "VTC-CLS: [CLS] Token Tells Everything Needed for Training-free Efficient MLLMs"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [多模态, 视觉token压缩, MLLM, 推理效率, CLIP-ViT, training-free, 清华大学]
sources: [internvl-2.5.pdf]
---

## 概述

VTC-CLS（Ao Wang 等，清华大学，arXiv:2412.05819，2024年12月）提出了一种无需训练的视觉 token 压缩方法，用于加速多模态大语言模型（MLLM）的推理。核心发现是：视觉编码器中 **[CLS] token 对视觉 patch 的注意力分数**与 LLM 中视觉 token 的重要性具有高度一致性。基于此先验，VTC-CLS 直接利用 [CLS] token 注意力作为 importance indicator，在不需要额外训练的情况下对视觉 token 进行剪枝。

**注意**：此文件（internvl-2.5.pdf）实际包含的是 VTC-CLS 论文，而非 InternVL 2.5 论文。

## 关键要点

### 核心观察

1. **视觉编码器与 LLM 的 token 重要性一致性**：在 CLIP-ViT 视觉编码器中，[CLS] token 对各视觉 patch 的注意力分数与 LLM 中这些视觉 token 被输出 token 关注的程度高度正相关（Spearman's rank correlation consistently high across layers）
2. **感知偏差问题**：先前方法 FastV 使用 LLM 中 prompt text tokens 对视觉 token 的注意力作为重要性指标，导致保留的 token 与 prompt 强相关但与实际需要生成的 response 无关（perception bias）
3. **[CLS] token 先验**：[CLS] token 聚合了整张图像的关键视觉信息，其注意力分布更全面地反映了视觉信息的重要性

### 方法设计

- **重要性评估**：取视觉编码器倒数第 K 层（默认 K=3）的 [CLS] token 注意力分数，跨 head 平均作为视觉 token 重要性
- **跨层集成（Ensemble）**：从倒数第二层向前选取 K 层的 [CLS] 注意力分数，通过平均操作集成，获得更全面的重要性评估
- **Token 剪枝**：根据集成后的重要性分数，保留 Top-U 个视觉 token，其余移除
- **即插即用**：无需训练或微调，直接应用于现有 MLLM

### 评测结果

实验基于 LLaVA-1.5-7B，在 8 个基准上验证：

| 压缩级别 | SciQA | GQA | TextVQA | POPE | MMB | MMB-CN | MMVet | SEED | Avg |
|---------|-------|-----|---------|------|-----|--------|-------|------|-----|
| 原始（576 tokens） | 70.2 | 62.0 | 58.3 | 85.9 | 64.7 | 58.1 | 31.1 | 66.2 | 62.1 |
| VTC-CLS（256 tokens） | 69.6 | 60.3 | 57.7 | 86.2 | 63.6 | 57.1 | 34.4 | 64.8 | **61.7** |
| VTC-CLS（128 tokens） | 69.7 | 58.2 | 56.8 | 84.0 | 62.5 | 56.8 | 33.4 | 62.2 | **60.5** |
| VTC-CLS（64 tokens） | 70.0 | 55.7 | 55.5 | 78.8 | 61.2 | 55.8 | 31.7 | 58.3 | **58.4** |

- 256 tokens 时比 FastV 和 PruMerge+ 分别提升 1.2% 和 1.1%
- 128 tokens 时仅损失 0.4% 平均性能，同时减少 ~78% 的视觉 token

### 推理效率

| 方法 | Token数 | GQA 时间(s) | GQA 准确率 | POPE 时间(s) | POPE F1 |
|------|--------|-----------|----------|-----------|---------|
| 原始 | 576 | 3018 | 62.0 | 1921 | 85.9 |
| VTC-CLS | 128 | 2049 | 58.2 | 1349 | 84.0 |
| VTC-CLS | 64 | 1750 | 55.7 | 1216 | 78.8 |

- 128 tokens：GQA 加速 32.1%，POPE 加速 29.8%
- 64 tokens：约 1.7x 推理加速

## 重要发现

1. **K=3 是最优集成层数**：从倒数第二层向前取 3 层进行集成，在 overlap ratio 和 Spearman 相关系数上达到最佳
2. **平均操作优于 max/min**：不同集成函数中，简单平均在多个基准上表现最优
3. **优于聚类/合并方法**：VTC-CLS（59.5 avg）优于 ToMe（58.5）、KMeans（58.5）和随机选择（57.5），验证了基于重要性选择优于基于相似性合并的策略
4. **可在 LLM 层内压缩**：VTC-CLS 的重要性分数也可用于 LLM 第二层之后的压缩，比 FastV 在 LLM 内压缩场景也表现更好
5. **视觉保留可视化**：保留的 token 准确对应图像中与问题/回答相关的关键区域，有效去除背景冗余

## 问题与思考

1. **评测局限性**：仅在 LLaVA-1.5-7B 上验证，未测试更大规模模型（如 13B、72B）或其他架构（如 InternVL、Qwen-VL），泛化性有待验证
2. **动态压缩比**：当前方案使用固定 token 数 U，不同图像复杂度应有不同的最优压缩率，自适应压缩是值得探索的方向
3. **与其他效率方法的结合**：VTC-CLS 是否可以与量化、知识蒸馏等方法正交结合，进一步提升效率？
4. **高分辨率场景**：对于动态分辨率输入（如 InternVL 2.5 或 Qwen2-VL 的任意分辨率方案），视觉 token 数量更多，VTC-CLS 的收益可能更大
5. **对评测方法论的启示**：视觉 token 重要性分析为理解 MLLM 的视觉感知机制提供了新视角，可用于诊断模型在不同 benchmark 上的视觉依赖程度

## 相关页面

- [BLIP-2: Bootstrapping Language-Image Pre-training](../sources/blip2.md)
- [LLaVA: Visual Instruction Tuning](../sources/llava.md)

（来源：[internvl-2.5.pdf](../../raw/papers/internvl-2.5.pdf)）
