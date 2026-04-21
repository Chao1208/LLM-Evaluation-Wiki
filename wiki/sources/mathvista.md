---
title: "MathVista: Evaluating Mathematical Reasoning of Foundation Models in Visual Contexts"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [benchmark, math-reasoning, visual, multimodal, ICLR]
sources: [2310.02255v3.pdf]
---

## 概述

MathVista 由 Lu 等人提出，发表于 ICLR 2024，是一个评估基础模型在视觉场景下数学推理能力的基准。该基准整合了来自 28 个已有数据集和 3 个新创建数据集的 6,141 个样本，涵盖 5 种任务类型，揭示了当前 AI 模型在视觉数学推理方面与人类之间的显著差距。

## 关键发现

- 人类与最佳 AI 模型之间在视觉数学推理上存在显著性能差距
- 需要同时理解视觉信息和进行数学推理的任务对模型挑战极大
- 不同模型在不同任务类型上的表现差异明显，没有全能选手
- 几何题解和学术论文图表分析是模型最薄弱的环节
- 多模态 CoT（Chain-of-Thought）推理在部分任务上能提升表现

## 方法论

- **数据来源**: 从 28 个已有数据集中精选，并新创建 3 个数据集，共 6,141 个样本
- **5 种任务类型**:
  - 图表问答（Figure QA）：对统计图表的理解与推理
  - 几何题解（Geometry Problem Solving）：几何图形相关的数学问题
  - 数学应用题（Math Word Problems）：结合视觉场景的文字应用题
  - 教材问答（Textbook QA）：教科书中图文结合的题目
  - 学术论文图表分析（Academic Paper Figure QA）：对论文中图表的分析
- **评测对象**: 涵盖纯语言 LLM、多模态 LLM、专用数学模型等
- **评测指标**: 根据任务类型采用准确率等指标

## 重要数据

- 6,141 个评测样本
- 28 个已有数据集 + 3 个新创建数据集
- 5 种任务类型
- 人类表现与最佳模型之间存在显著差距

## 局限性

- 数据来源多样导致难度和格式不完全统一
- 部分任务类型的样本量较少
- 评测侧重于静态图像，未涉及动态视觉场景
- 数学推理的深度有限，未涵盖高等数学等更复杂的数学领域
- 来自已有数据集的样本可能存在数据污染风险

## 相关页面

- [MME](./mme.md)
- [MMBench](./mmbench.md)
- [MM-Vet](./mm-vet.md)
- [LVLM-EHub](./lvlm-ehub.md)
