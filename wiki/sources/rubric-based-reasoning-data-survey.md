---
title: "Rubric-Based 推理数据调研报告"
type: source
created: 2026-04-09
updated: 2026-04-09
tags: [rubric, benchmark, 评测方法论, LLM-as-Judge, 数据集构建]
sources: [raw/report/Rubric-Based推理数据调研报告.md]
---

# Rubric-Based 推理数据调研报告

## 概述

本报告是一份针对 Rubric-Based 评测数据集构建方法论的综合调研，来源于百度内部如流知识库。报告系统梳理了业界采用 Rubric 方法论的 5 个代表性 benchmark，提炼了完整的 6 阶段构建方法论（P0-P5），总结了 Rubric 编写的五大原则，并提供了详细的 Prompt 模板和验收标准。

## 关键发现

### 1. 五大 Rubric-Based Benchmark 全景

报告调研了以下 5 个采用相同方法论的 benchmark：

| 项目 | 机构 | 领域 | 规模 | Rubric 特征 |
|------|------|------|------|-------------|
| [PRBench](../benchmarks/prbench.md) | [Scale AI](../entities/scale-ai.md) | 法律+金融 | 1,100 任务, 19,356 criteria | 加权、分类标签、独立验证 |
| [HealthBench](../benchmarks/healthbench.md) | [OpenAI](../entities/openai.md) | 医疗 | 5,000 对话, 48,562 criteria | 正/负分标准、多主题多轴 |
| [BigLaw Bench](../benchmarks/biglaw-bench.md) | [Harvey AI](../entities/harvey-ai.md) | 法律 | 未公开规模 | 定制 rubric、按 practice area 分类 |
| [AdvancedIF](../benchmarks/advancedif.md) | [Meta](../entities/meta.md) | 通用指令遵循 | 1,600+ prompts | 专家编写、多轮验证 |
| [CNFinBench](../benchmarks/cnfinbench.md) | 学术团队 | 中国金融合规 | 13,000+ 实例 | 安全-有效性双轨、15 子任务 |

### 2. 六阶段构建方法论

完整 pipeline 分为 P0-P5：

- **P0 领域框架设计**：由领域专家与评测工程师共同设计维度体系，每个维度需配套定义文档和正反例
- **P1 专家招募与培训**：资质验证 + 试标，建立"技术团队-顾问团-标注员"三层沟通机制
- **P2 任务创建**：基于真实工作场景，交叉审核确保质量，约 30% 为多轮对话
- **P3 Rubric 编写**：遵循[五大原则](../concepts/rubric-based-evaluation.md)，自动校验 + 专家复审
- **P4 独立验证**：IRA（Inter-Rater Agreement）≥ 90%，PRBench 达到 93.9%
- **P5 LLM Judge 校准**：Judge-Expert IRA ≥ 80%，三种校准方法（直接 Prompting、校准网络、微调 Verifier）

### 3. Rubric 编写五大原则

详见 [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)。

- 构建性（Constructive）、互斥穷尽（MECE）、原子性（Atomic）、客观性（Objective）、自包含性（Self-Contained）

### 4. 权重分配体系

PRBench 采用 6 级分层权重：Critically Important (+8~+10) → Important (+4~+7) → Slightly Important (+1~+3) → Slightly Detrimental (-1~-3) → Detrimental (-4~-7) → Critically Detrimental (-8~-10)。正向 criterion 描述好回答应具备的质量，负向 criterion 捕捉模型的"过度自信"和"幻觉"。

### 5. LLM Judge 校准方法

报告总结了三种 LLM Judge 校准方法：

- **方法 A - 直接 Prompting**（PRBench 用 o4-mini，HealthBench 用 GPT-4.1）
- **方法 B - 校准网络**（LLM-Rubric，通过前馈神经网络映射 LLM 分布到人类判定）
- **方法 C - 微调 Verifier**（RIFL，两阶段 SFT 微调，F1 从 0.639 提升至 0.790）

### 6. Reward Hacking 防护

来自 AdvancedIF 的发现：当 Rubric 用于 RL 训练时，模型会出现 reward hacking（如在回答末尾生成"all instructions are followed"来欺骗 Verifier）。对策包括微调专用 Verifier 和增加反 hacking criteria。

## 重要数据

### 验收标准参考

| 指标 | 目标值 | 参考基线 |
|------|--------|----------|
| Rubric 清晰度一致率 | ≥ 90% | PRBench: 93.9% |
| LLM Judge - Expert IRA | ≥ 80% Macro F1 | PRBench: 80.2% |
| Expert - Expert IRA | ≥ 75% Macro F1 | PRBench: 81.3% |
| Rubric Generator F1 | ≥ 0.75 | RIFL: 0.79 |
| Criteria/Task 数量 | 15-25 条/任务 | PRBench: 16-17 中位数 |
| 正/负 Criteria 比例 | 7:3 到 8:2 | PRBench 参考 |

## 问题与思考

1. 报告提到评分策略是"目前 benchmark 搭建链路中最费时间费事的一环"——如何降低 LLM Judge 校准的人力成本？
2. AdvancedIF 提出了用少量专家样本训练 Rubric Generator 的规模化方案——这是否可以推广到其他领域？
3. 负向 criterion 的比例应该如何根据领域特性调整？法律/医疗等高风险领域是否需要更高比例的负向 criterion？

## 相关页面

- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [PRBench](../benchmarks/prbench.md)
- [HealthBench](../benchmarks/healthbench.md)
- [BigLaw Bench](../benchmarks/biglaw-bench.md)
- [AdvancedIF](../benchmarks/advancedif.md)
- [CNFinBench](../benchmarks/cnfinbench.md)
- [Rubric-Forge](../entities/rubric-forge.md)
