---
title: "代码生成评测"
type: concept
created: 2026-04-22
updated: 2026-04-22
tags: [代码生成, HumanEval, pass@k, 评测, 编程]
sources: [human-eval-repo.md, classeval.md, polycoder-code-eval.md, simple-evals-repo.md]
---

## 概述

代码生成评测（Code Generation Evaluation）是大模型评测中的关键分支，旨在衡量 LLM 自动编写可执行、功能正确代码的能力。随着 Codex、GPT-4、Claude 等模型在编程任务上展现出越来越强的能力，代码评测已从早期的文本匹配范式演进为以 **执行正确性** 为核心的评测体系。

当前代码评测的核心挑战包括：评测粒度从函数级向类级和项目级扩展、跨语言能力的系统性评估、以及评测指标对真实开发场景的覆盖度。（来源：[HumanEval](../sources/human-eval-repo.md)、[ClassEval](../sources/classeval.md)、[PolyCoder](../sources/polycoder-code-eval.md)）

## 核心评测方法

### 基于执行的评测（Execution-based）

主流方法是将模型生成的代码在沙箱环境中实际执行，通过预定义的测试用例判断功能正确性。核心指标为 **pass@k**。

**pass@k** 衡量模型在生成 k 个候选方案时，至少有一个通过全部测试用例的概率。为避免采样偏差，HumanEval 论文（arXiv:2107.03374）提出了无偏估计公式：

$$\text{pass@k} = \mathbb{E}\left[1 - \frac{\binom{n-c}{k}}{\binom{n}{k}}\right]$$

其中 $n$ 为总生成数，$c$ 为通过测试的数量，$k$ 为选取数。常用 k=1（单次准确率）、k=10、k=100。该无偏估计方法已成为代码生成评测的标准实践。（来源：[HumanEval](../sources/human-eval-repo.md)）

### 基于匹配的评测（Match-based）

早期方法使用 BLEU、CodeBLEU 等文本相似度指标，将生成代码与参考代码进行字符串/token 级匹配。这类方法计算成本低但无法判断功能正确性——语法不同但逻辑等价的代码会被错误地低估。HumanEval 的提出正是为了克服这一局限，将评测范式从文本匹配转向执行验证。

## 基准对比

| 基准名称 | 数据规模 | 语言 | 任务粒度 | 评测方式 | 核心特色 |
|----------|---------|------|---------|---------|---------|
| [HumanEval](../sources/human-eval-repo.md) | 164 题 | Python | 函数级 | pass@k（执行） | 开创性 benchmark，最广泛使用 |
| MBPP | 974 题 | Python | 函数级 | pass@k（执行） | 规模更大的基础编程题集 |
| [ClassEval](../sources/classeval.md) | 100 个类 | Python | 类级 | class-level / method-level pass@1 | 首个类级别 benchmark，测试方法间依赖 |
| [PolyCoder 评测](../sources/polycoder-code-eval.md) | HumanEval + MBPP + 跨语言 | 12 种语言 | 函数级 | pass@k + perplexity | 系统性跨语言评测框架 |

（来源：[HumanEval](../sources/human-eval-repo.md)、[ClassEval](../sources/classeval.md)、[PolyCoder](../sources/polycoder-code-eval.md)）

## 评测粒度演进

代码生成评测的粒度经历了从简单到复杂的演进过程：

### 函数级（Function-level）

以 HumanEval（164 题）和 MBPP（974 题）为代表。给定函数签名和 docstring，模型补全函数体。优势是评测简洁、标准化程度高；局限是仅测试独立函数，无法反映真实软件开发的复杂性。（来源：[HumanEval](../sources/human-eval-repo.md)）

### 类级（Class-level）

ClassEval 将评测粒度提升到类级别，要求模型生成包含构造函数、多个方法及方法间依赖关系的完整 Python 类。研究发现 LLM 在类级别任务上表现显著退化（相比函数级差距 20-40 个百分点），方法间依赖是主要瓶颈。增量生成（Incremental）策略总体最优。（来源：[ClassEval](../sources/classeval.md)）

### 项目级（Project-level）

SWE-bench 等新一代 benchmark 将评测推向真实项目级别，要求模型在完整代码仓库中定位问题并生成修复补丁。这一方向仍在快速发展中。

### 多语言评测

PolyCoder 研究系统性地评测了 12 种编程语言上的代码生成能力，发现模型在不同语言上表现差异很大——Codex 在 Python 上领先，而 PolyCoder（2.7B）在 C 语言上可超越 Codex。训练数据中的语言比例是关键影响因素。（来源：[PolyCoder](../sources/polycoder-code-eval.md)）

## 各模型代码能力对比

以下为 [simple-evals](../sources/simple-evals-repo.md) 仓库报告的各模型 HumanEval pass@1 得分（零样本 + CoT 设定）：

| 模型 | HumanEval |
|------|-----------|
| o4-mini-high | **99.3** |
| o3-mini-high | 97.6 |
| o4-mini | 97.3 |
| o3-mini | 96.3 |
| gpt-4.1 | 94.5 |
| gpt-4.1-mini | 93.8 |
| o1-preview | 92.4 |
| o1-mini | 92.4 |
| Claude 3.5 Sonnet | 92.0 |
| gpt-4o (2024-11) | 90.2 |
| Llama 3.1 405b | 89.0 |
| o3-high | 88.4 |
| gpt-4.5-preview | 88.6 |
| Grok 2 | 88.4 |
| gpt-4-turbo | 88.2 |
| gpt-4o-mini | 87.2 |
| gpt-4.1-nano | 87.0 |
| Claude 3 Opus | 84.9 |
| Llama 3.1 70b | 80.5 |
| Gemini 1.0 Ultra | 74.4 |
| Llama 3.1 8b | 72.6 |
| Gemini 1.5 Pro | 71.9 |

值得注意：o4-mini-high 达到 99.3%，接近 HumanEval 的天花板，说明该 benchmark 对最新推理模型已趋于饱和。（来源：[simple-evals](../sources/simple-evals-repo.md)）

## 相关页面

- [HumanEval](../sources/human-eval-repo.md) — 函数级代码生成 benchmark
- [ClassEval](../sources/classeval.md) — 类级代码生成 benchmark
- [PolyCoder 代码评测研究](../sources/polycoder-code-eval.md) — 多语言代码生成系统性评测
- [simple-evals](../sources/simple-evals-repo.md) — OpenAI 模型跑分数据
- [OpenAI](../entities/openai.md)
- [评测框架与工具](evaluation-frameworks.md)
