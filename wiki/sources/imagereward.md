---
title: "ImageReward: Learning and Evaluating Human Preferences for Text-to-Image Generation"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [text-to-image, 人类偏好, reward model, RLHF, 图像生成评测, diffusion model]
sources: [imagereward.pdf]
---

## 概述

ImageReward（清华大学 & 智谱 AI, NeurIPS 2023）是首个通用的文本到图像人类偏好 reward model，基于系统化的人类标注流程训练，旨在自动评估和优化文本到图像生成模型。该工作包含两个核心贡献：1) ImageReward 模型本身作为人类偏好评分器；2) ReFL（Reward Feedback Learning）算法，利用 ImageReward 的反馈直接微调 diffusion 模型。

代码和数据集开源于 https://github.com/THUDM/ImageReward

## 关键要点

### 数据集构建

- **数据来源**：从 DiffusionDB 中选取 10,000 个真实用户 prompt，每个 prompt 配 4-9 张生成图像
- **标注规模**：8,878 个有效 prompt，共 **136,892 对**专家比较标注（约 137K 对）
- **标注维度**：三个七级评分维度 + 排名
  - Image-Text Alignment（图文对齐）
  - Fidelity（图像质量/逼真度）
  - Harmlessness（无害性）
- **标注流程**：三阶段系统 — Prompt Annotation → Text-Image Rating → Image Ranking
- **质量控制**：专业标注公司 + 质量检查员 + 95.8% 标注员具大学学历

### 模型架构

- **Backbone**: BLIP（ViT-L 图像编码器 + 12 层 Transformer 文本编码器）
- **训练方式**：提取图像和文本特征，通过 cross attention 融合，用 MLP 生成标量偏好分数
- **损失函数**：基于排序的 pairwise comparison loss（Bradley-Terry 模型）
- **关键训练技巧**：冻结 70% backbone transformer layers 防止过拟合，学习率 1e-5，batch size 64

### ImageReward 作为评测指标

与人类评估排名的 Spearman 相关系数：

| 指标 | Real User Prompts (ρ) | MS-COCO 2014 (ρ) |
|------|----------------------|------------------|
| ImageReward | **1.00** | **0.77** |
| CLIP Score | 0.60 | 0.09 |
| Zero-shot FID | - | 0.09 |

### Preference Accuracy

| 模型 | Preference Acc. | Recall@1 | Recall@2 | Recall@4 |
|------|----------------|----------|----------|----------|
| CLIP Score | 54.82 | 27.22 | 48.52 | 78.17 |
| Aesthetic Score | 57.35 | 30.73 | 53.91 | 75.74 |
| BLIP Score | 57.76 | 30.73 | 50.67 | 77.63 |
| **ImageReward** | **65.14** | **39.62** | **63.07** | **90.84** |

ImageReward 偏好准确率达 65.14%，比随机（50%）高 15.14%，约为 BLIP Score 提升幅度的两倍。

### ReFL 算法

- **核心洞察**：在 diffusion 去噪过程中，步骤 t >= 30（共 40 步）时直接预测的 x_0' 的 ImageReward 分数已能区分最终图像质量
- **方法**：在随机选取的后期去噪步骤（t ∈ [30,40]）反向传播 ImageReward 的梯度，直接微调 LDM
- **损失**：ReFL loss + pre-training loss 联合优化（λ=1e-3）
- **训练设置**：Stable Diffusion v1.4，8 × A100 GPU，half-precision

### ReFL 人类评估结果

| 方法 | Real User Prompts #Win | MT Bench WinRate |
|------|----------------------|-----------------|
| SD v1.4 (baseline) | 1315 | - |
| Dataset Filtering | 1394 (55.17%) | 51.72% |
| Reward Weighted | 1075 (39.52%) | 43.33% |
| RAFT (iter=1) | 1341 (49.86%) | 42.31% |
| **ReFL (Ours)** | **1508 (58.79%)** | **58.49%** |

ReFL 在总 win 数和 win rate 上均超越所有对比方法。

## 重要发现

1. **ImageReward 显著优于现有指标**：超越 CLIP Score（38.6%）、Aesthetic Score（39.6%）、BLIP Score（31.6%）在理解人类偏好方面的表现
2. **更高区分度**：ImageReward 在不同模型间的分数分布有更大的四分位距（interquartile range），能更好区分不同质量的图像
3. **FID 的局限性**：FID 与人类偏好排名仅有 0.09 的相关系数（MS-COCO 2014），且无法评估单张图像
4. **BLIP 优于 CLIP 作为 backbone**：可能因为 BLIP 使用 image-grounded text encoder，将文本编码器作为跨模态融合器而非独立编码器
5. **数据规模正相关**：训练集从 1K 到 8K prompts，偏好准确率从 63.07% 提升至 65.14%
6. **直接优化优于间接方法**：ReFL 的直接梯度反馈比 Dataset Filtering、Reward Weighted、RAFT 等间接数据筛选/加权方法更有效

## 问题与思考

1. **标注偏差**：标注员主要是中国大学生，是否能代表全球用户的审美偏好？
2. **Reward hacking**：ReFL 是否可能过度优化 ImageReward 而产生不自然的图像？
3. **领域泛化**：训练数据来自 DiffusionDB（偏向艺术风格），对真实照片、专业设计等场景的泛化能力如何？
4. **与 HPSv2 的竞争**：HPSv2 使用更大数据集（798K choices vs 137K pairs），是否能进一步提升？
5. **评测指标的局限**：ImageReward 仍是单一标量，无法像 VBench 那样提供多维度诊断信息
6. **RLHF for vision 的未来**：ImageReward + ReFL 验证了 RLHF 范式从 NLP 到视觉生成的迁移，后续发展值得关注

## 相关页面

- [BLIP-2](../sources/blip2.md)
- [CLIP](../entities/clip.md)
- [HPSv2](../sources/hpsv2.md)
- [RLHF](../concepts/rlhf.md)
- [FID 指标](../concepts/fid.md)
