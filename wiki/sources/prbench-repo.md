---
title: "PRBench 官方仓库"
type: source
created: 2026-04-09
updated: 2026-04-09
tags: [PRBench, Scale AI, 法律, 金融, rubric, 仓库]
sources: [raw/benchmarks/PRBench/README.md, raw/benchmarks/PRBench/README_zh.md]
---

# PRBench 官方仓库

## 概述

Scale AI 开源的 PRBench（Professional Reasoning Benchmark）评测工具仓库。提供轻量级评测 harness，支持基于 rubric 的大规模专业领域推理评测。主入口为 evals.py，集成了模型回复生成、LLM-judge 自动评分、缓存、重试和报告等功能。

论文和完整详情：https://scale.com/research/prbench
可视化探索工具：https://prbench-explorer.vercel.app/

## 关键发现

### 1. 数据集规模确认

- 1,100 个专家编写的对话（金融 + 法律）
- 19,356 条专家精心编写的 rubric criteria（每任务 10-30 条）
- 覆盖 114 个国家、47 个美国司法管辖区、25 个专业主题
- 高难度子集：Finance-300、Legal-250

### 2. 评测流水线

- **主入口**：`python evals.py config.yaml`
- **回复来源**：`sampled`（实时采样）或 `prefilled`（预填充 JSON，格式 task→response）
- **配置项**：`litellm_key_path`（API 密钥路径）、`response_model_names`（待评测模型列表）、`debug` 模式（仅前 2 个样本）
- **底层 SDK**：OpenAI SDK

### 3. 报告指标

- 论文使用 `mean_clipped` 分数作为核心指标
- 结果保存在 `results/` 目录下
- 单条数据的详细结果在 `outputs/` 目录下

## 问题与思考

1. 仓库代码较简洁，核心逻辑集中在 evals.py 单文件
2. 使用 LiteLLM 作为底层，理论上可对接多家模型 API
3. 数据集本身不在仓库中，需另行获取

## 相关页面

- [PRBench](../benchmarks/prbench.md)
- [Scale AI](../entities/scale-ai.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
