---
title: "HealthBench"
type: benchmark
created: 2026-04-09
updated: 2026-04-09
tags: [benchmark, rubric, 医疗, OpenAI, simple-evals]
sources: [raw/report/Rubric-Based推理数据调研报告.md, raw/benchmarks/HealthBench-simple-evals/README.md]
---

# HealthBench

## 基本信息

| 项目 | 详情 |
|------|------|
| 创建机构 | [OpenAI](../entities/openai.md) |
| 领域 | 医疗 |
| 规模 | 5,000 对话，48,562 criteria |
| 方法论 | [Rubric-Based 评测](../concepts/rubric-based-evaluation.md) |
| GitHub | https://github.com/openai/simple-evals |

## 评测维度

- 正/负分标准、多主题多轴评测
- Criteria 数量：最小 ~3，中位数 ~10，最大 ~30+
- 采用 GPT-4.1 作为 [LLM Judge](../concepts/llm-as-judge.md)

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

- 34 条 Consensus Criteria：医师团队编写的通用高标准准则（如"急诊场景中是否在前几句话中建议立即就医"）
- 一条 consensus criterion 仅在 2+ 位审核医师均同意其相关时才纳入
- 元评估（Meta-Evaluation）验证评分系统可信度

## 局限性与争议

- 高度依赖医师专家，成本高
- 医疗领域的评测标准可能因地区、指南版本不同而存在差异

## 所属评测框架

HealthBench 是 [OpenAI](../entities/openai.md) simple-evals 评测库的一部分。simple-evals 还包含 MMLU、MATH-500、GPQA、DROP、MGSM、HumanEval、SimpleQA、BrowseComp 等评测基准。该库强调**零样本 + 思维链**评测设定，不使用 few-shot 或角色扮演 prompt。

**注意**：simple-evals 已于 2025年7月宣布弃用（不再更新新模型结果），但继续维护 HealthBench、BrowseComp、SimpleQA 的参考实现。

（来源：[simple-evals 仓库](../sources/simple-evals-repo.md)）

## 相关页面

- [OpenAI](../entities/openai.md)
- [PRBench](prbench.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
- [simple-evals 仓库](../sources/simple-evals-repo.md)
