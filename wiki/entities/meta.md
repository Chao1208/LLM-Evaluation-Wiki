---
title: "Meta"
type: entity
entity_type: organization
created: 2026-04-09
updated: 2026-04-22
tags: [机构, LLM, Llama, 评测, 多模态]
sources: [raw/report/Rubric-Based推理数据调研报告.md, raw/benchmarks/AdvancedIF/README.md, 2307.09288-llama2.pdf]
---

# Meta

## 基本信息

科技公司（GenAI 部门），Llama 系列开源大模型的开发者。在 LLM 开源生态中扮演核心角色，从 Llama 1 到 Llama 3 系列持续推动开源模型能力提升。

## 核心模型

### Llama 2（2023 年 7 月）

- 规模：7B / 13B / 34B / 70B 参数
- 训练：2T tokens，4K 上下文，GQA（34B/70B）
- 微调版 Llama 2-Chat：SFT（27,540 条高质量标注）→ RLHF（Rejection Sampling + PPO）
- **关键洞察**："Quality Is All You Need"——少而精的标注数据优于大量低质数据
- 碳排放：3.3M GPU 小时，539 tCO₂eq
- 详见：[Llama 2 论文摘要](../sources/llama2.md)

## 评测贡献

- [AdvancedIF](../benchmarks/advancedif.md)：通用指令遵循 Rubric-Based 评测基准，1,600+ prompts，CC-BY-NC 许可
- RIFL 规模化方案：用少量专家样本训练 Rubric Generator，实现批量生成
- 发现 Rubric 用于 RL 训练时的 Reward Hacking 问题并提出对策
- 开源评测工具链：CLI + 两种专用 Judge + 批量处理器
- Llama 2 论文提供了详尽的**安全评测方法论**（Red Teaming + 多维安全分类器 + 人工评测 vs GPT-4 Judge 对比）

## 评测表现

### Llama 2 系列（学术基准，来源：[Llama 2 论文](../sources/llama2.md)）

| 模型 | MMLU (5-shot) | GSM8K (8-shot) | HumanEval (0-shot) | BIG-Bench Hard |
|------|-------------|---------------|-------------------|----------------|
| Llama 2 70B | 68.9 | 56.8 | 29.9 | 51.2 |
| Llama 2 34B | 62.6 | 42.2 | 22.6 | 44.1 |
| Llama 2 13B | 54.8 | 28.7 | 18.9 | 39.4 |
| Llama 2 7B | 45.3 | 14.6 | 12.8 | 32.6 |

### Llama 3.1 系列（来源：[simple-evals](../sources/simple-evals-repo.md)）

| 模型 | MMLU | GPQA | MATH | HumanEval | MGSM | DROP |
|------|------|------|------|-----------|------|------|
| Llama 3.1 405b | 88.6 | 50.7 | 73.8 | 89.0 | 91.6 | 84.8 |
| Llama 3.1 70b | 82.0 | 41.7 | 68.0 | 80.5 | 86.9 | 79.6 |
| Llama 3.1 8b | 68.4 | 30.4 | 51.9 | 72.6 | 68.9 | 59.5 |

## 相关页面

- [Llama 2 论文](../sources/llama2.md)
- [LLaVA](../sources/llava.md)
- [有效长上下文扩展](../sources/effective-long-context.md)
- [AdvancedIF](../benchmarks/advancedif.md)
- [AdvancedIF 仓库文档](../sources/advancedif-repo.md)
- [InstructGPT](../sources/instructgpt.md)（RLHF 方法论对比）
- [MT-Bench & Chatbot Arena](../sources/mt-bench-chatbot-arena.md)
- [MMLU](../sources/mmlu.md)
- [simple-evals 仓库](../sources/simple-evals-repo.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
