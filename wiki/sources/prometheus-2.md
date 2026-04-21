---
title: "Prometheus 2: An Open Source Language Model Specialized in Evaluating Other Language Models"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [LLM-as-judge, rubric, evaluation-model, open-source, weight-merging]
sources: [2403.07974v2.pdf, 2024.emnlp-main.248.pdf]
---

## 概述

Prometheus 2 是 Kim 等人在 EMNLP 2024 上发表的工作，是 [Prometheus](prometheus.md) 的改进版本。核心突破在于同时支持 **direct assessment**（直接评分）和 **pairwise ranking**（成对排序）两种评估模式，通过 weight merging 技术将两种能力统一到单一模型中。

该工作解决了前代 Prometheus 仅支持直接评分的局限性，使开源评估模型首次具备了与闭源评估器（如 GPT-4）相当的双模式评估能力。

## 关键发现

- **双模式统一**：通过 weight merging 技术，将分别训练的 direct assessment 模型和 pairwise ranking 模型合并为单一模型，两种能力互不干扰
- **开源模型最高人类相关性**：在与人类评估者的相关性指标上，Prometheus 2 在开源模型中达到最高水平
- **Rubric + Preference 联合训练**：训练数据同时包含基于 rubric 的评分数据和基于偏好的排序数据
- **灵活适配**：用户可根据场景选择直接评分或成对比较模式，无需切换模型
- **Weight Merging 有效性**：实验证明模型合并不会显著降低单项能力，反而在某些场景下带来互补提升

## 方法论

1. **分别训练两个专家模型**：
   - Direct Assessment 模型：基于 rubric 评分数据训练，输出 1-5 分 + 反馈
   - Pairwise Ranking 模型：基于偏好数据训练，输出哪个 response 更好 + 理由
2. **Weight Merging**：使用模型权重合并技术（如 TIES-Merging 或 DARE），将两个专家模型融合为一个统一模型
3. **训练数据扩展**：
   - 扩充了 Feedback Collection 数据集
   - 新增 pairwise 比较数据
   - 覆盖更多评估维度和领域
4. **全面评估**：在 direct assessment 和 pairwise ranking 两个维度上分别与人类评估、GPT-4 进行相关性对比

## 重要数据

| 指标 | 数值 |
|------|------|
| 模型规格 | 7B / 8x7B 参数 |
| 评估模式 | Direct Assessment + Pairwise Ranking |
| 与人类评估相关性 | 开源模型中最高 |
| 合并技术 | Weight Merging（TIES / DARE） |
| 发表会议 | EMNLP 2024 |

## 局限性

- Weight merging 技术的最优超参数需要经验搜索，缺乏理论保证
- 仍然依赖 GPT-4 生成的训练数据
- 在某些极端评估场景（如高度专业领域），与 GPT-4 仍存在差距
- Pairwise ranking 在候选 response 质量差异很小时判断力下降
- 未充分探索多语言评估场景

## 相关页面

- [Prometheus](prometheus.md) — 前代版本
- [LLM-as-Judge 概念](../concepts/llm-as-judge.md)
- [Rubric-Based Evaluation](../concepts/rubric-based-evaluation.md)
- [Rubric-Based 推理数据调研报告](rubric-based-reasoning-data-survey.md)
