---
title: "A Survey on Evaluation of Large Language Models"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [survey, LLM-evaluation, benchmark, metrics]
sources: [3641289.pdf]
---

## 概述

本文是 Chang et al. 发表于 ACM Transactions on Intelligent Systems and Technology（TIST, 2024）的大语言模型评测综合综述。文章从三个关键维度系统梳理了 LLM 评测的全貌：**评什么（What to evaluate）**、**在哪评（Where to evaluate）**、**怎么评（How to evaluate）**，是当前 LLM 评测领域最全面的综述之一。

## 关键发现

- **评测维度应超越传统 NLP 任务**：LLM 的评测需要扩展到推理能力、伦理安全、社会偏见、鲁棒性等多个维度
- **现有 benchmark 快速过时**：随着模型能力提升，许多传统 benchmark 出现天花板效应（saturation），需要持续更新
- **LLM-as-Judge 成为新范式**：利用 LLM 自身作为评估工具是一个快速发展的方向，但存在固有偏见
- **人工评测仍是金标准但成本高昂**：人类评测在开放式生成任务中不可替代，但扩展性差
- **评测与能力理解之间存在鸿沟**：高分不等于真正理解，评测设计需更加注重能力本质的探测

## 方法论

文章采用系统性文献综述方法，围绕三个核心维度展开：

### What to evaluate（评什么）
- **自然语言处理任务**：文本分类、信息抽取、问答、摘要、翻译等
- **推理能力**：数学推理、逻辑推理、常识推理
- **自然科学**：物理、化学、生物等学科知识
- **医学**：临床推理、医学问答
- **伦理与安全**：毒性、偏见、价值观对齐
- **社会科学**：经济、法律、心理学等领域
- **Agent 能力**：工具使用、规划、交互

### Where to evaluate（在哪评）
- 主流 benchmark 汇总：MMLU、HumanEval、GSM8K、TruthfulQA 等
- 数据集特征对比和适用范围分析

### How to evaluate（怎么评）
- **自动评测指标**：BLEU、ROUGE、精确匹配等
- **人工评测**：打分、排序、对比评估
- **LLM-based 评测**：模型互评、自我评估

## 重要数据

- 综述覆盖 **数百篇**相关文献，是该领域引用量最高的综述之一
- 梳理了超过 **20 个主要评测维度**
- 汇总了 **数十个主流 benchmark** 的特点和适用场景
- 对比了 **三大评测方法**（自动、人工、LLM-based）的优劣势

## 局限性

- 综述具有时效性限制，LLM 领域发展极快，部分内容可能已过时
- 以英文为中心，对中文等非英文评测生态的覆盖较薄
- 未深入讨论多模态评测（图文、音视频等）
- 对评测方法论本身的理论基础（如测量学、心理统计学）着墨较少

## 相关页面

- [HELM 整体性评测](helm.md)
- [LLM-as-Judge 概念](../concepts/llm-as-judge.md)
- [LLM 评测综述（Future Internet）](llm-eval-survey-future-internet.md)
- [幻觉综述](hallucination-survey.md)
