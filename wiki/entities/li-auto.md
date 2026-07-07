---
title: "理想汽车 (Li Auto)"
type: entity
entity_type: organization
created: 2026-07-08
updated: 2026-07-08
tags: [机构, 车企, rubric, 评测, 后训练]
sources: [2601.08430-rubrichub.pdf]
---

# 理想汽车 (Li Auto)

## 基本信息

中国智能电动车企业（Li Auto Inc.）。除整车业务外，其 AI 团队在大模型评测与后训练方向有学术产出，与浙江大学、南洋理工大学、香港中文大学（深圳）等高校合作。

## 评测贡献

- 主导 [RubricHub](../sources/rubrichub.md)（arXiv:2601.08430, 2026）：提出 **Coarse-to-Fine Rubric Generation** 框架，构建 ~110k 多领域高区分度 rubric 数据集
- 核心创新 **难度演化（Difficulty Evolution）**机制，克服 rubric 监督的"天花板效应"
- 通过 RuFT + RuRL 两阶段后训练（DAPO + verl），让 **Qwen3-14B 在 [HealthBench](../benchmarks/healthbench.md) 达 69.3，超过 GPT-5（67.2）**
- 实证发现：rubric 用于 RL 时**纯正向加权优于加惩罚项**，负向 criteria 反而降低性能

## 相关页面

- [RubricHub](../sources/rubrichub.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [HealthBench](../benchmarks/healthbench.md)
- [RLHF](../concepts/rlhf.md)
