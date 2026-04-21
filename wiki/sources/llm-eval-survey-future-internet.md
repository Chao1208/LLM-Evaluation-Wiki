---
title: "Survey on Evaluation of Large Language Models (Future Internet)"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [survey, LLM-evaluation, text-generation]
sources: [futureinternet-15-00260-v3.pdf]
---

## 概述

本文是发表于 Future Internet 期刊（2023）的一篇关于大语言模型评测方法的综述论文。文章聚焦于文本生成模型的评测方法体系，系统梳理了自动评测指标、人工评测方法以及 benchmark 数据集三大评测途径，为研究者和实践者提供了评测方法选择的参考框架。

## 关键发现

- **自动指标与人类判断存在差距**：传统的 n-gram 匹配指标（如 BLEU、ROUGE）在开放式生成任务中与人类评价的相关性有限
- **基于模型的评测指标崛起**：BERTScore、BARTScore 等利用预训练模型进行语义层面的评测，显著提升了与人类判断的一致性
- **人工评测标准化不足**：不同研究中人工评测的维度定义、评分标准差异大，导致结果难以跨研究对比
- **benchmark 设计需平衡覆盖度和针对性**：通用型 benchmark 覆盖面广但深度不足，领域专用型 benchmark 深度好但泛化性差
- **多维度评测是趋势**：单一指标无法全面反映模型能力，多维度综合评测成为共识

## 方法论

文章按评测方法类型进行分类梳理：

### 自动评测指标
- **基于 n-gram 的指标**：BLEU、ROUGE、METEOR、CIDEr 等
- **基于嵌入的指标**：BERTScore、MoverScore 等，利用语义向量相似度评估
- **基于生成模型的指标**：BARTScore、GPTScore 等，利用语言模型的生成概率评估
- **任务特定指标**：精确匹配（EM）、F1 score、代码执行通过率（pass@k）等

### 人工评测
- 评测维度：流畅性（fluency）、相关性（relevance）、一致性（coherence）、信息性（informativeness）
- 评测方式：绝对打分、相对排序、A/B 对比
- 评注者间一致性（Inter-annotator agreement）的重要性

### Benchmark 数据集
- 通用 benchmark：SuperGLUE、MMLU 等
- 领域 benchmark：医学、法律、编程等专项评测集
- 动态 benchmark：持续更新以对抗数据泄露

## 重要数据

- 综述覆盖了 **主要的自动评测指标家族**，从传统的词重叠方法到基于神经网络的方法
- 汇总了评测指标与人类判断的 **相关性对比数据**
- 梳理了 **数十个主流评测 benchmark** 的特征和适用任务

## 局限性

- 综述的覆盖范围集中在文本生成评测，对分类、信息抽取等判别式任务覆盖较少
- 发表时间为 2023 年，未能涵盖后续快速发展的 LLM-as-Judge 方法
- 对中文和多语言评测的讨论不够深入
- 未涉及多模态模型的评测方法

## 相关页面

- [LLM 评测综述（Chang et al.）](llm-eval-survey-chang.md)
- [HELM 整体性评测](helm.md)
- [LLM-as-Judge 概念](../concepts/llm-as-judge.md)
