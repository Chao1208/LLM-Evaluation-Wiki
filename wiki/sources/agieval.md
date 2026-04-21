---
title: "AGIEval: A Human-Centric Benchmark for Evaluating Foundation Models"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [benchmark, human-centric, reasoning, exam, AGI]
sources: [2024.findings-naacl.149.pdf]
---

## 概述

AGIEval 是由 Zhong et al. 提出的以人类为中心的基础模型评测 benchmark，发表于 NAACL 2024 Findings。该 benchmark 的核心理念是利用真实的人类考试题目来评测模型的推理和理解能力，涵盖 SAT、GRE、LSAT、中国公务员考试、高考等 20 种标准化考试任务。AGIEval 是双语 benchmark（英文 + 中文），能够评测模型在标准化考试场景下的综合推理能力。

## 关键发现

- **LLM 在标准化考试中表现不均**：模型在某些考试（如 SAT 数学）中表现优异，但在需要深层推理的考试（如 LSAT 逻辑推理）中表现较差
- **中英文能力差距明显**：大部分模型在英文考试上的表现显著优于中文考试，反映了训练数据的语言偏向
- **Few-shot 有效但受限**：给出少量示例能提升表现，但在需要长链推理的题目上改善有限
- **Chain-of-Thought 提升推理表现**：使用 CoT 提示策略在数学和逻辑推理类考试中带来显著提升
- **考试类 benchmark 更贴近实际能力**：相比人工构造的 benchmark，真实考试题目更能反映模型的实际认知水平

## 方法论

### 考试任务设计
AGIEval 涵盖 **20 种考试任务**，分为以下类别：

**英文考试**：
- **SAT**：阅读理解、数学
- **GRE**：语言推理、定量推理
- **LSAT**：逻辑推理、分析推理、阅读理解
- **GMAT**：逻辑推理

**中文考试**：
- **高考（Gaokao）**：语文、数学、英语、物理、化学、生物、历史、地理
- **公务员考试**：行政能力测试、申论
- **法律职业资格考试**：法律知识

### 题型
- **选择题**（多数）：单选和多选
- **填空题**：数学和科学类
- **简答题**：开放式回答

### 评测方式
- 选择题：精确匹配（Exact Match）
- 填空题：答案匹配
- 简答题：人工评估或规则匹配
- 支持 zero-shot 和 few-shot 两种设置
- 支持 Chain-of-Thought（CoT）提示

### 模型测试
- 评测了 GPT-4、ChatGPT、Text-Davinci-003 等多个主流模型
- 对比了不同 prompt 策略的效果

## 重要数据

- 包含 **20 种考试任务**，共 **数千道题目**
- GPT-4 在多数英文考试中达到或超过人类平均水平
- GPT-4 在 LSAT 分析推理上的准确率仍低于人类高分段
- 中文考试上，各模型准确率普遍低于对应英文考试 **10-20 个百分点**
- CoT 策略在数学类考试上平均提升 **5-15 个百分点**

## 局限性

- 考试题目数量有限，统计显著性可能受影响
- 标准化考试侧重知识和推理，无法覆盖创造力、交互能力等维度
- 选择题格式可能使模型通过排除法等"技巧"获得较高分，不一定反映真正理解
- 部分考试题目可能已存在于模型训练数据中，存在数据泄露风险
- 中文考试的覆盖集中在中国考试体系，代表性有限

## 相关页面

- [HELM 整体性评测](helm.md)
- [IFEval 指令遵循评测](ifeval.md)
- [LLM 评测综述（Chang et al.）](llm-eval-survey-chang.md)
- [Rubric-based 评测](../concepts/rubric-based-evaluation.md)
