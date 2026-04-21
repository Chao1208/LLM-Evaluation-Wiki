---
title: "PRBench"
type: benchmark
created: 2026-04-09
updated: 2026-04-09
tags: [benchmark, rubric, 法律, 金融, Scale AI]
sources: [raw/report/Rubric-Based推理数据调研报告.md, raw/benchmarks/PRBench/README.md]
---

# PRBench

## 基本信息

| 项目 | 详情 |
|------|------|
| 创建机构 | [Scale AI](../entities/scale-ai.md) |
| 领域 | 法律 + 金融 |
| 规模 | 1,100 任务，19,356 criteria |
| 方法论 | [Rubric-Based 评测](../concepts/rubric-based-evaluation.md) |
| GitHub | https://github.com/scaleapi/PRBench |

## 评测维度

- 任务由 182 位持有 JD/CFA 或 6 年以上从业经验的专家基于**真实客户工作场景**设计
- 覆盖 114 个国家、47 个美国司法管辖区
- 约 30% 为多轮对话（最多 10 轮）
- 关键区别于学术 benchmark：不是考试题，而是日常专业工作中真实遇到的问题

元数据字段：Decision Type（决策类型）、Economic Pathway（经济后果路径）、Jurisdiction（法域）、User Expertise Level（提问者专业水平）

## 方法论

### Rubric 特征

- 每个任务配有 10–30 条 criteria（中位数 16-17），总计 19,356 条
- 每条包含：权重、能力类别标签、可判定条件
- 采用加权、分类标签、独立验证
- 遵循[五大编写原则](../concepts/rubric-based-evaluation.md)：构建性、MECE、原子性、客观性、自包含性

### 评分流程

- 使用 o4-mini 作为 [LLM Judge](../concepts/llm-as-judge.md) 逐条检查
- 每次评测重复 3 次取平均，报告 95% 置信区间
- 加权得分裁剪到 [0, 1]
- 引入 Min-Normalized Scoring 处理全为负向 criterion 的情况

### 任务创建

- 任务必须源自真实专业工作场景
- 要求大量分析、解释或创造性思考，禁止考试型问题
- 多轮对话由专家与开源模型（GPT OSS 20B、Mistral Medium、DeepSeek R1）交互构建
- 每条任务由独立领域专家交叉审核

## 质量验证

| 指标 | 数值 |
|------|------|
| 独立专家 Rubric 清晰度一致率 | 93.9% |
| LLM Judge - Expert IRA | 80.2% |
| Expert - Expert IRA | 81.3% |
| Hard Subset 头部模型得分 | 0.37-0.39 |

## 局限性与争议

- 任务集中于法律和金融两个领域，泛化到其他专业领域需要重新构建
- 依赖高资质专家，数据集扩展成本高
- LLM Judge 与人类一致率（80.2%）虽接近人类间一致率（81.3%），但在高风险领域仍有提升空间

## 评测工具链

- **主入口**：`python evals.py config.yaml`
- **回复来源**：`sampled`（实时从模型采样）或 `prefilled`（预填充 JSON，格式 task→response）
- **底层 SDK**：OpenAI SDK / LiteLLM
- **调试模式**：设置 `debug: true` 仅对前 2 个样本运行
- **核心指标**：`mean_clipped` 分数
- **可视化工具**：https://prbench-explorer.vercel.app/

（来源：[仓库文档](../sources/prbench-repo.md)）

## 相关页面

- [Scale AI](../entities/scale-ai.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
- [仓库文档](../sources/prbench-repo.md)
