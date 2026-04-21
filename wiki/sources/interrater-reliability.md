---
title: "Interrater Reliability: The Kappa Statistic"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [statistics, interrater-reliability, kappa, evaluation-methodology]
sources: ["McHugh ML.-Interrater reliability.pdf"]
---

## 概述

本文由 McHugh 于 2012 年发表于 Biochemia Medica，是一篇关于评分者间信度（interrater reliability）统计方法的教程性文章。文章系统介绍了评估评分一致性的主要统计指标，重点讲解了 Cohen's kappa 统计量的原理、计算方法和解读标准。作为评估信度的关键参考文献，本文对任何涉及多评分者判断的评估系统（包括 LLM-as-Judge）都具有重要参考价值。

## 关键发现

- **百分比一致性（percent agreement）**是最简单但最不可靠的信度指标，因为它未校正随机一致性（chance agreement）
- **Cohen's kappa** 通过减去随机一致性来修正百分比一致性，是最广泛使用的信度指标
- **Weighted kappa** 适用于有序分类（ordinal）数据，区分"差一个等级"和"差两个等级"的不同严重程度
- **Kappa 的解读标准**：

| Kappa 值 | 一致性水平 |
|----------|----------|
| < 0 | 差于随机（poor） |
| 0.00 - 0.20 | 轻微一致（slight） |
| 0.21 - 0.40 | 一般一致（fair） |
| 0.41 - 0.60 | 中等一致（moderate） |
| 0.61 - 0.80 | 较高一致（substantial） |
| 0.81 - 1.00 | 几乎完全一致（almost perfect） |

- **建议最低标准**：McHugh 建议在大多数应用中，kappa 值应至少达到 0.61（substantial）才能认为评分具有可接受的信度
- **样本量影响**：样本量不足会导致 kappa 估计不稳定

## 方法论

文章采用教程式写作，通过具体计算示例演示各信度指标的计算过程：

1. **百分比一致性**：一致判断数 / 总判断数
2. **Cohen's kappa**：κ = (Po - Pe) / (1 - Pe)，其中 Po 为观察一致率，Pe 为期望随机一致率
3. **Weighted kappa**：引入权重矩阵，对不同程度的不一致赋予不同惩罚
4. 文章还讨论了 kappa 的假设条件和适用范围

## 重要数据

- 百分比一致性系统性地高估真实一致程度，在分类数少时尤为明显
- 当类别分布极度不均衡（prevalence problem）时，kappa 可能产生误导性低值
- 评分者数量超过 2 人时，应使用 Fleiss' kappa 或其他多评分者信度指标
- 文章被广泛引用（数千次），是评估信度领域的标准参考

## 局限性

- 文章主要面向医学和生物学研究者，示例偏向临床场景
- 未深入讨论连续型数据的信度评估方法（如 ICC, intraclass correlation coefficient）
- 对 kappa 的 paradox 问题（如 prevalence 和 bias 的影响）讨论不够深入
- 未涉及 LLM 评测场景中信度评估的特殊挑战（如 LLM 的确定性 vs 人类的随机性）

## 相关页面

- [Rubric-based 评测方法](../concepts/rubric-based-evaluation.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [Rubric 设计研究](./rubric-design-education.md)
