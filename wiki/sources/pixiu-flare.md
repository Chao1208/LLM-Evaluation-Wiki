---
title: "PIXIU: A Large Language Model, Instruction Data and Evaluation Benchmark for Finance"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [benchmark, finance, NLP, domain-specific, LLM]
sources: [2306.05443v1.pdf]
---

## 概述

PIXIU 由 Xie 等人于 2023 年提出，是一个面向金融领域的大语言模型评测与训练框架。该工作包含三大组成部分：FLARE（Financial Language Understanding and Prediction Evaluation）多任务金融 NLP 基准、FinMA（基于 LLaMA 微调的金融专用模型）以及配套的指令数据集。FLARE 涵盖 5 类金融 NLP 任务，为评估 LLM 在金融垂直领域的能力提供了系统化的基准。

## 关键发现

- 通用 LLM 在金融领域任务上表现不佳，领域适配（domain adaptation）至关重要
- FinMA 通过金融指令数据微调后，在多项金融任务上超越通用模型
- 情感分析和文本分类等理解类任务相对容易，而金融预测和问答等任务挑战更大
- 金融文本的专业性和时效性对模型提出了独特要求

## 方法论

- **FLARE 基准** — 5 类金融 NLP 任务:
  - 情感分析（Sentiment Analysis）：金融文本情感极性判断
  - 文本分类（Classification）：新闻分类、事件检测等
  - 信息抽取（Extraction）：实体识别、关系抽取、事件抽取
  - 文本生成（Generation）：金融摘要、报告生成
  - 问答（QA）：金融知识问答
- **FinMA 模型**: 基于 LLaMA 架构，使用金融领域指令数据进行微调
- **指令数据集**: 收集并构建金融领域的指令-回答对，用于模型训练
- **评测设计**: 针对不同任务类型采用对应的评测指标（如 F1、准确率、ROUGE 等）

## 重要数据

- 5 类金融 NLP 任务
- FinMA：基于 LLaMA 的金融领域微调模型
- 配套金融指令数据集
- 覆盖情感分析、分类、抽取、生成、问答等典型任务

## 局限性

- 主要聚焦英文金融文本，对中文等其他语言的金融场景覆盖不足
- 金融数据具有强时效性，基准数据可能快速过时
- FinMA 模型基于较早版本的 LLaMA，参数规模有限
- 未涵盖更复杂的金融推理场景（如多步骤分析、跨文档推理）
- 金融领域的合规性和安全性问题未充分纳入评测

## 相关页面

- [LLM-as-Judge](../concepts/llm-as-judge.md)
