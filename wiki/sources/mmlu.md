---
title: "Measuring Massive Multitask Language Understanding (MMLU)"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [benchmark, evaluation, multitask, knowledge, reasoning, 2020]
sources: [2009.03300-mmlu.pdf]
---

## 概述

本文由 UC Berkeley（Dan Hendrycks, Collin Burns, Steven Basart, Andy Zou 等）于 2020 年 9 月发表（arXiv: 2009.03300，ICLR 2021），提出了 **MMLU（Measuring Massive Multitask Language Understanding）**——一个覆盖 57 个学科的大规模多任务语言理解基准。MMLU 涵盖 STEM、人文、社科、商业等广泛领域，难度从初级到专业水平不等，共计 **15,908** 道四选一选择题。MMLU 已成为评测大型语言模型知识广度和深度的事实标准基准之一，几乎所有主流 LLM 的发布都会报告 MMLU 分数。

## 关键发现

- **当时模型表现远低于专家水平**：最强模型 GPT-3（few-shot）的平均准确率仅 **43.9%**，而人类专家基线约 89.8%，随机猜测为 25%
- **知识密集型任务挑战巨大**：在专业医学、法律、数学等高难度科目上，模型表现接近随机水平
- **预训练数据量与多任务性能正相关**：更大规模的预训练持续提升 MMLU 性能，暗示知识编码能力随规模增长
- **Few-shot 优于 Zero-shot**：few-shot prompting 在 MMLU 上一致优于 zero-shot，表明任务格式的理解很重要
- **微调效果有限**：在 MMLU 上，基于 task-specific 微调的收益相对于模型规模扩展的收益较小

## 方法论

### 基准设计

- **57 个学科**：覆盖四大领域——
  - STEM（数学、物理、化学、计算机科学、工程等）
  - 人文（哲学、历史、文学、法律等）
  - 社会科学（心理学、经济学、社会学、政治学等）
  - 其他（商业、健康、杂项等）
- **题目来源**：收集自真实考试（如 AP、GRE subject tests）、教材练习题和专业资格认证题库
- **统一格式**：全部为四选一选择题（multiple choice, 4 options）
- **难度分级**：从高中水平到博士/专业水平

### 数据规模

- **总题数**：15,908 题
- **分割方式**：
  - Few-shot dev set：每个科目 5 题（用于 few-shot prompting）
  - Validation set：1,531 题
  - Test set：14,042 题

### 评估协议

- **Few-shot 设置**：使用每科目的 5 个 dev 示例作为 prompt
- **Zero-shot 设置**：不提供示例，直接回答
- **评分方式**：选择概率最高的选项，计算准确率
- **按科目/领域聚合**：可按学科、领域、难度级别分别统计

## 重要数据

| 模型 | 平均准确率 | 人文 | 社科 | STEM | 其他 |
|------|----------|------|------|------|------|
| 随机基线 | 25.0% | 25.0% | 25.0% | 25.0% | 25.0% |
| GPT-3 (few-shot) | 43.9% | 40.8% | 50.5% | 36.7% | 48.8% |
| UnifiedQA | 48.9% | 45.6% | 56.6% | 40.2% | 54.6% |
| 人类专家基线 | ~89.8% | — | — | — | — |

**后续模型在 MMLU 上的进展（非原文数据）**：

| 模型 | MMLU 5-shot |
|------|-------------|
| GPT-4 (2023) | 86.4% |
| Claude 3 Opus (2024) | 86.8% |
| Llama 3.1 405B (2024) | 87.3% |

## 局限性

- **选择题格式的局限**：四选一格式无法测试开放式生成、长文本推理等能力
- **静态基准**：题库固定，存在数据泄露（data contamination）风险——后续模型训练数据可能包含 MMLU 题目
- **知识偏向**：重视记忆和知识检索，对推理和创造性能力的评估不足
- **英语为中心**：原始版本仅覆盖英语（后有多语言扩展如 MMLU-Pro、多语言 MMLU）
- **答案歧义**：部分题目存在争议性答案，标注质量不均匀
- **已接近饱和**：截至 2024 年，顶级模型准确率已超过 87%，区分力下降
- **评测协议差异**：不同研究使用不同的 few-shot 数量和评分方式，导致跨研究对比困难

## 相关页面

- [GPT-4 Technical Report](./gpt4-technical-report.md)
- [OpenAI](../entities/openai.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [Rubric-based 评测方法](../concepts/rubric-based-evaluation.md)
