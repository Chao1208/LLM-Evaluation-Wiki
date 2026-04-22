---
title: "Google"
type: entity
entity_type: organization
created: 2026-04-22
updated: 2026-04-22
tags: [机构, LLM, 评测, Google DeepMind, Gemini]
sources: []
---

# Google

## 基本信息

Google 是全球领先的科技公司，也是大型语言模型研究与评测领域的核心参与者。其 AI 研究力量经历了多次组织演变：Google Brain（2011 年成立）和 DeepMind（2014 年收购）于 2023 年合并为 **Google DeepMind**，成为统一的 AI 研究机构。

主要模型系列包括：
- **Gemini** 系列（2023 年至今）：多模态大模型，包括 Gemini 1.0 Ultra/Pro/Nano、Gemini 1.5 Pro 等
- **PaLM** 系列（2022-2023）：大规模语言模型，PaLM 540B 是 Chain-of-Thought 研究的核心实验模型
- **LaMDA**（2021）：对话语言模型

## 评测贡献

### IFEval — 指令遵循评测

由 Google 的 Zhou et al. 于 2023 年提出，IFEval 设计了一套**可程序化验证的指令集合**，包含约 500 个可验证指令、25 种指令类型。核心创新在于无需人工评估或 LLM 评估即可客观判断指令遵循情况，是目前最广泛使用的指令遵循 benchmark 之一。

（来源：[IFEval 论文摘要](../sources/ifeval.md)）

### Chain-of-Thought Prompting

由 Google Research 的 Jason Wei 等人于 2022 年提出（NeurIPS 2022），通过在 few-shot 示例中提供中间推理步骤来引导 LLM 进行复杂推理。关键发现包括：
- CoT 是**涌现能力**（Emergent Ability），仅在约 100B 参数以上的模型中显现
- 在 GSM8K 数学基准上，PaLM 540B + CoT 准确率从 17.9% 提升至 56.9%（**+39pp**）
- CoT 已成为 LLM 推理和评测领域最基础的技术之一

（来源：[Chain-of-Thought 论文摘要](../sources/chain-of-thought.md)）

### 模型评测表现

Google 模型在主流 benchmark 上的表现（来源：[simple-evals 仓库](../sources/simple-evals-repo.md)）：

| 模型 | MMLU | GPQA | MATH | HumanEval | MGSM | DROP |
|------|------|------|------|-----------|------|------|
| Gemini 1.0 Ultra | 83.7 | — | 53.2 | 74.4 | 79.0 | 82.4 |
| Gemini 1.5 Pro | 81.9 | — | 58.5 | 71.9 | 88.7 | 78.9 |

## 核心论文

### Chain-of-Thought Prompting（2022）
- 提出 CoT 推理方法，NeurIPS 2022
- PaLM 540B + CoT 在 GSM8K 上接近当时有监督 SOTA
- 详见：[Chain-of-Thought 论文摘要](../sources/chain-of-thought.md)

### IFEval（2023）
- 约 500 个可程序化验证的指令，25 种指令类型
- 无需人工评估或 LLM Judge，完全自动化评测
- 详见：[IFEval 论文摘要](../sources/ifeval.md)

## 相关页面

- [IFEval 论文摘要](../sources/ifeval.md)
- [Chain-of-Thought Prompting](../sources/chain-of-thought.md)
- [MMLU](../sources/mmlu.md)
- [simple-evals 仓库](../sources/simple-evals-repo.md)
- [OpenAI](openai.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
