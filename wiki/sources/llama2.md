---
title: "Llama 2: Open Foundation and Fine-Tuned Chat Models"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [大模型, Llama, Meta, RLHF, 安全评测, 开源模型, 对齐]
sources: [2307.09288-llama2.pdf]
---

## 概述

Llama 2（Meta GenAI, 2023 年 7 月）是一系列预训练和微调的开源大语言模型，规模从 7B 到 70B 参数。微调版本 **Llama 2-Chat** 专门针对对话场景优化，在人工评测的有用性和安全性上优于大多数开源模型，接近部分闭源模型水平。

这是开源 LLM 发展的里程碑之作，提供了迄今为止最详尽的微调方法论和安全评测体系描述。

## 关键发现

1. **"Quality Is All You Need"**：SFT 阶段仅需 27,540 条高质量标注即可达到高水平，少而精优于多而杂
2. **Llama 2 70B 接近 GPT-3.5**：MMLU 68.9 vs 70.0，但与 GPT-4 仍有显著差距
3. **RLHF 迭代改进显著**：通过持续收集偏好数据并迭代训练奖励模型，Llama 2-Chat 逐版本改进
4. **Ghost Attention (GAtt)**：新技术维持多轮对话中的系统提示一致性
5. **安全与有用性存在张力**：需要两个独立的奖励模型分别优化

## 模型规格

| 属性 | Llama 2 |
|------|---------|
| 参数规模 | 7B / 13B / 34B / 70B |
| 训练数据 | 2T tokens（公开数据新混合） |
| 上下文长度 | 4K（Llama 1 为 2K） |
| 架构改进 | GQA（34B/70B）、RoPE |
| 训练算力 | 3.3M GPU 小时（A100） |
| 碳排放 | 539 tCO₂eq |

## 微调流程

### SFT（监督微调）

- 数据：27,540 条高质量标注（vendor 采集 + 人工筛选）
- 关键洞察：不同标注平台/供应商的数据质量差异显著影响下游性能
- 仅训练 answer tokens（零化 prompt tokens 的 loss）
- 2 epochs，sequence length 4096

### RLHF（人类反馈强化学习）

**奖励模型**：
- 二元偏好比较 + 偏好程度标签（significantly better / slightly better / negligibly better / unsure）
- 两个独立 RM：Helpfulness RM 和 Safety RM
- 迭代收集数据：每轮使用最新 Llama 2-Chat 生成样本

**训练策略**：
- **Rejection Sampling（拒绝采样）**：从 K 个候选中选最优
- **PPO（近端策略优化）**：标准 RLHF PPO 训练
- 实践中先用 rejection sampling 迭代，再用 PPO 精调

### Ghost Attention (GAtt)

解决多轮对话中系统提示（如 persona、安全约束）被"遗忘"的问题：
- 在训练数据中将系统消息拼接到每轮用户消息前
- 使用合成数据训练，辅以 dropout 策略
- 有效提升多轮对话的一致性

## 评测表现

### 预训练模型（学术基准）

| 基准 | Llama 2 70B | GPT-3.5 | GPT-4 |
|------|------------|---------|-------|
| MMLU (5-shot) | 68.9 | 70.0 | **86.4** |
| GSM8K (8-shot) | 56.8 | 57.1 | **92.0** |
| HumanEval (0-shot) | 29.9 | 48.1 | **67.0** |
| BIG-Bench Hard (3-shot) | 51.2 | — | — |

### 人工评测（Llama 2-Chat）

- 对比 ChatGPT-0301：70B-chat 胜率 36%、平 31.5%、负 32.5%
- 对比 Falcon 40B-instruct：70B-chat 胜率 76.3%
- 安全性违规率：70B-chat 约 4%（最低），ChatGPT 约 8%

## 安全评测体系

论文提供了详尽的安全评测方法论：

1. **Safety 预训练**：过滤有害数据，上采样事实来源
2. **Safety 微调**：安全相关 SFT 数据（指南 + 对抗样本）
3. **Red Teaming（红队测试）**：350+ 人参与，覆盖多个风险类别
4. **Safety 评估**：
   - ~2,000 对抗性 prompt（单轮 + 多轮）
   - 安全分类器自动判定 + 人工抽检
   - 按风险类别（暴力、性、隐私等）细分报告

## 与评测相关的启示

1. **RLHF 偏好数据收集方法论**：偏好程度四级标签设计值得评测场景借鉴
2. **Safety 评测框架**：Red teaming + 多维度安全分类器的组合方法成为行业标准
3. **人工评测 vs 自动评测**：论文同时使用人工评测和 GPT-4 Judge，提供了对比视角
4. **数据质量洞察**："Quality Is All You Need" 与 [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)中强调的"质量重于数量"一致
5. **碳排放透明度**：为模型训练的环境影响评估提供了范式

## 相关页面

- [Meta](../entities/meta.md)
- [InstructGPT](../sources/instructgpt.md)
- [GPT-4 Technical Report](../sources/gpt4-technical-report.md)
- [MT-Bench & Chatbot Arena](../sources/mt-bench-chatbot-arena.md)
- [MMLU](../sources/mmlu.md)
- [RewardBench](../sources/rewardbench.md)
- [LLM-as-Judge 方法论](../concepts/llm-as-judge.md)
- [Chain-of-Thought Prompting](../sources/chain-of-thought.md)

（来源：[2307.09288-llama2.pdf](../../raw/papers/2307.09288-llama2.pdf)）
