---
title: "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [prompting, reasoning, chain-of-thought, few-shot, emergent-ability, 2022]
sources: [2201.11903-chain-of-thought.pdf]
---

## 概述

本文由 Google Research（Jason Wei, Xuezhi Wang, Dale Schuurmans 等）于 2022 年 1 月发表（arXiv: 2201.11903，NeurIPS 2022），提出了 **Chain-of-Thought（CoT）Prompting**——通过在 few-shot 示例中提供中间推理步骤来引导大型语言模型进行复杂推理。这一方法极其简单（仅需在 prompt 中添加推理链示例），却在算术推理、常识推理和符号推理等任务上带来了显著的性能提升。CoT 已成为 LLM 推理和评测领域最基础、最广泛使用的技术之一。

## 关键发现

- **涌现能力（Emergent Ability）**：CoT 的效果仅在足够大的模型（约 **100B 参数**以上）中显现，小模型使用 CoT 甚至可能降低性能
- **GSM8K 突破**：在 GSM8K 数学基准上，PaLM 540B + CoT 达到 **56.9%** 的准确率（标准 prompting 仅 17.9%），接近当时有监督 SOTA
- **跨任务通用性**：CoT 在算术推理（GSM8K、SVAMP、AQuA、MAWPS、ASDiv）、常识推理（CSQA、StrategyQA）和符号推理（Last Letter、Coin Flip）三大类任务上均有效
- **无需微调**：CoT 完全通过 prompting 实现，不需要额外的训练数据或模型参数更新
- **可解释性**：中间推理步骤提供了模型决策过程的窗口，便于调试和错误分析

## 方法论

### Chain-of-Thought Prompting

- 在标准 few-shot prompting 的基础上，在每个示例的问题和答案之间插入自然语言的推理步骤
- 不需要修改模型参数或训练过程，仅修改 prompt 格式
- 推理链由人类手工编写（8 个示例），风格为自然的解题思路

### 实验设计

- **模型覆盖**：GPT-3（davinci）、LaMDA（422M-137B）、PaLM（8B-540B）、Codex、UL2 20B
- **基准覆盖**：
  - 算术推理：GSM8K、SVAMP、ASDiv、AQuA、MAWPS、SingleEq、AddSub、MultiArith
  - 常识推理：CSQA（CommonsenseQA）、StrategyQA
  - 符号推理：Last Letter Concatenation、Coin Flip
- **消融实验**：仅方程（equation only）、仅变量（variable compute only）、推理后答案 vs. 答案后推理等变体

### 关键设计选择

- **Few-shot 设置**：使用 8 个 CoT 示例作为 prompt
- **贪心解码**（greedy decoding）用于主要实验
- **Self-consistency**（后续工作 2203.11171）进一步通过多次采样+多数投票提升性能

## 重要数据

| 基准 | 标准 Prompting (PaLM 540B) | CoT Prompting (PaLM 540B) | 提升幅度 |
|------|---------------------------|--------------------------|---------|
| GSM8K | 17.9% | 56.9% | +39.0pp |
| SVAMP | 79.0% | 79.0% | - |
| MAWPS (MultiArith) | 33.3% | 94.7% | +61.4pp |
| CSQA | 76.5% | 79.9% | +3.4pp |
| StrategyQA | 73.9% | 77.8% | +3.9pp |

| 模型规模与 CoT 效果 | 观察 |
|-------------------|------|
| <10B 参数 | CoT 无效或有害 |
| 10B-100B 参数 | CoT 效果不稳定 |
| >100B 参数 | CoT 显著有效（涌现） |

## 局限性

- **规模依赖**：CoT 仅在大模型上有效，无法惠及小模型，存在显著的"涌现门槛"
- **正确性无保证**：生成的推理链可能包含错误步骤但仍得出正确答案，或推理看似合理但结论错误
- **评估困难**：难以自动验证推理链的正确性（区别于仅验证最终答案）
- **示例敏感性**：不同的 CoT 示例可能导致显著不同的结果，最优示例的选择缺乏系统方法
- **成本增加**：生成更长的推理链增加了推理时间和计算成本
- **任务局限**：对不需要多步推理的简单任务，CoT 帮助有限

## 相关页面

- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [Rubric-based 评测方法](../concepts/rubric-based-evaluation.md)
