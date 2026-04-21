---
title: "RewardBench: Evaluating Reward Models for Language Modeling"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [benchmark, reward-model, RLHF, evaluation, alignment]
sources: [2025.findings-naacl.96.pdf]
---

## 概述

RewardBench 是由 Lambert et al. 提出的奖励模型（Reward Model）评测 benchmark，发表于 NAACL 2025 Findings。在基于人类反馈的强化学习（RLHF）流程中，奖励模型是连接人类偏好与模型训练的关键组件。RewardBench 构建了一套系统性的测试集，用于评估奖励模型在对话、安全、推理等多个维度上的判断能力，并发现不同奖励模型之间存在显著的性能差距。

## 关键发现

- **奖励模型之间差距显著**：不同奖励模型在 RewardBench 上的表现差异巨大，说明奖励模型的选择对 RLHF 训练结果有关键影响
- **生成式模型作为评判器具有竞争力**：直接使用生成式 LLM（如 GPT-4）作为评判器的表现与专门训练的奖励模型相当甚至更优
- **安全类任务是薄弱环节**：大部分奖励模型在安全相关的判断（如拒绝有害请求）上表现较差
- **推理类任务区分度高**：数学推理和代码推理任务能有效区分不同奖励模型的能力
- **模型规模与奖励模型质量正相关**：基于更大基座模型训练的奖励模型通常表现更好

## 方法论

### 测试集构建
RewardBench 的测试集分为四个子集：

1. **Chat（对话）**：评估奖励模型对日常对话质量的判断能力
   - 包含 helpfulness（有用性）、harmlessness（无害性）等维度
2. **Safety（安全）**：评估对有害请求的识别能力
   - 模型应偏好拒绝有害请求的回答
   - 涵盖毒性、歧视、危险行为等场景
3. **Reasoning（推理）**：评估对推理质量的判断
   - 数学推理：正确 vs. 错误的计算过程
   - 代码推理：可运行 vs. 有 bug 的代码
4. **Prior Sets（已有数据集）**：整合已有偏好数据集的子集
   - 来自 Anthropic HH、Stanford SHP 等

### 评测范式
- **成对偏好判断**：给定一个 prompt 和两个回答（chosen 和 rejected），奖励模型需要给 chosen 回答更高的分数
- **准确率**：正确偏好 chosen 回答的比例

### 评测对象
- 专门训练的分类式奖励模型（如 OpenAssistant RM）
- DPO 训练的模型（隐式奖励模型）
- 生成式模型直接作为评判器（如 GPT-4、Claude）

## 重要数据

- 测试集包含 **数千个偏好对**，覆盖四大类别
- 最佳奖励模型与最差模型之间的准确率差距超过 **20 个百分点**
- 生成式模型（GPT-4）在多个子集上的表现与顶尖奖励模型持平
- 安全子集是所有模型表现最差的类别，平均准确率显著低于其他子集
- 推理子集的区分度最高，模型间方差最大

## 局限性

- 成对偏好判断是简化的评测范式，实际 RLHF 中奖励信号更复杂
- chosen/rejected 标注可能存在噪声，影响 benchmark 质量
- 测试集覆盖的场景有限，可能无法反映奖励模型在长尾场景中的表现
- 未评测奖励模型在实际 RLHF 训练中的下游效果
- 生成式模型作为评判器的成本和延迟问题未充分讨论

## 相关页面

- [LLM-as-Judge 概念](../concepts/llm-as-judge.md)
- [LLMBar 元评测](llmbar.md)
- [Rubric-based 评测](../concepts/rubric-based-evaluation.md)
- [LLM 评测综述（Chang et al.）](llm-eval-survey-chang.md)
