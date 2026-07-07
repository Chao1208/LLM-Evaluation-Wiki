---
title: "Scale AI"
type: entity
entity_type: organization
created: 2026-04-09
updated: 2026-07-08
tags: [机构, 数据标注, 评测, rubric-RL]
sources: [raw/report/Rubric-Based推理数据调研报告.md, raw/benchmarks/PRBench/README.md, 2507.17746-rubrics-as-rewards.pdf]
---

# Scale AI

## 基本信息

AI 数据基础设施公司，提供数据标注、模型评测等服务。拥有内部后训练平台 **RLXF**。

## 评测贡献

- 开发了 [PRBench](../benchmarks/prbench.md)：法律+金融领域的 Rubric-Based 评测基准，1,100 任务，19,356 criteria
- PRBench 由 182 位专家基于真实工作场景设计，是 [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)的标杆实践
- 提供轻量级评测工具（evals.py），支持 sampled/prefilled 两种回复模式，底层基于 LiteLLM
- 提供可视化探索工具：https://prbench-explorer.vercel.app/
- 提出 [Rubrics as Rewards (RaR)](../sources/rubrics-as-rewards.md)（arXiv:2507.17746, 2025）：把 rubric 当作 RL 奖励信号，将 [RLVR](../concepts/rlhf.md) 扩展到医学/科学等不可验证领域，[HealthBench](../benchmarks/healthbench.md) 相对提升 31%。开源 RaR-Medicine（20,166）与 RaR-Science（20,625）数据集

## 相关页面

- [PRBench](../benchmarks/prbench.md)
- [Rubrics as Rewards (RaR)](../sources/rubrics-as-rewards.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [RLHF](../concepts/rlhf.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
- [PRBench 仓库文档](../sources/prbench-repo.md)
