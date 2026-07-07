---
title: "Human Preference Score v2: A Solid Benchmark for Evaluating Human Preferences of Text-to-Image Synthesis"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [text-to-image, 人类偏好, preference prediction, benchmark, CLIP, 图像生成评测]
sources: [hpsv2.pdf]
---

## 概述

HPSv2（CUHK MMLab & SenseTime & CPII, 2023）提出了 Human Preference Dataset v2（HPD v2）和 Human Preference Score v2（HPS v2），分别是大规模人类偏好数据集和偏好预测模型。HPD v2 包含 **798,090 个人类偏好选择**（约 798K），覆盖 433,760 对图像，是同类数据集中规模最大的。HPS v2 基于 fine-tuned CLIP（ViT-H/14）训练，在偏好预测准确率和跨模型泛化能力上超越此前所有方法。

该工作同时建立了一套公平、稳定、易用的文本到图像生成模型评测 benchmark，覆盖 4 种风格共 3200 个评测 prompt。

代码和数据集开源于 https://github.com/tgxs002/HPSv2

## 关键要点

### HPD v2 数据集

**数据规模与来源**:
- **798K 偏好选择**，434K 图像对，108K prompt
- 涵盖 **9 个文本到图像生成模型** + COCO Captions 真实图像：
  - CogView2 (24B, Autoregressive)
  - DALL-E 2 (3.5B, Diffusion)
  - GLIDE (0.94B, Diffusion)
  - SD v1.4 & v2.0 (0.89B, Diffusion)
  - LAFITE (0.75B, GAN)
  - VQGAN+CLIP (0.73B, GAN)
  - VQ-Diffusion (0.37B, Diffusion)
  - FuseDream (0.35B, GAN)

**消除偏差的设计**:
- **图像来源偏差**：纳入 9 个不同模型（而非仅 Stable Diffusion 变体），确保跨模型泛化
- **Prompt 偏差**：使用 ChatGPT 清洗 DiffusionDB 中的 prompt，去除风格词（如 "artstation"、"greg rutkowski"）和冲突描述，转为清晰的单句描述
- **NSFW 过滤**：设置 NSFW 分数阈值 0.4 + DALL-E 2 安全检查器

**标注团队**:
- 57 名合同工（50 标注员 + 7 质检员）
- 标注员经过培训和测试，总体录用率 61%
- 每组 10 个不同标注员标注（test set），保证多样性

**数据划分**:
- 训练集：107,515 组（4 模型 + COCO），645,090 对二元比较
- 测试集：400 组（9 模型 + COCO），153,000 对二元比较
- Benchmark prompts：3200 个（不含配对图像）

### HPS v2 模型

**架构与训练**:
- Base model: ViT-H/14 CLIP（OpenCLIP，在 LAION-2B 上预训练）
- 训练目标：KL 散度最小化（预测偏好概率 vs 真实偏好标签）
- 训练超参：AdamW，lr=3.3×10⁻⁶，weight decay 0.35，batch size 128，warm-up 500 steps，总计 4000 steps
- 冻结策略：训练最后 20 层 image encoder + 最后 11 层 text encoder（通过贝叶斯优化确定）

**偏好预测准确率**:

| 模型 | ImageReward test set | HPD v2 test set |
|------|---------------------|-----------------|
| CLIP ViT-H/14 | 57.1 | 65.1 |
| ImageReward | 65.1 | 74.0 |
| Aesthetic Score Predictor | 57.4 | 76.8 |
| HPS v1 | 61.2 | 77.6 |
| PickScore | 62.9 | 79.8 |
| Single Human vs. Single Human | 65.3 | 78.1 |
| Single Human vs. Averaged Human | 53.9 | 85.0 |
| **HPS v2** | **65.7** | **83.3** |

HPS v2 在 HPD v2 测试集上达到 83.3% 准确率，超越所有模型，甚至超越单个人类与平均偏好的一致性。

### Benchmark 设计

**4 种风格 × 800 prompt = 3200 总 prompt**:
- Animation（动画）
- Concept-art（概念艺术）
- Painting（绘画）
- Photo（照片）

