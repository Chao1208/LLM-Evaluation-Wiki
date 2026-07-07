---
title: "HealthBench"
type: benchmark
created: 2026-04-09
updated: 2026-07-08
tags: [benchmark, rubric, 医疗, OpenAI, simple-evals]
sources: [raw/report/Rubric-Based推理数据调研报告.md, raw/benchmarks/HealthBench-simple-evals/README.md, 2505.08775-healthbench.pdf]
---

# HealthBench

> **一手论文摘要见** [wiki/sources/healthbench.md](../sources/healthbench.md)（arXiv:2505.08775, OpenAI, 2025-05）。本页为汇总专页，含排行榜与跨来源信息。

## 基本信息

| 项目 | 详情 |
|------|------|
| 创建机构 | [OpenAI](../entities/openai.md) |
| 领域 | 医疗 |
| 规模 | 5,000 对话，48,562 条独特 criteria（均值 11.5 条/样本，范围 2–48） |
| 方法论 | [Rubric-Based 评测](../concepts/rubric-based-evaluation.md) |
| 发布 | 2025 年 5 月（arXiv:2505.08775） |
| GitHub | https://github.com/openai/simple-evals |

## 评测维度

- 对话特定 rubric，每条 criterion 分值 **−10 ~ +10**（负分惩罚不良行为）
- **5 大评测轴**：完整性 39%、准确性 33%、上下文感知 16%、沟通质量 8%、指令遵循 4%
- **7 大主题**：全球健康 21.9%、不确定性下应答 21.4%、专业度定制沟通 18.4%、上下文寻求 11.9%、急诊转诊 9.6%、健康数据任务 9.5%、回答深度 7.2%
- 采用 GPT-4.1 作为 [LLM Judge](../concepts/llm-as-judge.md)（Macro-F1 0.709，5/7 主题超平均医师）

## 各模型表现

（来源：[HealthBench 论文](../sources/healthbench.md)）

| 模型 | HealthBench | HealthBench Hard |
|------|-------------|------------------|
| o3 | **0.60** | 0.32 |
| Grok 3 | ~0.54 | — |
| Gemini 2.5 Pro (2025-03) | ~0.52 | — |
| GPT-4.1 | 0.48 | 0.16 |
| o1 | 0.42 | 0.08 |
| Claude 3.7 Sonnet (extended) | ~0.35 | — |
| GPT-4o (2024-08) | 0.32 | 0.00 |
| Llama 4 Maverick | ~0.25 | — |
| GPT-3.5 Turbo | 0.16 | 0.00 |

历史进展：GPT-3.5 Turbo 16% → GPT-4o 32% → o3 60%；GPT-4.1 nano 超过 GPT-4o 且便宜 25 倍。HealthBench Hard（最难 1,000 题）最高分仅 32%，基准远未饱和。

**作为后训练评测台**：后续 rubric 研究普遍以 HealthBench 为主评测基准——[Rubrics as Rewards](../sources/rubrics-as-rewards.md) 相对提升 31%，[OpenRubrics](../sources/openrubrics.md) 的 Rubric-RM 达 68.3，[RubricHub](../sources/rubrichub.md) 让 Qwen3-14B 达 **69.3，超过 GPT-5（67.2）**。

## 方法论

### 数据策略

1. **医师编写高风险场景**：反映关键临床决策点
2. **对抗性红队测试**：针对已知盲点（如抗生素滥用场景）设计 Prompt
3. **搜索查询转化**：将消费者健康搜索（HealthSearchQA）转化为结构化多轮对话

### 培训体系

- 由 Physician Lead 带领核心顾问团
- 包含 Rubric 编写规范、正反例库、常见错误案例
- 一对一指导 + 试标（5-10 条样本），持续质量反馈

### Consensus 机制

- 34 条 Consensus Criteria：医师团队编写的通用高标准准则（如"急诊场景中是否在前几句话中建议立即就医"），共出现 8,053 次
- 一条 consensus criterion 仅在 >50% 审核医师（≥2 人一致）均同意其相关时才纳入
- HealthBench Consensus 子集 = 3,671 个含 ≥1 条正向共识准则的样本

### 元评测（Meta-Evaluation）与可信度

- 60,896+ 条元评测样本（平均每 criterion 1,791 条）验证评分系统可信度
- **核心结论：模型-医生一致性 ≈ 医生-医生一致性**（均在 55–75% 区间）——这是 HealthBench 方法论最重要的可信度证据
- Grader Macro-F1：GPT-4.1 0.709 > o4-mini 0.692 > o3 0.681 > GPT-4.1 mini 0.661 > nano 0.580
- 长度偏差小（GPT-4o r=−0.053），16 次运行标准差 ≈0.002

## 局限性与争议

- 高度依赖医师专家（262 位，26 专科，历时 11 个月），成本高
- 仅评测"对单条对话的单次回答"，不涉及多轮 agentic 工作流，也不度量真实健康结果（作者列为关键未来工作）
- 样本特定 criteria 由单个医师编写、未经他人验证，个体标注差异可能引入噪声；consensus 一致性也仅 55–75%
- 医疗领域的评测标准可能因地区、指南版本不同而存在差异
- 含 canary 字符串与私有留出集防数据泄露

## 所属评测框架

HealthBench 是 [OpenAI](../entities/openai.md) simple-evals 评测库的一部分。simple-evals 还包含 MMLU、MATH-500、GPQA、DROP、MGSM、HumanEval、SimpleQA、BrowseComp 等评测基准。该库强调**零样本 + 思维链**评测设定，不使用 few-shot 或角色扮演 prompt。

**注意**：simple-evals 已于 2025年7月宣布弃用（不再更新新模型结果），但继续维护 HealthBench、BrowseComp、SimpleQA 的参考实现。

（来源：[simple-evals 仓库](../sources/simple-evals-repo.md)）

## 相关页面

- [HealthBench 论文摘要](../sources/healthbench.md)
- [OpenAI](../entities/openai.md)
- [PRBench](prbench.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [Rubrics as Rewards (RaR)](../sources/rubrics-as-rewards.md)
- [OpenRubrics](../sources/openrubrics.md)
- [RubricHub](../sources/rubrichub.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
- [simple-evals 仓库](../sources/simple-evals-repo.md)
