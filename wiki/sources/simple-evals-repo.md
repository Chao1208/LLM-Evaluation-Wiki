---
title: "OpenAI simple-evals 仓库（含 HealthBench）"
type: source
created: 2026-04-09
updated: 2026-04-09
tags: [OpenAI, simple-evals, HealthBench, 模型跑分, 多语言, 仓库]
sources: [raw/benchmarks/HealthBench-simple-evals/README.md, raw/benchmarks/HealthBench-simple-evals/README_zh.md, raw/benchmarks/HealthBench-simple-evals/multilingual_mmlu_benchmark_results.md]
---

# OpenAI simple-evals 仓库（含 HealthBench）

## 概述

OpenAI 开源的轻量级语言模型评测库。开源目的是确保发布模型时公布的准确率数据具有透明度。仓库托管了 HealthBench、BrowseComp、SimpleQA 的参考实现。

**弃用声明（2025年7月）**：simple-evals 不再为新模型或 benchmark 结果更新，但继续维护 HealthBench/BrowseComp/SimpleQA 实现。

## 关键发现

### 1. 评测哲学

- 强调**零样本（zero-shot）+ 思维链（chain-of-thought）**设定
- 使用简单指令（如"解决以下选择题"），不使用 few-shot 或角色扮演 prompt
- 认为这种方式更能反映模型在实际使用中的表现
- 相比 few-shot/角色扮演 prompt，这是从"评估 base model"向"评估 chat model"的范式转变

### 2. 包含的评测基准（8 个）

| 评测 | 领域 | 参考 |
|------|------|------|
| MMLU | 大规模多任务语言理解 | arxiv:2009.03300 |
| MATH (MATH-500) | 数学问题求解 | arxiv:2103.03874 |
| GPQA | 研究生水平问答 | arxiv:2311.12022 |
| DROP | 离散推理阅读理解 | arxiv:1903.00161 |
| MGSM | 多语言小学数学 | arxiv:2210.03057 |
| HumanEval | 代码生成 | arxiv:2107.03374 |
| SimpleQA | 短文事实性 | openai.com |
| HealthBench | 医疗健康 LLM 评测 | openai.com |

注：BrowseComp（浏览代理基准）也在仓库中，但未列入主表。

### 3. 模型跑分数据（核心表格）

#### OpenAI 推理模型系列

| 模型 | MMLU | GPQA | MATH | HumanEval | MGSM | DROP | SimpleQA |
|------|------|------|------|-----------|------|------|---------|
| o3-high | 93.3 | 83.4 | 98.1 | 88.4 | 92.0 | 89.8 | 48.6 |
| o3 | 92.9 | 82.8 | 97.8 | 87.4 | 92.3 | 80.6 | 49.4 |
| o3-low | 92.8 | 78.6 | 96.9 | 87.3 | 91.9 | 82.3 | 49.4 |
| o4-mini-high | 90.3 | 81.3 | 98.2 | 99.3 | 93.5 | 78.1 | 19.3 |
| o4-mini | 90.0 | 77.6 | 97.5 | 97.3 | 93.7 | 77.7 | 20.2 |
| o4-mini-low | 89.5 | 73.6 | 96.2 | 95.9 | 93.0 | 76.0 | 20.2 |

#### OpenAI o3-mini / o1 系列

| 模型 | MMLU | GPQA | MATH | HumanEval | MGSM | DROP | SimpleQA |
|------|------|------|------|-----------|------|------|---------|
| o3-mini-high | 86.9 | 77.2 | 97.9 | 97.6 | 92.0 | 80.6 | 13.8 |
| o3-mini | 85.9 | 74.9 | 97.3 | 96.3 | 90.8 | 79.2 | 13.4 |
| o1 | 91.8 | 75.7 | 96.4 | — | 89.3 | 90.2 | 42.6 |
| o1-preview | 90.8 | 73.3 | 85.5 | 92.4 | 90.8 | 74.8 | 42.4 |
| o1-mini | 85.2 | 60.0 | 90.0 | 92.4 | 89.9 | 83.9 | 7.6 |

#### OpenAI GPT-4 系列

