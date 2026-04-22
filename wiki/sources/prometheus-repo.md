---
title: "Prometheus: 开源细粒度评测语言模型 (GitHub 仓库)"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [LLM-as-Judge, 评测模型, Rubric, 开源, Prometheus, Llama, 细粒度评测]
sources: [raw/github/prometheus/README.md]
---

## 概述

Prometheus 是由 KAIST AI 团队开发的开源评测语言模型（Evaluator LM），专门设计用于基于自定义评分标准（score rubric）的细粒度评测。作为人工评测和 GPT-4 评测的替代方案，Prometheus 具有开源、可复现、低成本的优势。

- 论文：[Prometheus: Inducing Fine-grained Evaluation Capability in Language Models](https://arxiv.org/abs/2310.08491)
- 训练数据：[Feedback Collection](https://huggingface.co/datasets/kaist-ai/Feedback-Collection) -- 100K 反馈数据集
- 基础模型：基于 Llama-2-Chat 架构
- 训练框架：基于 Facebook 的 [llama-recipes](https://github.com/facebookresearch/llama-recipes)
- 推理框架：HuggingFace [TGI](https://github.com/huggingface/text-generation-inference)

## 关键特性

1. **Rubric 驱动的细粒度评测**：核心创新在于通过自定义 score rubric 实现特定评测标准下的细粒度评分（1-5 分制）
2. **结构化输入输出**：
   - 输入：指令（instruction）、待评测响应（response）、参考答案（reference answer，5 分标准）、评分标准（score rubric，1-5 分描述）
   - 输出：详细反馈（feedback）+ 评分结果（`[RESULT] score`）
3. **开源可复现**：完全开源模型权重、训练数据和代码，解决 GPT-4 评测的黑箱和成本问题
4. **Feedback Collection 数据集**：约 100K 条训练样本，包含指令-响应-反馈-评分的完整四元组
5. **支持绝对评分和相对评分**：
   - `run_absolute_scoring.py` -- 对单个响应给出 1-5 分
   - `run_relative_scoring.py` -- 对两个响应进行成对比较

## 技术架构

### 输入 Prompt 模板

```
###Task Description:
An instruction, a response to evaluate, a reference answer that gets a score of 5,
and a score rubric representing evaluation criteria are given.
1. Write detailed feedback based on the score rubric
2. Write a score between 1 and 5
3. Output format: "Feedback: (feedback) [RESULT] (score)"
4. No other opening/closing/explanations

###The instruction to evaluate: {instruction}
###Response to evaluate: {response}
###Reference Answer (Score 5): {reference_answer}
###Score Rubrics:
[{criteria}]
Score 1: {description} ... Score 5: {description}
###Feedback:
```

### 训练流程

- 使用 `torchrun` 分布式训练，支持 FSDP（Fully Sharded Data Parallel）
- 支持 BF16 精度和低 CPU 内存 FSDP
- 数据集使用 `feedback_collection_freeform_dataset`

### 推理流程

- 必须应用 Llama-2-Chat 的 conversation template（不应用会导致异常行为）
- 使用 FastChat 的 `get_conv_template("llama-2")` 构建对话格式
- 系统消息：`"You are a fair evaluator language model."`
- 支持通过 HuggingFace TGI 进行快速推理

## 与评测相关的启示

1. **开源 LLM-as-Judge 的先驱**：Prometheus 证明了通过专门训练可以让开源 LLM 达到接近 GPT-4 的评测能力，为 [LLM-as-Judge](../concepts/llm-as-judge.md) 方法论提供了重要的开源替代方案
2. **Rubric 驱动评测的实践**：与 [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md) 高度契合，Prometheus 的 score rubric 设计（5 级分描述）是自动化 rubric 评测的典型范例
3. **参考答案的必要性**：Prometheus 要求提供 5 分参考答案，这既提供了评分锚点，也意味着评测成本包含参考答案构建
4. **反馈+评分双输出**：不仅给出分数，还生成详细的文字反馈，增强了评测的可解释性和可审计性
5. **后续发展**：[Prometheus 2](../sources/prometheus-2.md) 进一步支持直接评分+成对排名双模式，并引入权重合并技术
6. **Conversation Template 的重要性**：推理时必须正确应用对话模板，否则输出质量会大幅下降，这对评测系统的工程实践有警示意义
7. **评测民主化**：开源评测模型降低了高质量自动评测的门槛，使小团队也能进行可靠的模型评估

## 相关页面

- [Prometheus 论文摘要](../sources/prometheus.md) -- 论文内容的详细摘要
- [Prometheus 2](../sources/prometheus-2.md) -- 后续升级版本（EMNLP 2024）
- [LLM-as-Judge](../concepts/llm-as-judge.md) -- Prometheus 所属的评测范式
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md) -- Prometheus 的核心方法论基础
- [Rubric Is All You Need](../sources/rubric-is-all-you-need.md) -- 问题特定 Rubric 提升代码评测
- [RL with Rubric Anchors](../sources/rl-rubric-anchors.md) -- Rubric 锚点指导 RL 训练
- [MT-Bench & Chatbot Arena](../sources/mt-bench-chatbot-arena.md) -- Prometheus 评测实验中使用的基准之一
- [RewardBench](../sources/rewardbench.md) -- 奖励模型评测基准
