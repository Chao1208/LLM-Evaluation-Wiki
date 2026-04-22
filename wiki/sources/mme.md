---
title: "MME: A Comprehensive Evaluation Benchmark for Multimodal Large Language Models"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [benchmark, multimodal, VLM, perception, cognition]
sources: [2306.13394v5.pdf]
---

## 概述

MME 是一个面向多模态大语言模型（Multimodal LLM）的综合评测基准，由 Fu 等人提出，发表于 arXiv 2023（论文 ID: 2306.13394）。该基准设计了 14 个子任务，全面覆盖感知（perception）和认知（cognition）两大能力维度，采用统一的 Yes/No 问答格式进行客观评测，避免了开放式生成评测中的主观性问题。

## 关键发现

- 多模态大模型在感知任务上表现差异显著，OCR 和物体识别能力参差不齐
- 认知类任务（如常识推理、数值计算）对模型挑战更大，整体得分低于感知类任务
- Yes/No 格式有效降低了评测的随机性和主观偏差，提供了可复现的定量对比
- 不同模型在各子任务上的能力分布呈现明显差异，没有单一模型在所有任务上占优

## 方法论

- **任务设计**: 14 个子任务分为感知和认知两大类
  - 感知任务：包括 OCR、物体存在性判断、物体计数、物体位置识别、颜色识别、海报理解、名人识别、场景理解、地标识别、艺术品识别等
  - 认知任务：包括常识推理、数值计算、文本翻译、代码推理等
- **评测格式**: 所有问题统一为 Yes/No 二选一格式，便于自动化评分
- **指标计算**: 每个子任务计算准确率，感知和认知分别汇总得分，最终计算总分

## 重要数据

- 共 14 个子任务，覆盖 10 个感知任务和 4 个认知任务
- 采用 Yes/No 格式确保评测的客观性和一致性
- 评测了包括商用和开源在内的多种多模态大模型

## 局限性

- Yes/No 格式虽然客观，但无法评估模型的开放式生成和复杂推理能力
- 二选一的随机基线为 50%，区分度有限
- 部分任务的数据规模较小，可能影响统计显著性
- 未涵盖视频理解、多轮对话等更复杂的多模态场景

## 相关页面

- [MMBench](./mmbench.md)
- [MM-Vet](./mm-vet.md)
- [MathVista](./mathvista.md)
- [LVLM-EHub](./lvlm-ehub.md)
- [VLMEvalKit](./vlmevalkit.md)
- [多模态评测概念](../concepts/llm-as-judge.md)
