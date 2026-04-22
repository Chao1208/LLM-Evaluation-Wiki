---
title: "评测方法论基础"
type: concept
created: 2026-04-22
updated: 2026-04-22
tags: [评测方法论, 综述, 统计方法, 信度, 幻觉, 过拟合, 交叉验证]
sources: []
---

# 评测方法论基础

## 概述

本页汇聚 LLM 评测领域的综述性研究和底层统计方法论文献，为理解评测体系的理论基础提供参考。内容分为两大板块：**LLM 评测全景综述**（宏观框架）和**评估统计方法论**（微观工具）。

## LLM 评测综述

### 综述文献对比

| 综述 | 期刊/会议 | 年份 | 核心框架 | 侧重点 |
|------|-----------|------|----------|--------|
| [Chang et al.](../sources/llm-eval-survey-chang.md) | ACM TIST | 2024 | What / Where / How 三维度 | 最全面的 LLM 评测全景图 |
| [Future Internet 综述](../sources/llm-eval-survey-future-internet.md) | Future Internet | 2023 | 自动指标 / 人工评估 / 基准数据集 | 聚焦文本生成评测方法选择 |
| [幻觉综述](../sources/hallucination-survey.md) | arXiv | 2023 | 事实性 / 忠实性分类 | 跨模态幻觉检测与缓解 |

### Chang et al. 三维度框架

这是目前最系统的 LLM 评测分类体系：

- **What**：评什么——自然语言处理、推理、知识、安全、对齐等能力维度
- **Where**：在哪评——静态 benchmark、动态对战（Arena）、人工评估
- **How**：怎么评——自动指标、LLM-as-Judge、人工标注、混合方法

### 幻觉问题

[幻觉综述](../sources/hallucination-survey.md)将幻觉分为两类：
- **事实性幻觉（Factuality）**：生成内容与客观事实不符
- **忠实性幻觉（Faithfulness）**：生成内容偏离给定的输入/上下文

这一分类对评测设计影响深远——[HealthBench](../benchmarks/healthbench.md) 和 [BigLaw Bench](../benchmarks/biglaw-bench.md) 的负向 criteria 正是为了捕捉这类幻觉。

## 评估统计方法论

### 核心方法文献

| 文献 | 主题 | 核心内容 | 对 LLM 评测的意义 |
|------|------|----------|-------------------|
| [评分者间信度](../sources/interrater-reliability.md) | Kappa 统计量 | Cohen's Kappa 原理、计算、解读标准（≥0.80 强一致） | LLM Judge 与人工一致率的衡量基础 |
| [ML 模型评估](../sources/ml-model-evaluation.md) | 交叉验证与指标 | 偏差-方差权衡、Accuracy/F1/AUC-ROC | 评测指标选择的理论依据 |
| [临床预测模型评估](../sources/clinical-prediction-model-eval.md) | TRIPOD+AI 指南 | 区分度、校准、决策曲线 | 高风险场景评测的标准参考 |
| [过拟合与模型调优](../sources/overfitting-model-tuning.md) | 正则化与验证 | 过拟合检测、超参调优、交叉验证 | Benchmark 泄漏与过拟合检测 |

### 关键概念映射

这些统计方法论概念在 LLM 评测中的对应关系：

| 统计方法论概念 | LLM 评测对应 |
|---------------|-------------|
| 评分者间信度（IRA） | LLM Judge 与人工专家的一致率（PRBench 81.3%） |
| Cohen's Kappa ≥ 0.80 | [Rubric-Based 评测](rubric-based-evaluation.md)的质量关卡标准 |
| 交叉验证 | 多次评测取平均（PRBench 3 次重复） |
| 校准（Calibration） | LLM Judge 校准方法（直接 Prompting / 校准网络 / 微调 Verifier） |
| 过拟合 / 数据泄漏 | Benchmark contamination（训练数据包含测试题） |
| 区分度（Discrimination） | Benchmark 是否能有效区分不同能力水平的模型 |

## 相关页面

- [Rubric-Based 评测方法论](rubric-based-evaluation.md)
- [LLM-as-Judge](llm-as-judge.md)
- [RLHF](rlhf.md)
- [LLM 评测综述 (Chang)](../sources/llm-eval-survey-chang.md)
- [LLM 评测综述 (Future Internet)](../sources/llm-eval-survey-future-internet.md)
- [幻觉综述](../sources/hallucination-survey.md)
- [评分者间信度](../sources/interrater-reliability.md)
- [ML 模型评估方法论](../sources/ml-model-evaluation.md)
- [临床预测模型评估](../sources/clinical-prediction-model-eval.md)
- [过拟合与模型调优](../sources/overfitting-model-tuning.md)
- [HealthBench](../benchmarks/healthbench.md)
- [BigLaw Bench](../benchmarks/biglaw-bench.md)
- [PRBench](../benchmarks/prbench.md)
