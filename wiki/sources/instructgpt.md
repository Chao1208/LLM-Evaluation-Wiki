---
title: "Training Language Models to Follow Instructions with Human Feedback (InstructGPT)"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [RLHF, alignment, instruction-following, reward-model, PPO, human-feedback, 2022]
sources: [2203.02155-instructgpt.pdf]
---

## 概述

本文由 OpenAI（Long Ouyang, Jeff Wu, Xu Jiang 等）于 2022 年 3 月发表（arXiv: 2203.02155，NeurIPS 2022），提出了 InstructGPT——通过人类反馈的强化学习（RLHF）将语言模型与用户意图对齐的里程碑式工作。核心发现：仅 1.3B 参数的 InstructGPT 在人类偏好评测中优于 175B 参数的 GPT-3，证明了对齐技术能以极小的参数代价带来巨大的用户体验提升。该工作奠定了后续 ChatGPT 和 GPT-4 的技术基础。

## 关键发现

- **小模型胜大模型**：1.3B InstructGPT 的人类偏好率显著高于 175B GPT-3，证明对齐比单纯扩大规模更有效
- **三阶段训练流程**：SFT → 奖励模型训练 → PPO 强化学习，成为后续 RLHF 工作的标准范式
- **标注者偏好一致**：标注者与研究人员在偏好上高度一致（研究人员偏好 InstructGPT 的比例远高于 GPT-3）
- **真实性改善**：InstructGPT 在 TruthfulQA 基准上的事实性显著优于 GPT-3
- **毒性降低**：InstructGPT 生成有毒内容的概率降低约 **25%**（在 RealToxicityPrompts 上）
- **"对齐税"有限**：RLHF 训练仅在少数学术 NLP 基准上造成轻微性能下降（alignment tax），在大多数任务上保持或提升性能

## 方法论

### 三阶段训练流程

1. **监督微调（SFT）**：使用标注者编写的示范数据（约 13,000 条）对 GPT-3 进行微调
2. **奖励模型训练（RM）**：标注者对模型输出进行排序，训练 6B 参数的奖励模型学习人类偏好
3. **PPO 强化学习**：使用奖励模型作为奖励信号，通过 Proximal Policy Optimization 优化策略模型

### 数据收集

- **Prompt 来源**：OpenAI API 用户提交的真实请求（经过去标识化和筛选）
- **标注团队**：约 40 名合同标注者，经过严格筛选和培训
- **标注类型**：示范数据（demonstration）+ 比较数据（comparison/ranking）
- 标注者间一致率约 **73%**（比较任务）

### 评估方法

- **人类偏好评测**：标注者在成对对比中选择偏好的输出
- **自动评测基准**：TruthfulQA（真实性）、RealToxicityPrompts（毒性）、标准 NLP 基准
- **公共 NLP 数据集**：评估"对齐税"对传统 NLP 任务性能的影响

## 重要数据

| 模型 | 参数量 | 人类偏好胜率（vs GPT-3 175B）|
|------|--------|----------------------------|
| GPT-3 (SFT, 1.3B) | 1.3B | 基线 |
| InstructGPT (PPO, 1.3B) | 1.3B | 显著优于 175B GPT-3 |
| InstructGPT (PPO-ptx, 175B) | 175B | 最优 |

| 评测维度 | InstructGPT 改善 |
|----------|-----------------|
| 有用性（Helpfulness） | 显著优于 GPT-3 |
| 真实性（TruthfulQA） | 真实+有信息的回答比例提升 ~2× |
| 无害性（RealToxicityPrompts） | 毒性降低 ~25% |
| 对齐税（NLP 基准退化） | 轻微（PPO-ptx 版本可进一步缓解） |

| 数据规模 | 数量 |
|----------|------|
| SFT 示范数据 | ~13,000 条 |
| RM 比较数据 | ~33,000 条（训练） |
| 标注者 | ~40 人 |
| PPO prompt 数据 | ~31,000 条 |

## 局限性

- 标注者群体相对有限（约 40 人），可能无法充分代表全球用户的多样化偏好
- 对齐的是标注者偏好而非"真正的人类价值观"，存在代理目标（proxy objective）问题
- RLHF 可能导致模型过度优化奖励模型的偏好，而非真正提升质量（reward hacking）
- 在安全性方面仍不完善，InstructGPT 仍可被引导生成有害内容
- 训练数据主要为英语，多语言对齐效果未充分验证
- PPO 训练过程不稳定，需要精心调参

## 相关页面

- [OpenAI](../entities/openai.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [Rubric-based 评测方法](../concepts/rubric-based-evaluation.md)
