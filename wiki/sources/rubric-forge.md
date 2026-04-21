---
title: "Rubric-Forge：基于 Rubric 的 LLM 自动评分系统"
type: source
created: 2026-04-09
updated: 2026-04-09
tags: [rubric, 自动评分, LLM-as-Judge, 评测系统, 内部实践]
sources: [raw/report/Rubric-Forge：基于 Rubric 的 LLM 自动评分系统.md]
---

# Rubric-Forge：基于 Rubric 的 LLM 自动评分系统

## 概述

本文档记录了百度内部 Rubric-Forge 评分系统的设计、迭代过程和最终结果。该系统为每个 query 构建独立的评分标准（criteria），使用 Claude Sonnet 4.6 作为评分模型逐条判定 pass/fail，再由规则层加权聚合得出最终分数。系统相比基线版本（M3 工作流）实现了 +18.3pp 的一致率提升。

## 关键发现

### 1. 系统架构

- 每个 query 拥有独立的评分标准（criteria），包含维度、权重、pass/fail 条件、veto 标签
- 评分模型（Claude Sonnet 4.6）逐条判定并给出 evidence
- 规则层加权聚合，threshold=0.6
- **veto 机制**：关键标准（如核心任务未完成）一票否决，直接判 0

### 2. 相比基线的显著提升

白盒集（参与调优的 6 个 batch）一致率从基线 ~71.1% 提升至 ~89.4%（+18.3pp）。黑盒集（未参与调优的 4 个 batch）一致率达 85.2%，验证了系统的泛化能力。

| 集合 | 评测条数 | 一致率 | 过松 | 过严 |
|------|----------|--------|------|------|
| 白盒集 | 2,352 | 89.4% | 136 | 113 |
| 黑盒集 | 1,276 | 85.2% | 119 | 70 |
| **合计** | **3,628** | **87.9%** | **255** | **183** |

### 3. 自进化迭代过程

系统经历了 10+ 轮迭代，从 v2（79.4%）到最终版（89.4%）：

- **v2→v3**（+6.2pp）：首轮 bad case 修复，效果最显著
- **v5**（88.9%）：引入 veto 机制，过松过严趋于平衡
- **v7**（90.5%）：大幅 criteria 改写，达到训练集上的峰值
- **v10**（89.1%）：引入验证集后略有回落，但泛化更好（验证集 87.3%）

### 4. Rubric 规格

| 项目 | 数值 |
|------|------|
| 覆盖 Query 数 | 392 |
| 总 Criteria 数 | 1,729 |
| 平均 Criteria / Query | 4.4 |
| Veto Criteria 数 | 33 |
| 评分模型 | Claude Sonnet 4.6 |
| 聚合策略 | 加权求和(threshold=0.6) + veto 一票否决 |

### 5. Criteria 维度分布

| 维度 | 数量 | 占比 |
|------|------|------|
| instruction_following | 1,140 | 65.9% |
| reasoning | 224 | 13.0% |
| emotion | 166 | 9.6% |
| style | 100 | 5.8% |
| conversation | 50 | 2.9% |
| safety | 22 | 1.3% |
| 其他 | 27 | 1.6% |

### 6. 关键洞察

1. **Rubric 不需要长，关键在准**：即使每 query 仅 2~3 条 criteria，配合校准提示词经迭代后也能达到 ~90% 一致率。评分效果主要取决于 criteria 是否精准命中人工关注的区分点，而非数量堆砌。
2. **系统自适应人工偏好**：通过反复对比 GT=1 和 GT=0 回复差异，自动发现人工真正关注的判分维度。
3. **反向暴露标注质量问题**：迭代中发现部分 GT 标注不一致、不同 batch 间宽严尺度不统一。
4. **维度发现能力**：每个 query 的 criteria 维度分布反映了该 query 考察重点，为测试集结构化分析提供数据基础。

## 问题与思考

1. 白盒集与黑盒集存在 ~4pp 的一致率差距（89.4% vs 85.2%），如何进一步缩小？
2. 报告提到"更换烂一点的评分模型，以此提升 rubrics 的粒度和质量"——这是一种反直觉但有价值的思路：用弱模型倒逼 criteria 更加明确和细致。
3. 平均 4.4 条 criteria/query 远低于 PRBench 的 16-17 条中位数，是否说明在内部场景中更少但更精准的 criteria 反而更有效？

## 相关页面

- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [Rubric-Forge](../entities/rubric-forge.md)
- [调研报告](rubric-based-reasoning-data-survey.md)
