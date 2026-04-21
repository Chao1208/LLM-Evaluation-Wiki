---
title: "MM-Vet: Evaluating Large Multimodal Models for Integrated Capabilities"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [benchmark, multimodal, LMM, integrated-capabilities, GPT-4-judge]
sources: [2308.02490v4.pdf]
---

## 概述

MM-Vet 由 Yu 等人提出，发表于 ICML 2024，旨在评估大型多模态模型（Large Multimodal Models）的集成能力。与测试单一能力的基准不同，MM-Vet 关注模型同时调用多种核心视觉-语言能力来解决复杂问题的表现，定义了 6 种核心能力，并通过 218 道开放式问题进行评测，使用 GPT-4 作为自动评判者。

## 关键发现

- 现实世界的多模态任务往往需要多种能力的集成运用，而非单一能力
- 开放式问答比选择题更能反映模型的真实能力水平
- GPT-4 作为评判者与人类评判具有较高一致性，为开放式评测提供了可行方案
- 模型在需要组合多种能力的复杂问题上表现显著下降

## 方法论

- **6 种核心视觉-语言能力**:
  - Recognition（识别）：物体、场景、文字等的识别
  - Knowledge（知识）：需要外部知识才能回答的问题
  - OCR（光学字符识别）：图像中文字的读取
  - Spatial Awareness（空间感知）：空间关系理解
  - Language Generation（语言生成）：高质量文本输出
  - Math（数学）：数学推理与计算
- **集成能力评测**: 每道题可能需要 1-6 种核心能力的组合，通过能力组合矩阵分析模型表现
- **开放式评测**: 218 道开放式问题，不限制答案格式
- **自动评分**: 使用 GPT-4 作为 judge，对模型回答进行评分

## 重要数据

- 218 道开放式评测问题
- 6 种核心视觉-语言能力
- 多种能力组合的评测矩阵
- GPT-4 自动评分，与人类判断高度一致

## 局限性

- 218 道题的数据规模较小，统计显著性有限
- 依赖 GPT-4 作为评判者，引入了对特定模型的依赖
- 开放式评测的评分标准难以完全标准化
- 能力分类存在重叠，部分题目的能力标注可能存在争议
- GPT-4 评判可能对某些生成风格存在偏好

## 相关页面

- [MME](./mme.md)
- [MMBench](./mmbench.md)
- [MathVista](./mathvista.md)
- [LVLM-EHub](./lvlm-ehub.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
