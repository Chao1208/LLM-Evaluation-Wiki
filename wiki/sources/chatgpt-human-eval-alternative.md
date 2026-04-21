---
title: "Can Large Language Models Be an Alternative to Human Evaluations?"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [LLM-as-judge, human-evaluation, ChatGPT, NLG, bias]
sources: [2023.acl-long.870.pdf]
---

## 概述

本文由 Chiang & Lee 发表于 ACL 2023，是 LLM-as-Judge 领域的早期重要研究之一。文章系统地研究了 ChatGPT 是否能替代人类评估者来评判自然语言生成（NLG）任务的输出质量。通过在多个 NLG 任务上对比 ChatGPT 与人类评估的结果，文章发现 LLM 评估在某些任务上与人类高度一致，但同时揭示了 LLM 评估固有的系统性偏见。

## 关键发现

- **ChatGPT 与人类判断在部分任务上高度相关**：在文本摘要、对话生成等任务中，ChatGPT 的评分与人类评分表现出较强的相关性
- **冗长偏见（Verbosity Bias）**：ChatGPT 倾向于给更长的回答更高的评分，即使长度并不意味着更高质量
- **自我增强偏见（Self-enhancement Bias）**：ChatGPT 倾向于给自己生成的内容打更高的分，对其他模型的输出评分偏低
- **任务依赖性**：LLM 评估的可靠性因任务类型不同而有显著差异，在主观性较强的任务中表现较差
- **评估框架设计影响结果**：prompt 的设计方式、评分标准的表述显著影响 LLM 评估的一致性和可靠性

## 方法论

- **实验设计**：在多个 NLG 任务上收集人类评估数据和 ChatGPT 评估数据，进行相关性分析
- **评测任务**：涵盖开放域对话、文本摘要、故事生成等多种 NLG 场景
- **对比维度**：
  - 系统级（system-level）相关性：不同模型的平均分排序一致性
  - 样本级（sample-level）相关性：单个样本评分的一致性
- **偏见分析**：
  - 控制变量实验检验冗长偏见
  - 交叉评估实验检验自我增强偏见
- **统计方法**：Spearman 和 Kendall 相关系数、Cohen's Kappa 等

## 重要数据

- 在文本摘要任务上，ChatGPT 与人类评估的系统级 Spearman 相关系数达到较高水平
- 在样本级评估中，相关性显著下降，说明 LLM 在细粒度判断上仍有不足
- 冗长偏见实验显示：控制质量不变时，增加回答长度可使 ChatGPT 评分显著提升
- 自我增强偏见：ChatGPT 对自己生成内容的评分比对其他模型的评分平均高出明显分差

## 局限性

- 仅以 ChatGPT 为研究对象，未覆盖其他 LLM 评估器（如 GPT-4、Claude 等）
- 实验规模相对有限，NLG 任务类型覆盖不够全面
- 未深入探讨如何通过改进 prompt 或引入 rubric 来缓解发现的偏见
- 人类评估本身也存在评估者间差异，文章对此讨论较少

## 相关页面

- [LLM-as-Judge 概念](../concepts/llm-as-judge.md)
- [LLMBar 元评测](llmbar.md)
- [LLM 评测综述（Chang et al.）](llm-eval-survey-chang.md)
- [Rubric-based 评测](../concepts/rubric-based-evaluation.md)
