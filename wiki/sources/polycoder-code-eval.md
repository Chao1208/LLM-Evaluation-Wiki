---
title: "A Systematic Evaluation of Large Language Models of Code"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [benchmark, code-generation, LLM, PolyCoder, evaluation]
sources: [3520312.3534862.pdf]
---

## 概述

该论文由 Xu 等人发表于 MAPS '22（ACM SIGPLAN International Symposium on Memory Management），对代码大语言模型进行了系统性评估。研究团队开发了 PolyCoder——一个在 249GB、覆盖 12 种编程语言的代码语料上训练的开源代码 LLM，并将其与 Codex、GPT-J、GPT-NeoX、CodeParrot 等模型在 HumanEval 和 MBPP 等基准上进行了全面对比。

## 关键发现

- PolyCoder 作为开源模型，在 C 语言生成任务上表现优于 Codex 等闭源模型
- Codex 在 Python 代码生成上仍然保持显著优势
- 模型在不同编程语言上的表现差异很大，训练数据中语言的比例影响显著
- 模型规模增大带来的代码生成能力提升呈现边际递减趋势
- 开源代码 LLM 在特定语言上具有与闭源模型竞争的潜力

## 方法论

- **PolyCoder 模型**:
  - 训练数据：249GB 代码语料，覆盖 12 种编程语言
  - 基于 GPT-2 架构，参数规模为 2.7B
  - 完全开源，支持复现
- **对比模型**: Codex、GPT-J（6B）、GPT-NeoX（20B）、CodeParrot
- **评测基准**:
  - HumanEval：164 道 Python 编程题，测试功能正确性（pass@k）
  - MBPP（Mostly Basic Python Problems）：974 道基础 Python 编程题
  - 跨语言的 perplexity 评测
- **评测指标**: pass@k（k=1, 10, 100）、perplexity
- **跨语言评测**: 在 12 种编程语言上测试 perplexity，分析语言间差异

## 重要数据

- PolyCoder：2.7B 参数，249GB 训练数据，12 种编程语言
- HumanEval（164 题）和 MBPP（974 题）基准评测
- PolyCoder 在 C 语言上优于 Codex
- Codex 在 Python 上保持领先
- 跨 12 种语言的 perplexity 对比

## 局限性

- PolyCoder 的参数规模（2.7B）远小于 Codex（12B），对比不完全公平
- HumanEval 和 MBPP 偏重 Python，其他语言的评测基准缺乏
- 评测主要关注代码生成的功能正确性，未充分考虑代码质量、效率和安全性
- 训练数据的许可和合规问题未深入讨论
- 评测场景以独立函数生成为主，未测试项目级代码生成能力

## 相关页面

- [LLM-as-Judge](../concepts/llm-as-judge.md)