每种风格 800 prompt 分 10 组（每组 80），报告均值 ± 标准差，确保统计稳定性。

**评测模型排名**（HPS v2 分数，DrawBench 参考）:

| 模型 | Animation | Concept-art | Painting | Photo | DrawBench |
|------|-----------|-------------|----------|-------|-----------|
| Dreamlike Photoreal 2.0 | 28.24 | 28.24 | 27.60 | 27.99 | 27.88 |
| Realistic Vision | 28.22 | 27.53 | 27.56 | 27.75 | 27.77 |
| Deliberate | 28.13 | 27.46 | 27.45 | 27.62 | 27.73 |
| DeepFloyd-XL | 27.64 | 26.83 | 26.86 | 27.75 | 27.64 |
| Openjourney | 27.85 | 27.18 | 27.25 | 27.53 | 27.44 |
| SD v2.0 | 27.48 | 26.89 | 26.86 | 27.46 | 27.31 |
| SD v1.4 | 27.26 | 26.61 | 26.66 | 27.27 | 27.23 |
| DALL-E 2 | 27.34 | 26.54 | 26.47 | 27.24 | 27.16 |
| GLIDE | 23.34 | 23.08 | 23.27 | 24.50 | 25.05 |

社区模型（Dreamlike Photoreal, Realistic Vision, Deliberate 等）系统性地优于学术模型。

## 重要发现

1. **HPS v2 泛化能力强**：在 ImageReward 测试集（仅含 SD 图像）和 HPD v2 测试集（含 9 模型）上均表现最优，说明多源数据训练带来更好泛化
2. **HPS v2 对算法改进敏感**：能检测出 retrieval initialization 和 human-aligned tuning 等技术改进带来的分数提升
3. **社区模型 > 学术模型**：社区微调模型在 HPS v2 benchmark 上持续领先，可能因为社区模型经过大量人类反馈优化
4. **CogView2 的特殊性**：虽然 CLIP Score 和 Aesthetic Score 都低，但 HPS v2 给出相对较高分，说明这些指标各有侧重
5. **Inter-annotator agreement 与模型差距正相关**：质量差异大的模型对标注一致性高（~90%），质量接近的模型对一致性低（~65%）
6. **ChatGPT 清洗 prompt 有效**：减少了风格词偏差和内容冲突，使评测更公平

## 与相关数据集对比

| 数据集 | 标注者 | 数据格式 | Prompt 来源 | 图像来源 | #Prompts | #Choices |
|--------|--------|----------|------------|----------|----------|----------|
| HPD v1 | Discord 用户 | Top-1 选择 | Discord 用户 | Stable Diffusion | 25K | 25K |
| ImageReward | 专家 | Pairwise comparison | DiffusionDB | DiffusionDB | 9K | 137K |
| Pick-a-Pic | Web 用户 | Pairwise comparison | 4 模型 | 4 模型 | 38K | 584K |
| **HPD v2** | **专家** | **Pairwise comparison** | **DiffusionDB + COCO (ChatGPT 清洗)** | **9 模型 + real photo** | **108K** | **798K** |

## 问题与思考

1. **标注主观性**：57 名标注员未受过艺术专业训练，代表"普通人偏好"而非"专家审美"——这是优势还是局限？
2. **分辨率影响**：未研究图像分辨率对偏好的影响，而这在高分辨率生成时代可能很重要
3. **Prompt 覆盖**：仅使用 DiffusionDB 和 COCO Captions，可能遗漏 logo 设计、平面设计等专业领域
4. **ChatGPT 清洗的副作用**：去除风格词后是否改变了 prompt 的原始意图？
5. **与 ImageReward 的互补**：ImageReward 在 ReFL 方面更成熟，HPS v2 在数据多样性和泛化上更强——实际使用中如何选择？
6. **时效性**：Benchmark 中的模型已迭代数代（SD 1.4/2.0 → SDXL → SD3），需持续更新

## 相关页面

- [ImageReward](../sources/imagereward.md)
- [CLIP](../entities/clip.md)
- [Stable Diffusion](../entities/stable-diffusion.md)
- [FID 指标](../concepts/fid.md)
- [RLHF](../concepts/rlhf.md)
