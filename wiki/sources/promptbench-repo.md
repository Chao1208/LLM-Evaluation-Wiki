---
title: "PromptBench: LLM 评测与 Prompt 鲁棒性统一库"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [评测框架, Microsoft, prompt-attack, 鲁棒性, prompt-engineering, 动态评测, 多模态]
sources: [raw/github/promptbench/README.md]
---

## 概述

PromptBench 是由 **Microsoft Research** 开发的 PyTorch-based Python 评测库，GitHub 仓库为 `microsoft/promptbench`。项目由 Zhu Kaijie、Zhao Qinlin、Chen Hao、Wang Jindong、Xie Xing 等研究者共同开发，核心论文为 "PromptBench: A Unified Library for Evaluation of Large Language Models"（arXiv:2312.07910）。

PromptBench 的独特定位在于，它不仅提供标准的模型评测能力，还特别关注 **prompt 的鲁棒性评估**——即评测 LLM 在面对对抗性 prompt 攻击时的表现。这填补了主流评测框架在安全性和鲁棒性维度的空白。

## 关键特性

### 1. 快速模型性能评估
- 提供友好的接口，快速构建模型、加载数据集、评估性能
- 支持 `import promptbench as pb` 一行导入
- 提供丰富的 Jupyter Notebook 教程

### 2. Prompt Engineering 方法集成
- **Few-shot Chain-of-Thought (CoT)**：基于少样本的思维链推理
- **Zero-shot CoT**：零样本思维链
- **EmotionPrompt**：利用心理学情感刺激增强 LLM
- **Expert Prompting**：指导 LLM 扮演领域专家
- **Generated Knowledge**：先生成知识再推理
- **Least-to-Most**：从简到难的渐进推理

### 3. 对抗性 Prompt 攻击评测
这是 PromptBench 最独特的能力，支持四个层级的黑盒 prompt 攻击：

| 攻击层级 | 方法 | 说明 |
|---------|------|------|
| 字符级 | DeepWordBug, TextBugger | 对字符进行微小扰动 |
| 词级 | TextFooler, BertAttack | 替换关键词 |
| 句子级 | CheckList, StressTest | 在 prompt 中插入干扰句子 |
| 语义级 | Human-crafted attack | 人工构造的语义等价但形式不同的 prompt |

### 4. 动态评测（DyVal）
- 集成 DyVal 和 DyVal 2 框架
- 在评测时动态生成样本，控制复杂度
- 有效缓解 **测试数据污染**（test data contamination）问题

### 5. 高效多 Prompt 评测（PromptEval）
- 基于 IRT（Item Response Theory）模型
- 仅需采样 5% 的数据即可将估计误差控制在 2% 左右
- 在 MMLU、BBH、LMentry 上验证有效

### 6. 评测分析工具
- 标准评测 / 动态评测 / 语义评测
- 可视化分析
- 可迁移性分析（Transferability analysis）
- 词频分析（Word frequency analysis）

## 支持的模型/基准

### 语言数据集
- **GLUE 系列**：SST-2、CoLA、QQP、MRPC、MNLI、QNLI、RTE、WNLI
- **通用知识**：MMLU
- **推理能力**：BIG-Bench Hard、Math、GSM8K、CSQA、Numersense、QASC
- **阅读理解**：SQuAD V2
- **翻译**：IWSLT 2017、UN Multi
- **其他**：DROP、ARC、Last Letter Concatenate

### 多模态数据集
- VQAv2、NoCaps、MMMU、MathVista、AI2D、ChartQA、ScienceQA

### 语言模型
- **开源模型**：Flan-T5-Large、Dolly-v1-6B、Llama2 系列、Vicuna-13B、Cerebras-GPT-13B、GPT-NeoX-20B、Flan-UL2、Phi-1.5/2
- **商业模型**：PaLM 2、GPT-3.5、GPT-4、GPT-4o、Gemini Pro、Mistral、Mixtral、Baichuan、Yi

### 多模态模型
- **开源模型**：BLIP2、LLaVA、Qwen-VL/Qwen-VL-Chat、InternLM-XComposer2-VL
- **商业模型**：GPT-4V、Gemini Pro Vision、Qwen-VL-Max/Plus

## 技术架构

PromptBench 采用模块化设计，主要组件包括：

- **Models 模块**：统一的模型加载和推理接口
- **Datasets 模块**：标准数据集加载和预处理
- **Prompts 模块**：prompt 模板管理和 prompt engineering 方法实现
- **Prompt Attack 模块**：基于 TextAttack 库的对抗攻击实现
- **DyVal 模块**：动态评测样本生成（基于图结构）
- **PromptEval 模块**：基于 IRT 的高效多 prompt 评测
- **Metrics 模块**：评测指标计算
- **Utils 模块**：可视化、分析等工具函数

## 与评测相关的启示

1. **Prompt 鲁棒性是被忽视的评测维度**：大多数 benchmark 只关注"最佳 prompt"下的模型表现，而 PromptBench 揭示了 LLM 对 prompt 变化的敏感性。这对评测结果的可信度提出了质疑——如果更换 prompt 模板，排行榜排名可能完全不同
2. **数据污染的实际应对**：DyVal 通过动态生成评测样本来缓解数据污染问题，这是一个比仅仅"更换数据集"更根本的解决方案。随着 LLM 训练数据规模增大，这一方法的重要性将持续上升
3. **评测效率优化**：PromptEval 的 IRT-based 方法用 5% 数据达到 2% 误差，大幅降低了全面评测的成本。这对资源受限的研究者特别有价值
4. **多层级攻击体系**：字符 -> 词 -> 句子 -> 语义四个层级的攻击体系，提供了系统性评估 prompt 鲁棒性的方法论
5. **从静态到动态评测**：DyVal 代表了评测从"固定测试集"向"动态生成测试集"演进的趋势，可有效对抗过拟合和数据泄露
6. **Prompt Engineering 方法的标准化评测**：将 CoT、EmotionPrompt 等方法集成到统一框架中，便于公平对比不同 prompt 技术的效果

## 相关页面

- [Chain-of-Thought 论文摘要](../sources/chain-of-thought.md)
- [LLM-as-Judge 评测方法](../concepts/llm-as-judge.md)
- [MT-Bench 与 Chatbot Arena](../sources/mt-bench-chatbot-arena.md)
- [MMLU Benchmark](../sources/mmlu.md)
- [MathVista](../sources/mathvista.md)
- [LLM 评测综述 (Chang et al.)](../sources/llm-eval-survey-chang.md)
- [HELM 评测框架](../sources/helm.md)
