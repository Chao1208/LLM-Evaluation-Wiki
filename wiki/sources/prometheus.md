---
title: "Prometheus: Inducing Fine-Grained Evaluation Capability in Language Models"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [LLM-as-judge, rubric, evaluation-model, open-source, fine-grained]
sources: [7178_Prometheus_Inducing_Fine_.pdf]
---

## 概述

Prometheus 是 Kim 等人（2023）提出的开源 LLM 评估器，旨在为语言模型注入细粒度（fine-grained）的评估能力。该模型基于 Llama-2-Chat 微调，核心创新在于使用 **rubric-grounded evaluation**（基于评分标准的评估），使评估过程透明、可复现，并摆脱对 GPT-4 等闭源模型的依赖。

Prometheus 的设计动机来源于当前 LLM-as-Judge 范式的两个关键问题：（1）闭源评估器（如 GPT-4）缺乏透明性和可控性；（2）现有评估方式缺少明确的评分标准（score rubric），导致评估结果不一致、难以复现。

## 关键发现

- **Rubric 驱动评估**：Prometheus 使用 1-5 分的 score rubric，每个分数等级配有详细的评判标准描述，使评估过程结构化且可解释
- **高度对齐人类和 GPT-4 判断**：在多个评估任务上，Prometheus 与人类评估者和 GPT-4 的判断呈现高相关性，显著优于其他开源模型
- **Feedback Collection 数据集**：构建了包含约 100K 实例的训练数据集，每条数据包含 GPT-4 生成的评分反馈（feedback），涵盖多样化的评估场景和 rubric 定义
- **开源可复现**：模型权重和训练数据完全开源，支持社区进行定制化评估任务
- **细粒度反馈**：不仅给出分数，还生成详细的文本反馈，解释评分理由

## 方法论

1. **Score Rubric 设计**：为每个评估任务定义 1-5 分的评分标准，每个等级包含具体的判定条件
2. **Feedback Collection 数据集构建**：
   - 从多种来源收集评估场景（instruction、response 对）
   - 为每个场景设计对应的 rubric
   - 使用 GPT-4 根据 rubric 生成评分和反馈文本
   - 最终获得约 100K 条高质量训练样本
3. **模型训练**：基于 Llama-2-Chat（13B）进行监督微调（SFT），输入为 instruction + response + rubric，输出为 score + feedback
4. **评估协议**：在多个 benchmark 上测试，与人类评估者和 GPT-4 的评分进行 Pearson/Spearman 相关性分析

## 重要数据

| 指标 | 数值 |
|------|------|
| 训练数据规模 | ~100K 实例 |
| 基础模型 | Llama-2-Chat 13B |
| 评分范围 | 1-5 分（fine-grained rubric） |
| 与 GPT-4 相关性 | 在多数任务上 Pearson > 0.9 |
| 与人类评估相关性 | 显著高于其他开源替代方案 |

## 局限性

- 训练数据完全依赖 GPT-4 生成的反馈，可能继承 GPT-4 的偏见和盲区
- 仅支持 direct assessment（直接评分），不支持 pairwise ranking（成对排序）
- Rubric 需要人工设计或预定义，自动化程度有限
- 13B 参数规模在某些复杂评估任务上可能能力不足
- 评估覆盖的语言和领域有限，主要针对英文场景

## 相关页面

- [Prometheus 2](prometheus-2.md) — 改进版本，支持直接评分和成对排序
- [LLM-as-Judge 概念](../concepts/llm-as-judge.md)
- [Rubric-Based Evaluation](../concepts/rubric-based-evaluation.md)
- [Rubric-Based 推理数据调研报告](rubric-based-reasoning-data-survey.md)
