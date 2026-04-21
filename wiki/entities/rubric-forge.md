---
title: "Rubric-Forge"
type: entity
entity_type: tool
created: 2026-04-09
updated: 2026-04-09
tags: [评测工具, rubric, 自动评分, 百度]
sources: [raw/report/Rubric-Forge：基于 Rubric 的 LLM 自动评分系统.md]
---

# Rubric-Forge

## 基本信息

| 项目 | 详情 |
|------|------|
| 类型 | LLM 自动评分系统 |
| 开发方 | 百度（内部） |
| 评分模型 | Claude Sonnet 4.6 |
| 覆盖规模 | 392 queries，1,729 criteria |
| 方法论 | [Rubric-Based 评测](../concepts/rubric-based-evaluation.md) |

## 核心能力与特点

### 系统设计

- 每个 query 拥有独立的评分标准（criteria），包含维度、权重、pass/fail 条件、veto 标签
- 评分模型逐条判定 pass/fail 并给出 evidence
- 规则层加权聚合（threshold=0.6）
- **veto 机制**：关键标准一票否决，直接判 0

### 性能

| 指标 | 白盒集 | 黑盒集 | 合计 |
|------|--------|--------|------|
| 评测条数 | 2,352 | 1,276 | 3,628 |
| 一致率 | 89.4% | 85.2% | 87.9% |
| 相比基线提升 | +18.3pp | +16.8pp | — |

### Criteria 维度分布

instruction_following 占 65.9%，reasoning 占 13.0%，emotion 占 9.6%，style 占 5.8%，其余为 conversation、safety 等。

## 版本演进

| 版本 | 一致率 | 关键变化 |
|------|--------|----------|
| v2 (基线) | 79.4% | 初版种子 rubric |
| v3 | 85.6% | 首轮 bad case 修复（+6.2pp） |
| v5 | 88.9% | 引入 veto 机制 |
| v7 | 90.5% | 大幅 criteria 改写（训练集峰值） |
| v10 | 89.1% | 引入验证集（验证集 87.3%） |
| 最终版 | 89.4% | targeted rubric 修复（验证集 87.6%） |

## 关键洞察

1. **质量重于数量**：平均 4.4 条 criteria/query，远低于学术 benchmark 的 15-25 条，但一致率达 ~90%
2. **自适应人工偏好**：通过对比 GT=1/GT=0 差异自动发现判分维度
3. **反向标注质量监控**：迭代过程暴露 GT 标注不一致问题
4. **"用弱模型倒逼强 rubric"**：计划更换较弱的评分模型来提升 rubric 粒度和质量

## 相关页面

- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [Rubric-Forge 报告](../sources/rubric-forge.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
