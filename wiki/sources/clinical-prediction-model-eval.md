---
title: "Evaluation of Clinical Prediction Models (Part 1): From Development to External Validation"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [clinical, prediction-model, evaluation, calibration, AUC, BMJ]
sources: [bmj-2023-074819.full.pdf]
---

## 概述

本文由 Collins 等人发表于 BMJ（2024），是关于临床预测模型评估的系统性指南（第一部分），涵盖从模型开发到外部验证的完整评估流程。文章基于 TRIPOD+AI 指南框架，详细阐述了预测模型评估的核心概念和标准方法，对理解评估方法论的通用原则具有重要参考价值。

## 关键发现

- **评估的三大维度**：
  - **区分度（discrimination）**：模型区分不同结局的能力，核心指标为 C-statistic（即 AUC）
  - **校准度（calibration）**：预测概率与实际发生率的一致性，通过校准曲线和校准斜率/截距评估
  - **临床实用性（clinical utility）**：通过决策曲线分析（decision curve analysis）评估模型在实际决策中的净收益
- **验证层次**：
  1. **内部验证（internal validation）**：使用同一数据的重采样方法（如 bootstrap）评估模型过拟合程度
  2. **时间验证（temporal validation）**：使用同一机构不同时间段的数据验证模型时间稳定性
  3. **外部验证（external validation）**：使用不同机构/人群的数据验证模型泛化能力
- **过拟合是核心风险**：模型在开发数据上表现良好但在新数据上退化，是最常见的评估陷阱
- **TRIPOD+AI 指南**：为包含 AI/ML 方法的预测模型提供了标准化报告框架

## 方法论

文章系统梳理了预测模型评估的方法论框架：

1. **区分度评估**：C-statistic/AUC 计算及置信区间估计
2. **校准度评估**：校准曲线、Hosmer-Lemeshow 检验、校准斜率与截距
3. **决策曲线分析**：在不同阈值概率下比较模型策略与"全治疗"/"全不治疗"策略的净收益
4. **内部验证方法**：交叉验证、bootstrap 验证及过拟合校正
5. **外部验证设计**：多中心验证、时间验证、地理验证

## 重要数据

| 评估维度 | 核心指标 | 含义 |
|----------|---------|------|
| 区分度 | C-statistic (AUC) | 0.5 = 随机，1.0 = 完美区分 |
| 校准度 | 校准斜率 | 1.0 = 完美校准 |
| 校准度 | 校准截距 | 0 = 无系统偏差 |
| 临床实用性 | 净收益 | 正值 = 优于默认策略 |

- 仅报告 AUC 而不报告校准度是常见的评估不足
- 外部验证中模型表现下降是普遍现象，需要模型更新策略

## 局限性

- 主要面向临床医学场景，部分方法（如决策曲线分析）需要领域适配才能用于 LLM 评测
- 聚焦二分类和生存分析预测模型，对多分类和生成任务的评估讨论有限
- TRIPOD+AI 指南仍在发展中，部分建议可能随时间更新
- 未深入讨论因果推断与预测模型评估的区别

## 相关页面

- [ML 模型评估方法](./ml-model-evaluation.md)
- [过拟合与模型调优](./overfitting-model-tuning.md)
- [Rubric-based 评测方法](../concepts/rubric-based-evaluation.md)
