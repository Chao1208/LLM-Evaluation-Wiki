---
title: "HumanEval: Hand-Written Evaluation Set"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [代码生成, benchmark, OpenAI, pass-at-k, 功能正确性]
sources: [raw/github/human-eval/README.md]
---

## 概述

HumanEval 是由 [OpenAI](../entities/openai.md) 开发的手写代码生成评测数据集，配套的评测工具发布在 GitHub 仓库 `openai/human-eval` 上。该项目对应论文 "Evaluating Large Language Models Trained on Code"（arXiv:2107.03374），由 Mark Chen、Jerry Tworek 等数十位 OpenAI 研究者共同完成。

HumanEval 是代码生成领域最广泛使用的 benchmark 之一，其核心思想是通过 **功能正确性**（functional correctness）而非文本匹配来评估模型生成代码的质量。

## 关键特性

- **手工编写的编程题**：数据集包含 164 个手写的 Python 编程问题，每个问题包括函数签名、docstring 描述和测试用例
- **基于执行的评测**：不使用 BLEU 等文本相似度指标，而是通过实际运行生成代码并与测试用例对比来判断正确性
- **pass@k 指标**：核心指标为 pass@1、pass@10、pass@100，衡量模型在 k 次生成中至少有一次通过所有测试用例的概率。使用无偏估计方法计算 pass@k
- **安全沙箱设计**：由于需要执行模型生成的不可信代码，工具强调安全性，默认注释掉执行代码以确保用户理解风险
- **JSONL 格式**：采用简洁的 JSON Lines 格式存储样本，每行包含 `task_id` 和 `completion` 字段
- **轻量化工具链**：提供命令行工具 `evaluate_functional_correctness`，支持自定义 k 值参数

## 支持的模型/基准

HumanEval 本身是一个数据集 + 评测工具，不直接绑定特定模型。最初用于评测 OpenAI 的 Codex 模型，后来成为评测所有代码生成模型的标准 benchmark，被广泛应用于：

- OpenAI Codex / GPT-4 系列
- Meta Code Llama 系列
- Google Gemini
- Anthropic Claude 系列
- DeepSeek Coder 系列
- 以及几乎所有声称具备代码能力的 LLM

## 技术架构

项目结构简洁：

- `human_eval/data.py`：数据加载与写入（`read_problems`、`write_jsonl`）
- `human_eval/execution.py`：代码执行与测试运行（包含安全沙箱相关逻辑）
- `human_eval/evaluate_functional_correctness.py`：命令行评测入口
- `data/`：包含评测问题和示例解答

评测流程为：
1. 用户加载问题集，使用自己的模型生成代码补全
2. 将生成结果保存为 JSONL 格式
3. 运行 `evaluate_functional_correctness` 执行测试并计算 pass@k

## 与评测相关的启示

1. **功能正确性 vs 文本匹配**：HumanEval 开创了用执行结果而非文本相似度评测代码生成的范式，这一理念深刻影响了后续的代码评测 benchmark（如 MBPP、LiveCodeBench、SWE-bench 等）
2. **pass@k 的统计方法**：论文提出的 pass@k 无偏估计方法已成为代码生成评测的标准实践
3. **安全性考量**：执行不可信代码的安全风险是代码评测的固有挑战，后续工具（如沙箱环境）均需面对这一问题
4. **局限性**：仅 164 题、仅 Python、仅函数级补全，难以覆盖真实软件开发场景的复杂性。这推动了 HumanEval+、MultiPL-E、SWE-bench 等更全面 benchmark 的发展
5. **内存问题**：README 中提到的 RAM 不足导致正确程序失败的已知问题，提醒评测基础设施的稳定性不可忽视

## 相关页面

- [OpenAI](../entities/openai.md)
- [LLM-as-Judge 评测方法](../concepts/llm-as-judge.md)
- [MMLU Benchmark](../sources/mmlu.md)
- [GPT-4 技术报告](../sources/gpt4-technical-report.md)
- [PolyGoder 代码评测研究](../sources/polycoder-code-eval.md)
