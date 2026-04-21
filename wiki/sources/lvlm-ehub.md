---
title: "LVLM-EHub: A Comprehensive Evaluation Benchmark for Large Vision-Language Models"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [benchmark, VLM, multimodal, evaluation, arena]
sources: [LVLM-EHub_A_Comprehensive_Evaluation_Benchmark_for_Large_Vision-Language_Models.pdf]
---

## 概述

LVLM-EHub 是一个面向大型视觉-语言模型（Large Vision-Language Models）的综合评测基准。该工作从多个维度量化多模态模型能力，并引入在线 Arena 机制进行成对人类评估，同时覆盖商用模型和开源模型，为多模态模型的全面对比提供了统一平台。

## 关键发现

- 商用模型（如 GPT-4V）在多数维度上领先开源模型，但差距在逐步缩小
- 在线 Arena 的人类评估结果与自动指标之间存在一定差异，表明自动评测仍有局限
- 不同模型在视觉理解、推理、生成等不同能力维度上各有所长
- 模型规模与多模态能力之间并非简单的线性关系

## 方法论

- **多维度评测**: 从多个维度系统量化视觉-语言模型的能力，包括视觉感知、推理、知识运用等
- **在线 Arena**: 采用 Elo rating 系统，通过成对比较（pairwise comparison）的方式进行人类评估，用户对两个模型的回答进行盲评打分
- **模型覆盖**: 同时评测商用模型（如 GPT-4V、Gemini）和开源模型（如 LLaVA、InternVL）
- **统一框架**: 提供标准化的评测流程和指标体系

## 重要数据

- 覆盖多个评测维度的综合基准
- 在线 Arena 支持实时人类评估
- 同时评测商用和开源视觉-语言模型
- 基于 Elo rating 的排名系统

## 局限性

- 在线 Arena 的评估结果受用户群体和提问偏好影响
- 人类评估的规模和成本限制了评测的全面性
- 多模态能力的维度划分仍不够精细
- 评测数据可能未及时跟进最新发布的模型
- 不同评测维度之间的权重分配缺乏理论依据

## 相关页面

- [MME](./mme.md)
- [MMBench](./mmbench.md)
- [MM-Vet](./mm-vet.md)
- [MathVista](./mathvista.md)
- [VLMEvalKit](./vlmevalkit.md)
