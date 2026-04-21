---
title: "Meta"
type: entity
entity_type: organization
created: 2026-04-09
updated: 2026-04-09
tags: [机构, LLM, Llama, 评测]
sources: [raw/report/Rubric-Based推理数据调研报告.md, raw/benchmarks/AdvancedIF/README.md]
---

# Meta

## 基本信息

科技公司，Llama 系列开源大模型的开发者。

## 评测贡献

- Llama 团队开发了 [AdvancedIF](../benchmarks/advancedif.md)：通用指令遵循领域的 Rubric-Based 评测基准，1,600+ prompts，CC-BY-NC 许可
- 提出了 RIFL 规模化方案：用少量专家样本训练 Rubric Generator，实现批量生成
- 发现了 Rubric 用于 RL 训练时的 Reward Hacking 问题并提出对策
- 开源了完整评测工具链：CLI + 两种专用 Judge（IFRubricsJudge / SystemSteerIFRubricsJudge）+ 批量处理器

## 评测表现

| 模型 | MMLU | GPQA | MATH | HumanEval | MGSM | DROP |
|------|------|------|------|-----------|------|------|
| Llama 3.1 405b | 88.6 | 50.7 | 73.8 | 89.0 | 91.6 | 84.8 |
| Llama 3.1 70b | 82.0 | 41.7 | 68.0 | 80.5 | 86.9 | 79.6 |
| Llama 3.1 8b | 68.4 | 30.4 | 51.9 | 72.6 | 68.9 | 59.5 |

（来源：[simple-evals](../sources/simple-evals-repo.md)，由 OpenAI 报告，prompt 设定未知）

## 相关页面

- [AdvancedIF](../benchmarks/advancedif.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
- [AdvancedIF 仓库文档](../sources/advancedif-repo.md)
- [simple-evals 仓库](../sources/simple-evals-repo.md)
