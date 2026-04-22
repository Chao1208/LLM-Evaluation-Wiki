---
title: "Rubric Python 库：基于加权 Rubric 的 LLM 评测工具"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [Rubric, LLM评测, 自动评分, Python库, LLM-as-Judge, 工具]
sources: [raw/github/rubric/README.md]
---

## 概述

Rubric 是由 The LLM Data Company 开发的开源 Python 库（MIT 许可），专门用于基于加权评分标准（weighted rubrics）的 LLM 评测。它提供了三种自动评分策略，支持正负权重、多种输入格式，并为 RL 训练场景提供了原始分数输出模式。

仓库地址：`raw/github/rubric/`

## 关键特性

### 三种评分策略

| 策略 | 说明 | 调用次数 | 适用场景 |
|------|------|---------|---------|
| **PerCriterionGrader** | 并行逐条评估每个标准 | n 次（每标准一次） | 高精度，多标准场景 |
| **PerCriterionOneShotGrader** | 单次调用评估所有标准 | 1 次 | 成本优化，较少标准 |
| **RubricAsJudgeGrader** | 整体性评估，返回 0-100 分 | 1 次 | 快速评估，无需逐条细节 |

### 评分公式

**逐条评分**：
$$\text{raw\_score} = \sum_{i=1}^{n} \mathbb{1}[\text{verdict}_i = \text{MET}] \cdot w_i$$

**归一化**：
$$\text{score} = \max\left(0, \min\left(1, \frac{\text{raw\_score}}{\sum \max(0, w_i)}\right)\right)$$

### 正负权重机制

- **正权重**（weight > 0）：期望特征，MET 加分
- **负权重**（weight < 0）：错误检测，MET 扣分（如"使用了错误的分母"）
- 全负权重 Rubric 特殊处理：得分 = 1.0 - (错误数/总错误数)

### RL 训练支持

- `normalize=False` 模式返回原始加权和（可为负数），适合作为 RL 奖励信号
- `raw_score` 字段在所有评分器间语义一致（加权和），便于训练管线使用
- 批量处理接口支持 `asyncio.gather` 并行评分

### 灵活的输入/输出

- 支持 JSON、YAML 文件加载 Rubric
- 自定义 `generate_fn` 接口，可对接任意 LLM（OpenAI、Anthropic、本地模型等）
- Pydantic 模型确保类型安全和结构化输出
- 可自定义 system prompt

## 技术架构

```
src/rubric/
├── rubric.py                # 核心 Rubric 类
├── types.py                 # Criterion、Protocol 类型定义
├── utils.py                 # 默认生成函数（Gemini）
└── autograders/
    ├── base.py              # Autograder 抽象基类
    ├── schemas.py           # Pydantic 输出 schema
    ├── per_criterion_grader.py
    ├── per_criterion_one_shot_grader.py
    └── rubric_as_judge_grader.py
```

核心类型：
- `Criterion(weight, requirement)` — 单条评估标准
- `CriterionReport(verdict, reason)` — 单条评估结果
- `EvaluationReport(score, raw_score, llm_raw_score, report)` — 最终评估报告

## 与评测相关的启示

1. **Rubric 定义的工程化实践**：weight + requirement 二元组是最简化的 Rubric 表达，与 [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)的理论框架对应
2. **三种评分策略的权衡**：逐条精准 vs 一次性高效 vs 整体性快速，对应不同场景需求
3. **负权重设计**：优雅处理"错误检测"类 Rubric，与 [Rubric-Forge](../entities/rubric-forge.md) 的正负分 rubric 思路一致
4. **RL 训练集成**：`normalize=False` + `raw_score` 一致性设计，直接对接 [RL with Rubric Anchors](../sources/rl-rubric-anchors.md) 类框架
5. **可扩展性**：自定义 `Autograder` 子类机制为研究者提供了灵活的实验接口

## 相关页面

- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [LLM-as-Judge 方法论](../concepts/llm-as-judge.md)
- [Rubric-Forge](../entities/rubric-forge.md)
- [RL with Rubric Anchors](../sources/rl-rubric-anchors.md)
- [Prometheus](../sources/prometheus.md)
- [Rubric Is All You Need](../sources/rubric-is-all-you-need.md)

（来源：[GitHub - The-LLM-Data-Company/rubric](https://github.com/The-LLM-Data-Company/rubric)）
