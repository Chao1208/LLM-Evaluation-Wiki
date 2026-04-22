---
title: "OpenAI"
type: entity
entity_type: organization
created: 2026-04-09
updated: 2026-04-22
tags: [机构, LLM, 评测, simple-evals]
sources: [raw/report/Rubric-Based推理数据调研报告.md, raw/benchmarks/HealthBench-simple-evals/README.md]
---

# OpenAI

## 基本信息

人工智能研究公司，GPT 系列模型的开发者。

## 评测贡献

- 开发了 [HealthBench](../benchmarks/healthbench.md)：医疗领域的 Rubric-Based 评测基准，5,000 对话，48,562 criteria
- HealthBench 采用 Consensus 机制（34 条通用准则 + 多医师共识），是医疗领域 LLM 评测的重要参考
- GPT-4.1 用作 HealthBench 的 [LLM Judge](../concepts/llm-as-judge.md)
- 开源了 simple-evals 评测框架，包含 MMLU、MATH-500、GPQA、DROP、MGSM、HumanEval、SimpleQA、BrowseComp、HealthBench 共 9 个评测基准
- 评测哲学：**零样本 + 思维链**，不使用 few-shot 或角色扮演 prompt

**注意**：simple-evals 已于 2025年7月宣布弃用（不再更新新模型结果），但继续维护 HealthBench/BrowseComp/SimpleQA 实现。

## 模型评测表现

### 推理模型系列（o-series）

| 模型 | MMLU | GPQA | MATH | HumanEval | MGSM | DROP | SimpleQA |
|------|------|------|------|-----------|------|------|---------|
| o3-high | 93.3 | 83.4 | 98.1 | 88.4 | 92.0 | 89.8 | 48.6 |
| o3 | 92.9 | 82.8 | 97.8 | 87.4 | 92.3 | 80.6 | 49.4 |
| o4-mini-high | 90.3 | 81.3 | 98.2 | **99.3** | 93.5 | 78.1 | 19.3 |
| o4-mini | 90.0 | 77.6 | 97.5 | 97.3 | 93.7 | 77.7 | 20.2 |
| o3-mini-high | 86.9 | 77.2 | 97.9 | 97.6 | 92.0 | 80.6 | 13.8 |
| o1 | 91.8 | 75.7 | 96.4 | — | 89.3 | 90.2 | 42.6 |

### GPT 系列

| 模型 | MMLU | GPQA | MATH | HumanEval | MGSM | DROP | SimpleQA |
|------|------|------|------|-----------|------|------|---------|
| gpt-4.5-preview | 90.8 | 69.5 | 87.1 | 88.6 | 86.9 | 83.4 | **62.5** |
| gpt-4.1 | 90.2 | 66.3 | 82.1 | 94.5 | 86.9 | 79.4 | 41.6 |
| gpt-4.1-mini | 87.5 | 65.0 | 81.4 | 93.8 | 88.2 | 81.0 | 16.8 |
| gpt-4.1-nano | 80.1 | 50.3 | 62.3 | 87.0 | 73.0 | 82.2 | 7.6 |
| gpt-4o (2024-11) | 85.7 | 46.0 | 68.5 | 90.2 | 90.3 | 81.5 | 38.8 |
| gpt-4o-mini | 82.0 | 40.2 | 70.2 | 87.2 | 87.0 | 79.7 | 9.5 |

（来源：[simple-evals 仓库](../sources/simple-evals-repo.md)）

### 关键洞察

- o4-mini-high 的 HumanEval 达 99.3%，是所有模型中最高的代码生成得分
- gpt-4.5-preview 的 SimpleQA 达 62.5%，事实性显著领先，可能是推理模型过度推理的反面
- o-series 推理模型**不支持 system prompt**
- MGSM 和 DROP 被认为对新模型已饱和
- o3/o4-mini 测试时未启用任何 tools

## 核心论文

### InstructGPT（2022）
- RLHF 对齐里程碑：1.3B InstructGPT 优于 175B GPT-3
- SFT → RM → PPO 三阶段训练流程
- 详见：[InstructGPT 论文摘要](../sources/instructgpt.md)

### GPT-4 Technical Report（2023）
- 多模态大模型，Bar Exam 90th 百分位，[MMLU](../sources/mmlu.md) 86.4%
- 可预测扩展（Predictable Scaling）
- 详见：[GPT-4 论文摘要](../sources/gpt4-technical-report.md)

## 相关页面

- [HealthBench](../benchmarks/healthbench.md)
- [InstructGPT 论文](../sources/instructgpt.md)
- [GPT-4 Technical Report](../sources/gpt4-technical-report.md)
- [MT-Bench & Chatbot Arena](../sources/mt-bench-chatbot-arena.md)
- [MMLU](../sources/mmlu.md)
- [Chain-of-Thought Prompting](../sources/chain-of-thought.md)
- [HumanEval 仓库](../sources/human-eval-repo.md)
- [RewardBench](../sources/rewardbench.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
- [simple-evals 仓库](../sources/simple-evals-repo.md)
