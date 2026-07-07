---
title: "T2I-CompBench++: An Enhanced and Comprehensive Benchmark for Compositional Text-to-Image Generation"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [text-to-image, compositional-generation, benchmark, evaluation-metrics, multimodal]
sources: [t2i-compbench.pdf]
---

## 概述

T2I-CompBench++ 是一个面向组合式文本到图像生成（compositional text-to-image generation）的增强型评测基准，由香港大学和华为诺亚方舟实验室的研究者提出。该工作针对当前 T2I 模型在生成包含多个对象、不同属性和复杂关系的场景时表现不佳的问题，构建了一个包含 8,000 条组合式文本 prompt 的综合 benchmark，覆盖 4 大类别和 8 个子类别。论文同时提出了针对不同组合性维度的专用评测指标，并引入 GORS（Generative mOdel finetuning with Reward-driven Sample selection）方法来提升模型的组合生成能力。

论文发表于 arXiv:2307.06350（v3, 2025年3月），是 NeurIPS 2024 会议版本的扩展。

## 关键要点

### Benchmark 设计

T2I-CompBench++ 包含 8,000 条组合式文本 prompt，分为 4 大类别、8 个子类别：

| 类别 | 子类别 | Prompt 数量 | 说明 |
|------|--------|------------|------|
| Attribute Binding | Color | 1,000 | 颜色-对象绑定 |
| Attribute Binding | Shape | 1,000 | 形状-对象绑定 |
| Attribute Binding | Texture | 1,000 | 纹理-对象绑定 |
| Object Relationships | 2D-Spatial | 1,000 | 左右上下等 2D 空间关系 |
| Object Relationships | 3D-Spatial | 1,000 | 前后遮挡等 3D 空间关系 |
| Object Relationships | Non-Spatial | 1,000 | 动作交互关系（wear, hold, look at 等） |
| Generative Numeracy | - | 1,000 | 数量正确性（1-8 个对象） |
| Complex Compositions | - | 1,000 | 多对象+多属性+多关系的复合场景 |

每个子类别含 700 条训练集 + 300 条测试集。词汇多样性显著优于前代 benchmark（2,470 名词、33 颜色、32 形状、23 纹理）。

### 评测指标体系

论文为不同组合维度设计了专用的评测指标：

1. **Disentangled BLIP-VQA**：用于 attribute binding 评测。将复杂 prompt 拆解为独立的 object-attribute 对，逐一用 VQA 问答验证绑定正确性，取各问题 "yes" 概率的乘积作为得分。
2. **UniDet-based metric**：用于 2D/3D 空间关系和 numeracy 评测。使用 UniDet 检测生成图像中的对象，通过 bounding box 中心坐标比较验证空间关系，结合深度估计验证 3D 关系。
3. **MLLM-based metric**：用于 non-spatial 关系和 complex compositions。使用 GPT-4V 或 ShareGPT4V 作为评估器，采用 Chain-of-Thought 两步评估（先描述图像，再给出对齐分数）。
4. **3-in-1 metric**：复合指标，取 CLIPScore + Disentangled BLIP-VQA + UniDet 的均值，用于 complex compositions。

### 人工一致性验证

通过 Amazon Mechanical Turk 进行人工评估（每子类别 300 prompt，2,400 prompt 总计），使用 Kendall's tau 和 Spearman's rho 验证指标与人工判断的相关性：

- BLIP-VQA 在 attribute binding（color/shape/texture）上与人类评估相关性最高
- UniDet 在 2D spatial、3D spatial 和 numeracy 上表现最佳
- GPT-4V 在 non-spatial 和 complex 类别上排名第一
- ShareGPT4V + CoT 在多个类别上表现接近 GPT-4V

### 模型基准测试结果

论文在 T2I-CompBench++ 上评测了 11 个 T2I 模型：

| 模型 | Color (B-VQA) | Shape (B-VQA) | Texture (B-VQA) | 2D-Spatial (UniDet) | 3D-Spatial (UniDet) | Numeracy (UniDet) | Non-Spatial (GPT-4V) | Complex (GPT-4V) |
|------|--------------|--------------|----------------|--------------------|--------------------|------------------|--------------------|--------------------|
| FLUX.1 | 0.7407 | 0.5718 | 0.6922 | 0.6185 | 0.3866 | 0.3127 | 0.3703 | 0.7927 |
| DALLE-3 | 0.7785 | **0.6205** | 0.7036 | 0.5926 | 0.3744 | 0.3003 | 0.3773 | 0.7927 |
| Stable Diffusion 3 | **0.8132** | 0.5885 | **0.7334** | 0.6174 | 0.4084 | 0.3140 | 0.3771 | 0.7919 |
| SD-XL | 0.5879 | 0.4887 | 0.5299 | 0.4991 | 0.3566 | 0.3119 | 0.3237 | 0.7817 |

关键发现：
- Spatial relationship 是最具挑战性的类别
- DALLE-3 和 SD3 在多数维度上达到 SOTA
- 即便 SOTA 模型在复杂组合 prompt 上仍有明显不足

### GORS 训练方法

GORS 通过选取高对齐度的生成样本作为奖励加权样本，对 Stable Diffusion v2 进行 LoRA 微调：
- 在所有类别上超越基线和先前方法
- 使用 Grounded-SAM 作为 attribute binding 的 reward model（GORS-unbiased）
- 训练集从 700 扩展到 1,400 prompt 时性能持续提升

## 重要发现

1. **传统指标不适用于组合性评测**：CLIPScore 和 BLIP-CLIP 对不同模型给出相似分数，无法区分组合能力差异，且与人工评估相关性低。
2. **MLLM 作为评测器的潜力与局限**：GPT-4V 在 non-spatial 和 complex 维度上与人工相关性最高，但在 color、texture、2D/3D spatial 等细粒度维度上不如专用指标；ShareGPT4V 倾向给出不够多样的评分，稳定性不如 GPT-4V。
3. **增强 prompt 细节无助于组合性**：将 prompt 从平均 8.21 词扩展到 24.00 词的详细版本，BLIP-VQA 分数变化极小（0.5879 vs 0.5781），说明挑战在于视觉内容组合而非 prompt 理解。
4. **Seen vs Unseen 性能差异**：模型在未见过的属性-对象组合上表现略低于训练集中出现过的组合，说明泛化仍是问题。

## 问题与思考

1. **评测指标碎片化**：目前没有统一指标能覆盖所有组合维度，需要 4 种不同指标分别评测，增加了使用复杂度。论文承认这是一个未解决的局限。
2. **UniDet 的 2D 局限**：UniDet-based metric 仅限于 2D 空间关系评估，3D 关系依赖深度估计，准确性存疑。
3. **MLLM 评测的成本和可重复性**：GPT-4V 作为评测器存在 API 成本高、模型版本更新导致不可重复的问题。
4. **与 HealRub 的关联**：该工作中 MLLM-based metric 使用的 Chain-of-Thought + 分级评分范式（0-100 分的 rubric）与 HealRub 项目中的 LLM-as-Judge 评分框架高度相似，可参考其 prompt 设计。
5. **Benchmark 规模偏小**：相对于视觉生成模型的多样性，8,000 条 prompt 的覆盖面是否足够？尤其 complex compositions 仅 1,000 条。

## 相关页面

- [多模态评测](../concepts/multimodal-evaluation.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [CLIPScore](../concepts/clipscore.md)
