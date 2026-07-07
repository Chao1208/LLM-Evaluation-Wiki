---
title: "Pick-a-Pic: An Open Dataset of User Preferences for Text-to-Image Generation"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [dataset, evaluation, text-to-image, human-preference, CLIP, scoring-function, NeurIPS-2023, image-generation]
sources: [pickscore.pdf]
---

## 概述

本文由 Yuval Kirstain、Adam Polyak、Uriel Singer、Shahbuland Matiana、Joe Penna、Omer Levy 等来自 Tel Aviv University 和 Stability AI 的研究者于 2023 年发表于 NeurIPS 2023。论文提出了 Pick-a-Pic 数据集和 PickScore 评分函数。Pick-a-Pic 是一个包含超过 500K 人类偏好样本的开放数据集，通过专门的 web 应用收集真实用户对 text-to-image 生成图片的偏好判断。基于该数据集训练的 PickScore（一个 CLIP-based 评分模型）在预测人类偏好的任务上达到了超人类水平的表现（70.5% vs 人类 68.0%），且与人类判断的相关性显著优于 FID 等传统指标。

## 关键要点

### 数据集构建

- **数据收集方式**：构建了 Pick-a-Pic web 应用，用户输入 prompt 后生成两张图片，用户选择偏好的一张（或标记平手）
- **数据来源**：真实用户（通过 Twitter、Facebook、Discord、Reddit 等社交媒体招募），而非付费众包工人
- **生成模型**：Stable Diffusion 2.1、Dreamlike Photoreal 2.0、Stable Diffusion XL variants，采用不同的 classifier-free guidance scale
- **数据规模**：
  - 总计 968,965 个排名判断，来自 66,798 prompts、6,394 users
  - 论文实验使用的 NSFW 过滤版本：583,747 训练样本 + 500 验证/测试样本
  - 训练集：37,523 prompts，4,375 distinct users
  - 更新版本（pickapic_v2）已超过 1,000,000 examples
- **质量控制**：要求用户身份认证（Gmail 或 Discord）、限制每用户 1000 次交互、NSFW 词汇过滤、行为监控与封禁

### PickScore 模型方法论

- **架构**：基于 CLIP-H（ViT-H 架构），遵循 CLIP 架构用 transformer text encoder 和 transformer image encoder 生成 d 维向量，计算内积作为分数：$s(x,y) = E_{txt}(x) \cdot E_{img}(y) \cdot T$
- **训练目标**：类似 InstructGPT 的 reward model objective，最小化 preference 分布 p 与 softmax-normalized scores 之间的 KL 散度
- **训练设置**：在 Pick-a-Pic 训练集上 fine-tune CLIP-H，训练 4,000 步，学习率 3e-6，batch size 128，warmup 500 步，8 x A100 GPUs，不到 1 小时完成
- **加权策略**：对同一 prompt 的多个样本按 prompt 频率的倒数加权，防止过拟合到高频 prompt

### 评估方法论

- **偏好预测准确率**：考虑 tie 的 adapted accuracy metric（预测相同标签得 1 分，一方为 tie 得 0.5 分）
- **Tie threshold**：通过验证集选择最优的 tie threshold probability t
- **模型评估相关性**：使用 Elo rating 系统计算模型排名，与人类偏好排名计算 Spearman 相关系数

## 重要发现

### PickScore 偏好预测性能

| Model | Accuracy |
|---|---|
| Random | 56.8 |
| Aesthetics predictor | 56.8 |
| CLIP-H | 60.8 |
| ImageReward | 61.1 |
| HPS | 66.7 |
| Human Expert | 68.0 |
| **PickScore** | **70.5** |

- PickScore 达到 70.5% 准确率，**超越人类专家** (68.0%)
- "超人类"表现的解释：生成 ground truth 标签的真实用户有其上下文、想法和动机，外部人类标注者对此无感知；PickScore 能学到统计规律来超越外部标注者
- 显著优于 CLIP-H (60.8%)、ImageReward (61.1%) 和 HPS (66.7%)

### PickScore vs FID 的模型评估能力

- PickScore 与人类排名的 Spearman 相关系数：**0.917**
- FID 与人类排名的 Spearman 相关系数：**-0.900**（负相关，方向相反）
- FID 对 prompt 无感知（set-level 指标），不适合评估单个图片的生成质量
- 高 CFG scale → 更好的人类偏好但更差的 FID（因为偏离了 MS-COCO 的真实图片分布）

### Elo Rating 相关性（多模型评估）

- PickScore: 0.790 ± 0.054
- HPS: 0.670 ± 0.071
- ImageReward: 0.492 ± 0.086
- CLIP-H: 0.313 ± 0.075

### 通过 Ranking 提升生成质量

| Comparison | Win Rate |
|---|---|
| PickScore vs Random (Null Template) | 71.4% |
| PickScore vs Random (Random Template) | 82.0% |
| PickScore vs Aesthetics | 85.1% |
| PickScore vs CLIP-H | 71.3% |

- 从 100 张生成图片中用 PickScore 选择最佳一张，人类更偏好 PickScore 的选择
- PickScore 选择的图片同时具有更高的审美分数（68.5% 的情况）和更好的 text alignment（90.5% 的情况）

## 问题与思考

1. **评测范式转变**：Pick-a-Pic 提出了一个重要观点——MS-COCO captions（描述日常照片的文字）不适合作为 text-to-image 模型的评测 prompt，因为用户的真实意图是"生成虚构内容"而非"描述已有照片"
2. **Real Users vs Crowd Workers 的核心区别**：Pick-a-Pic 的数据来自有真实创作动机的用户，而非被指示完成任务的众包工人。这种"内在动机"产生的偏好数据更接近真实使用场景——这与 LLM 评测中 LMSYS Chatbot Arena 的理念类似
3. **CLIP-based Scoring 的通用性**：PickScore 证明了在 CLIP 架构上 fine-tune preference 数据可以获得强大的评分能力。这个思路可以推广到其他模态的评测（如视频生成、音频生成）
4. **FID 的局限性被量化**：FID 与人类偏好呈负相关（-0.900），这是对该传统指标的有力批判。评测社区需要从"分布相似度"转向"用户偏好对齐"
5. **与 RLHF 的联系**：PickScore 可作为 text-to-image 模型 RLHF 训练中的 reward model，类似于 InstructGPT 在 LLM 中的做法。Pick-a-Pic 数据集为此提供了大规模的 preference 数据基础
6. **局限性**：数据中仍有 NSFW 内容残留；用户偏好可能反映特定人群的审美偏见；当前仅覆盖特定生成模型，泛化性有待验证

## 相关页面

- [LAION-5B](../sources/laion-5b.md)
- [CLIP](../concepts/clip.md)
