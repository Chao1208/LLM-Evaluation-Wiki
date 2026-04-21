---
title: "EvalCrafter: Benchmarking and Evaluating Large Video Generation Models"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [benchmark, video-generation, evaluation, CVPR, multimodal]
sources: [Liu_EvalCrafter_Benchmarking_and_Evaluating_Large_Video_Generation_Models_CVPR_2024_paper.pdf]
---

## 概述

EvalCrafter 由 Liu 等人提出，发表于 CVPR 2024，是一个面向大型视频生成模型的基准评测框架。该工作设计了 700 条文本提示（text prompts），定义了 17 种评测指标，覆盖视觉质量、文本-视频对齐、运动质量和时序一致性四个维度，并通过人类评估与自动指标的对比分析，揭示了自动指标与人类判断之间的相关性。

## 关键发现

- 现有视频生成模型在文本-视频语义对齐和时序一致性方面仍存在明显不足
- 自动评测指标与人类评估之间的相关性因维度而异，部分指标与人类判断差距较大
- 运动质量是当前模型的主要瓶颈之一，生成的运动往往不自然或不合理
- 不同模型在四个评测维度上各有优劣，没有全面领先的模型

## 方法论

- **文本提示设计**: 700 条精心设计的 text prompts，覆盖多种场景和复杂度
- **17 种评测指标**, 分布于四个维度:
  - 视觉质量（Visual Quality）：FID、FVD、IS 等图像/视频质量指标
  - 文本-视频对齐（Text-Video Alignment）：CLIP score 等语义匹配指标
  - 运动质量（Motion Quality）：运动流畅性、合理性相关指标
  - 时序一致性（Temporal Consistency）：帧间一致性、闪烁度等指标
- **双重评估**:
  - 人类评估：由标注者对生成视频进行多维度主观打分
  - 自动评测：使用 17 种指标进行自动化评分
- **相关性分析**: 系统分析各自动指标与人类评估之间的 Pearson/Spearman 相关系数

## 重要数据

- 700 条文本评测提示
- 17 种评测指标
- 4 个评测维度：视觉质量、文本-视频对齐、运动质量、时序一致性
- 人类评估与自动指标的相关性分析

## 局限性

- 700 条提示难以覆盖所有视频生成场景（如长视频、多角色交互等）
- 部分自动指标与人类感知的相关性不够理想
- 人类评估的规模有限，评估者之间的一致性也需关注
- 视频生成领域发展迅速，评测基准需持续更新
- 未充分考虑生成视频的安全性和伦理问题

## 相关页面

- [数字人视频评测](../concepts/digital-human-video-evaluation.md)
- [VLMEvalKit](./vlmevalkit.md)
- [LVLM-EHub](./lvlm-ehub.md)