| 模型 | MMLU | GPQA | MATH | HumanEval | MGSM | DROP | SimpleQA |
|------|------|------|------|-----------|------|------|---------|
| gpt-4.1 | 90.2 | 66.3 | 82.1 | 94.5 | 86.9 | 79.4 | 41.6 |
| gpt-4.1-mini | 87.5 | 65.0 | 81.4 | 93.8 | 88.2 | 81.0 | 16.8 |
| gpt-4.1-nano | 80.1 | 50.3 | 62.3 | 87.0 | 73.0 | 82.2 | 7.6 |
| gpt-4.5-preview | 90.8 | 69.5 | 87.1 | 88.6 | 86.9 | 83.4 | 62.5 |
| gpt-4o (2024-11) | 85.7 | 46.0 | 68.5 | 90.2 | 90.3 | 81.5 | 38.8 |
| gpt-4o (2024-08) | 88.7 | 53.1 | 75.9 | 90.2 | 90.0 | 79.8 | 40.1 |
| gpt-4o-mini | 82.0 | 40.2 | 70.2 | 87.2 | 87.0 | 79.7 | 9.5 |
| gpt-4-turbo | 86.7 | 49.3 | 73.4 | 88.2 | 89.6 | 86.0 | 24.2 |

#### 第三方模型（OpenAI 报告）

| 模型 | MMLU | GPQA | MATH | HumanEval | MGSM | DROP | SimpleQA |
|------|------|------|------|-----------|------|------|---------|
| Claude 3.5 Sonnet | 88.3 | 59.4 | 71.1 | 92.0 | 91.6 | 87.1 | 28.9 |
| Claude 3 Opus | 86.8 | 50.4 | 60.1 | 84.9 | 90.7 | 83.1 | 23.5 |
| Llama 3.1 405b | 88.6 | 50.7 | 73.8 | 89.0 | 91.6 | 84.8 | — |
| Llama 3.1 70b | 82.0 | 41.7 | 68.0 | 80.5 | 86.9 | 79.6 | — |
| Llama 3.1 8b | 68.4 | 30.4 | 51.9 | 72.6 | 68.9 | 59.5 | — |
| Grok 2 | 87.5 | 56.0 | 76.1 | 88.4 | — | — | — |
| Gemini 1.0 Ultra | 83.7 | — | 53.2 | 74.4 | 79.0 | 82.4 | — |
| Gemini 1.5 Pro | 81.9 | — | 58.5 | 71.9 | 88.7 | 78.9 | — |

### 4. 多语言 MMLU 评测结果

使用**人工翻译**（非机翻）将 MMLU 测试集翻译成 14 种语言。关键发现：

| 语言 | o3-high | o1 | o4-mini-high | gpt-4.1 | gpt-4o (2024-11) |
|------|---------|-----|-------------|---------|-----------------|
| 中文（简体） | 0.893 | 0.889 | 0.869 | 0.861 | 0.842 |
| 日语 | 0.890 | 0.889 | 0.869 | 0.856 | 0.835 |
| 法语 | 0.906 | 0.893 | 0.874 | 0.870 | 0.846 |
| 阿拉伯语 | 0.904 | 0.890 | 0.861 | 0.844 | 0.831 |
| 斯瓦希里语 | 0.860 | 0.854 | 0.813 | 0.795 | 0.779 |
| 约鲁巴语 | 0.780 | 0.754 | 0.708 | 0.647 | 0.621 |
| **平均** | **0.888** | 0.877 | 0.852 | 0.837 | 0.814 |

- o3-high 在所有 14 种语言上均为最优
- 低资源语言（约鲁巴语 0.780 vs 意大利语 0.912）差距明显
- 推理模型（o3/o4-mini）在多语言上的优势贯穿各语言

### 5. 关键洞察

- **o4-mini-high 的 HumanEval 达 99.3%**，是所有模型中最高的代码生成得分
- **gpt-4.5-preview 的 SimpleQA 达 62.5%**，事实性显著领先其他模型
- **o3 系列不支持 system prompt**，o-series 推理模型架构特殊
- **MGSM 和 DROP 被认为对新模型已饱和**，但仍然报告以保持完整性
- **o3/o4-mini 测试时未启用任何 tools**

## 问题与思考

1. 仓库已弃用但仍维护 HealthBench——说明 HealthBench 的长期价值被认可
2. 零样本 + CoT 的评测范式值得关注，是否所有 benchmark 都应该转向这种方式？
3. 第三方模型数据标注为 "unknown" prompt 设定，可比性存疑
4. SimpleQA 上推理模型（o3/o4-mini）反而不如非推理模型（gpt-4.5），可能是过度推理导致

## 相关页面

- [HealthBench](../benchmarks/healthbench.md)
- [OpenAI](../entities/openai.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
