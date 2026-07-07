---
title: "HealthBench: 面向人类健康改善的 LLM 评测基准"
type: source
created: 2026-07-08
updated: 2026-07-08
tags: [benchmark, 医疗, rubric, OpenAI, LLM-as-Judge, meta-evaluation, 一手论文]
sources: [2505.08775-healthbench.pdf]
---

# HealthBench

## 概述

HealthBench 是 [OpenAI](../entities/openai.md) 于 2025 年 5 月发布的开源医疗评测基准，通过**真实、开放式、多轮**的医患对话，配合**对话特定的医师编写 rubric** 来衡量 LLM 在医疗场景下的能力与安全性。与以往的多选题或短答案医疗评测不同，HealthBench 用 5,000 组对话 + 48,562 条独特 rubric criteria 支持有意义、可信赖、且远未饱和的评测。

**论文信息**: arXiv:2505.08775v1, 2025 年 5 月 13 日。作者 Rahul K. Arora、Jason Wei、Karan Singhal（通讯）等，OpenAI。代码/数据随 [simple-evals](simple-evals-repo.md) 发布。

> **说明**：本页是 HealthBench **一手论文**的摘要页。Wiki 中另有 [HealthBench benchmark 专页](../benchmarks/healthbench.md)（汇总各来源信息、含排行榜），本页专注论文本身的方法与数据细节。

## 关键发现

1. **对话特定 rubric，分值 −10 ~ +10**：每条 criterion 有非零分值，负分惩罚不良行为。Judge 对每条独立判定"满足/不满足"，满足给满分否则给零。样本得分 = 满足项分值和（含负分）÷ 最大可能分，可为负；总分 = 样本均值 clip 到 [0,1]。
2. **模型-医生一致性 ≈ 医生-医生一致性**（均在 55–75% 区间），验证了模型评分的可信度——这是 HealthBench 方法论最重要的元评测结论。
3. **基准远未饱和**：GPT-3.5 Turbo 16% → GPT-4o 32% → o3 60%，近几个月前沿模型提升 28%（比 GPT-3.5→GPT-4o 的跨度还大）；HealthBench Hard（最难 1,000 题）最高分仅 32%。
4. **成本-性能帕累托前沿**：GPT-4.1 nano 超过 GPT-4o 且便宜 25 倍，o3/o4-mini/GPT-4.1 构成新的成本-性能前沿。
5. **模型已超越无参考的专家基线**：医师在无模型参考时写出的回答更短更弱；医师能改进 2024-09 的模型回答，但**改不动** 2025-04 的模型回答。

## 方法论

### 数据构建（历时 11 个月）

- **262 位医师**，覆盖 26 个专科、60 个国家执业经验、49 种语言（从 1,021 位申请者中选出 26%，后又筛掉 31 位）。
- 多数对话由定制 LLM 程序管线合成（以医师枚举的情境为种子），另有医师红队 + 将 Google **HealthSearchQA** 消费者健康搜索改写为对话。
- 对话经 o1-preview 过滤真实性、自洽性、身体健康相关性和完整性。

### Rubric 与 Consensus 机制

- 共 48,562 条独特 criteria，均值 11.5 条/样本（范围 2–48）。按轴分布：完整性 39%、准确性 33%、上下文感知 16%、沟通质量 8%、指令遵循 4%。
- **7 大主题**：全球健康 21.9%、不确定性下应答 21.4%、专业度定制沟通 18.4%、上下文寻求 11.9%、急诊转诊 9.6%、健康数据任务 9.5%、回答深度 7.2%。
- **Consensus 机制**：34 条通用共识准则，仅在 >50% 审核者（≥2 人一致）认可相关时才纳入，共出现 8,053 次。HealthBench Consensus 子集 = 3,671 个含 ≥1 条正向共识准则的样本。

### 元评测（Meta-Evaluation）

- 60,896+ 条元评测样本，平均每条 criterion 1,791 条。Grader Macro-F1：**GPT-4.1 0.709** > o4-mini 0.692 > o3 0.681 > GPT-4.1 mini 0.661 > GPT-4.1 nano 0.580。默认 grader 选 **GPT-4.1**，在 5/7 主题上超过平均医师。
- 长度偏差小：GPT-4o r=−0.053，o3 r=+0.123；16 次运行标准差 ≈0.002，稳定性高。

## 重要数据

| 模型 | HealthBench | HealthBench Hard |
|------|-------------|------------------|
| o3 | **0.60** | 0.32 |
| GPT-4.1 | 0.48 | 0.16 |
| o1 | 0.42 | 0.08 |
| GPT-4o (2024-08) | 0.32 | 0.00 |
| GPT-3.5 Turbo | 0.16 | 0.00 |

非 OpenAI 模型（近似）：Grok 3 ≈0.54，Gemini 2.5 Pro (2025-03) ≈0.52，Claude 3.7 Sonnet (extended thinking) ≈0.35，Llama 4 Maverick ≈0.25。

## 对我们研究的启示

- **医疗 Rubric 评测的标杆**：HealRub 项目直接面向医疗 Rubric 评测，HealthBench 的对话特定 rubric + 医师共识 + 元评测三件套是最重要的参照系。
- **元评测是可信度的关键**：仅有"最终分与人类一致"不够，HealthBench 用 60K+ 元样本证明 grader 在 criterion 级达到医师间一致性水平——这为我们的 Judge 校准提供了可复制的验证范式（对比 [RubricEval](2603.25133-rubriceval.md) 的 rubric 级元评测）。
- **负分/惩罚项设计**：HealthBench 采用 −10~+10 全区间分值捕捉幻觉/有害信息，与 [PRBench](../benchmarks/prbench.md)、[BigLaw Bench](../benchmarks/biglaw-bench.md) 的正负分机制一致；但注意 [Rubrics as Rewards](rubrics-as-rewards.md) 和 [RubricHub](rubrichub.md) 均发现负向 criteria 在 **RL 训练**中作用有限甚至有害——评测用负分与训练用负分需区分对待。
- **HealthBench 已成事实标准**：后续 [RaR](rubrics-as-rewards.md)、[OpenRubrics](openrubrics.md)、[RubricHub](rubrichub.md) 都把 HealthBench 作为主评测台，RubricHub 的 Qwen3-14B 甚至以 69.3 超过 GPT-5 的 67.2。

## 问题与思考

- HealthBench 只评测"对单条对话的单次回答"，不涉及多轮 agentic 工作流，也不度量真实健康结果（作者列为关键未来工作）。
- 样本特定 criteria 由单个医师编写、未经他人验证，个体标注差异可能引入噪声。
- 含 canary 字符串与私有留出集防数据泄露，作者要求勿将样本发到网上。

## 相关页面

- [HealthBench benchmark 专页](../benchmarks/healthbench.md)
- [OpenAI](../entities/openai.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [Rubrics as Rewards (RaR)](rubrics-as-rewards.md)
- [OpenRubrics](openrubrics.md)
- [RubricHub](rubrichub.md)
- [RubricEval](2603.25133-rubriceval.md)
- [simple-evals 仓库](simple-evals-repo.md)
- [PRBench](../benchmarks/prbench.md)
