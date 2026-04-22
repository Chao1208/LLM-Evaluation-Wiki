---
title: "BEIR: 信息检索零样本评测异质基准"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [信息检索, benchmark, 零样本评测, dense-retrieval, sparse-retrieval, reranking, NeurIPS]
sources: [raw/github/beir/README.md]
---

## 概述

BEIR（Benchmarking IR）是一个面向信息检索（Information Retrieval, IR）模型零样本评测的异质基准，GitHub 仓库为 `beir-cellar/beir`。项目由 **TU Darmstadt（达姆施塔特工业大学）UKP Lab**、**University of Waterloo（滑铁卢大学）** 和 **Hugging Face** 联合开发，核心作者为 Nandan Thakur、Nils Reimers、Andreas Ruckle、Abhishek Srivastava、Iryna Gurevych 等。

BEIR 发表于 NeurIPS 2021 Datasets and Benchmarks Track，论文为 "BEIR: A Heterogenous Benchmark for Zero-shot Evaluation of Information Retrieval Models"。后续在 SIGIR 2024 发表了 "Resources for Brewing BEIR: Reproducible Reference Models and an Official Leaderboard"。

BEIR 的核心价值在于提供了一个**跨领域、异质性**的检索评测基准，用于测试检索模型在**零样本迁移**（zero-shot transfer）场景下的泛化能力。

## 关键特性

- **17 个异质数据集**：覆盖事实检索、问答、论证挖掘、引用预测、实体链接、事实验证等多种 IR 任务类型
- **零样本评测设计**：评测模型在未见过的数据集上的迁移能力，而非在同一数据集上的训练-测试性能
- **多种检索架构支持**：
  - Lexical（词法）：如 BM25
  - Dense（稠密向量）：如 Sentence-BERT、Contriever
  - Sparse（稀疏向量）：如 SPLADE
  - Reranking（重排序）：如 Cross-Encoder
- **统一评测框架**：提供易用的 Python API 进行数据加载、模型评测和结果保存
- **多种评测指标**：NDCG@k、MAP@k、Recall@k、Precision@k、MRR@k（k = 1, 3, 5, 10, 100, 1000）
- **多后端推理支持**：Sentence-BERT、HuggingFace Transformers、vLLM（含 LoRA 支持）、API（如 Cohere）
- **Hugging Face 集成**：所有数据集和模型均可通过 Hugging Face Hub 访问
- **官方 Leaderboard**：提供可复现的参考模型和统计分析

## 支持的模型/基准

### 数据集（17 个）

| 数据集 | 领域 | 查询数 | 语料库规模 | 公开 |
|--------|------|--------|-----------|------|
| MSMARCO | 网页搜索 | 6,980 | 8.84M | Yes |
| TREC-COVID | 医学 | 50 | 171K | Yes |
| NFCorpus | 营养/医学 | 323 | 3.6K | Yes |
| BioASQ | 生物医学 | 500 | 14.91M | No |
| Natural Questions (NQ) | 开放域问答 | 3,452 | 2.68M | Yes |
| HotpotQA | 多跳问答 | 7,405 | 5.23M | Yes |
| FiQA-2018 | 金融 | 648 | 57K | Yes |
| Signal-1M | 新闻/推特 | 97 | 2.86M | No |
| TREC-NEWS | 新闻 | 57 | 595K | No |
| Robust04 | 新闻 | 249 | 528K | No |
| ArguAna | 论证挖掘 | 1,406 | 8.67K | Yes |
| Touche-2020 | 论证检索 | 49 | 382K | Yes |
| CQADupstack | 社区问答 | 13,145 | 457K | Yes |
| Quora | 重复问题检测 | 10,000 | 523K | Yes |
| DBPedia | 实体检索 | 400 | 4.63M | Yes |
| SCIDOCS | 科学文献 | 1,000 | 25K | Yes |
| FEVER | 事实验证 | 6,666 | 5.42M | Yes |
| Climate-FEVER | 气候事实验证 | 1,535 | 5.42M | Yes |
| SciFact | 科学事实验证 | 300 | 5K | Yes |

### 支持的检索模型类型
- **Dense Retrieval**：Sentence-BERT、HuggingFace 模型（如 e5-mistral-7b-instruct）、vLLM + LoRA
- **API-based**：Cohere Embed API 等
- **Lexical**：BM25 等传统方法
- **Reranking**：Cross-Encoder 等重排序模型

## 技术架构

BEIR 提供了简洁的 Python API：

- **数据加载**：`GenericDataLoader` 统一加载 corpus、queries、qrels
- **模型封装**：
  - `models.SentenceBERT`：Sentence-BERT 模型
  - `models.HuggingFace`：HuggingFace Transformers 模型（支持自定义 pooling、prompt、Flash Attention）
  - `models.VLLMEmbed`：vLLM 推理模型（支持 LoRA）
  - `apis.CohereEmbedAPI`：Cohere API
- **检索引擎**：`DenseRetrievalExactSearch (DRES)` 精确搜索
- **评测**：`EvaluateRetrieval` 类统一评测和指标计算
- **结果保存**：支持 TREC 格式的 runfile 和 JSON 结果

核心评测流程：
1. 下载并加载数据集 → 2. 加载检索模型 → 3. 编码并检索 → 4. 计算评测指标 → 5. 保存结果

## 与评测相关的启示

1. **零样本迁移是检索模型的核心挑战**：BEIR 揭示了一个重要发现——许多在 MSMARCO 上表现优异的模型，在其他领域的零样本迁移中表现大幅下降。这促使研究社区关注检索模型的泛化能力
2. **异质性评测的价值**：单一数据集无法全面评估模型能力，BEIR 通过聚合 17 个不同领域/任务的数据集，提供了更可靠的模型排名。这一理念对 LLM 评测同样适用
3. **评测指标的统计分析**：SIGIR 2024 的后续论文指出，简单将多个数据集的分数平均为单一指标难以解读，应使用 effect size 等统计方法量化模型差异。这对所有多数据集 benchmark 都有借鉴意义
4. **从 IR 到 RAG**：BEIR 中的检索评测方法直接影响了 RAG（Retrieval-Augmented Generation）系统的评测标准。Embedding 模型和 Reranker 的评测正是 RAG 系统评测的基础
5. **可复现性**：BEIR 强调提供可复现的参考实现和官方 Leaderboard，这是评测基础设施的重要组成部分
6. **LLM 作为检索模型**：BEIR 最新支持 vLLM + LoRA 推理，反映了 LLM 在检索任务中的应用趋势

## 相关页面

- [LLM-as-Judge 评测方法](../concepts/llm-as-judge.md)
- [MMLU Benchmark](../sources/mmlu.md)
- [HELM 评测框架](../sources/helm.md)
- [EvalScope 评测框架](../sources/evalscope-repo.md)
- [LLM 评测综述 (Chang et al.)](../sources/llm-eval-survey-chang.md)
