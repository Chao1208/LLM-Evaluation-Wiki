---
title: "Holistic Evaluation of Language Models (HELM)"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [benchmark, holistic-evaluation, LLM, Stanford, CRFM]
sources: [2211.09110v2.pdf]
---

## 概述

HELM（Holistic Evaluation of Language Models）是由 Stanford CRFM（Center for Research on Foundation Models）提出的大语言模型整体性评测框架，发表于 TMLR 2023（Liang et al.）。该框架旨在解决现有评测碎片化、单一指标主导的问题，通过多场景、多指标的系统性评估，为语言模型提供全面、透明的评测结果。HELM 覆盖了 42 个评测场景（scenarios），横跨 7 大指标类别，对 30 个主流语言模型进行了标准化评测。

## 关键发现

- **没有单一模型在所有维度上占优**：不同模型在不同场景和指标上各有强弱，强调了多维度评测的必要性
- **准确率与其他指标之间存在 trade-off**：高准确率的模型在公平性、鲁棒性或毒性指标上未必表现更好
- **模型规模与性能正相关但非线性**：更大的模型通常表现更好，但在校准（calibration）和效率（efficiency）方面并非如此
- **透明度差异显著**：不同模型在文档公开程度、训练数据披露等方面差异巨大
- **标准化评测揭示隐藏问题**：统一框架下的评测暴露了单项 benchmark 难以发现的系统性偏差

## 方法论

- **场景设计**：定义 42 个评测场景，涵盖问答、信息检索、文本摘要、情感分析、毒性检测等多种任务
- **七大指标类别**：
  1. **Accuracy（准确率）**：任务完成的正确性
  2. **Calibration（校准）**：模型置信度与实际正确率的一致性
  3. **Robustness（鲁棒性）**：面对输入扰动时的稳定性
  4. **Fairness（公平性）**：跨人口统计学群体的表现差异
  5. **Bias（偏见）**：模型输出中的社会偏见
  6. **Toxicity（毒性）**：生成有害内容的倾向
  7. **Efficiency（效率）**：推理速度和计算成本
- **标准化流程**：统一的 prompt 格式、解码参数和评分方式，确保可复现性
- **多轮适配（adaptation）**：支持 zero-shot、few-shot 等多种 prompt 策略

## 重要数据

- 评测覆盖 **30 个主流语言模型**，包括 GPT-3、BLOOM、OPT、GLM 等
- **42 个评测场景**横跨核心 NLP 任务、知识密集型任务和社会影响类任务
- 每个模型在每个场景下生成 **7 个维度的指标得分**，形成多维评测矩阵
- 总计产生超过 **数万个评测数据点**，构成迄今最大规模的语言模型对比数据之一

## 局限性

- 评测以英文为主，对多语言模型的覆盖有限
- 部分指标（如 fairness、bias）的度量方式仍有争议，不同定义可能导致不同结论
- 评测场景以文本为主，未覆盖多模态能力
- 静态 benchmark 存在数据污染（data contamination）风险，模型可能在训练中已见过测试数据
- 效率指标受硬件和部署条件影响，横向对比的公平性存在挑战

## 相关页面

- [LLM 评测综述（Chang et al.）](llm-eval-survey-chang.md)
- [LLM-as-Judge 概念](../concepts/llm-as-judge.md)
- [Rubric-based 评测](../concepts/rubric-based-evaluation.md)
