---
title: "Siren's Song in the AI Ocean: A Survey on Hallucination in Large Foundation Models"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [survey, hallucination, LLM, foundation-models, evaluation]
sources: [2309.05922v1.pdf]
---

## 概述

本文是一篇关于大基础模型（Large Foundation Models）中幻觉（hallucination）问题的综合性综述（2023）。文章不局限于大语言模型（LLM），还涵盖了视觉语言模型（VLM）、视频生成模型等多种基础模型中的幻觉现象。文章系统性地对幻觉进行了分类，并梳理了检测和缓解方法，为评测和改进模型可靠性提供了重要参考。

## 关键发现

- **幻觉是基础模型的普遍问题**：不仅限于 LLM，在视觉语言模型、音频模型等多种模态中都存在
- **幻觉分为两大类**：
  - **事实性幻觉（Factuality Hallucination）**：生成的内容与已知事实不符
  - **忠实性幻觉（Faithfulness Hallucination）**：生成的内容与输入上下文不一致
- **幻觉根源多样**：训练数据噪声、模型架构限制、解码策略偏差、知识过时等多因素共同导致
- **检测幻觉仍是开放难题**：自动检测方法的准确率有限，尤其在需要专业领域知识的场景下
- **缓解方法效果有限**：现有方法（如检索增强生成 RAG、人类反馈强化学习 RLHF）能减少但无法消除幻觉

## 方法论

### 幻觉分类体系
- **按模态分**：文本、图像、视频、音频等
- **按类型分**：事实性幻觉 vs. 忠实性幻觉
- **按严重程度分**：轻微不准确 vs. 完全捏造

### 检测方法
- **基于知识库的检测**：将模型输出与外部知识源进行事实核查
- **基于模型自身的检测**：利用模型不确定性、一致性检验等内在信号
- **基于 NLI 的检测**：使用自然语言推理模型判断输出与输入之间的蕴含关系
- **人工评测**：人类标注者对幻觉进行判别和分类

### 缓解方法
- **数据层面**：提高训练数据质量，过滤噪声数据
- **模型层面**：改进架构设计、引入外部知识
- **训练层面**：RLHF、DPO 等对齐技术
- **推理层面**：检索增强生成（RAG）、自我一致性解码（self-consistency）、思维链（Chain-of-Thought）

## 重要数据

- 综述梳理了 **多种模态**中的幻觉表现形式
- 汇总了 **主要的幻觉检测 benchmark**：TruthfulQA、HaluEval、FActScore 等
- 对比了不同 LLM 在幻觉率上的差异
- 分析了 **检索增强生成（RAG）** 对减少幻觉的定量效果

## 局限性

- 幻觉的定义在学界尚未完全统一，不同研究对幻觉的边界划分存在差异
- 对多模态幻觉（如图文不一致）的评测方法和 benchmark 仍不成熟
- 综述时间为 2023 年，未涵盖后续出现的新缓解方法（如更先进的 RAG 架构）
- 缺少对幻觉检测方法之间的系统性对比实验

## 相关页面

- [LLM 评测综述（Chang et al.）](llm-eval-survey-chang.md)
- [LLM-as-Judge 概念](../concepts/llm-as-judge.md)
- [LLMBar 元评测](llmbar.md)
