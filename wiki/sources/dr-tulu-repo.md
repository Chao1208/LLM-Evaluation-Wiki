---
title: "DR Tulu: Reinforcement Learning with Evolving Rubrics for Deep Research"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [DR Tulu, Rubric, 强化学习, 深度研究, Ai2, GRPO, 开源模型]
sources: [raw/github/dr-tulu/README.md]
---

## 概述

DR Tulu 是由 Allen Institute for AI (Ai2) 联合 UW、CMU、MIT 研究者开发的首个开源**深度研究（Deep Research）模型**。核心创新是使用**演化评分标准（Evolving Rubrics）**进行强化学习训练，使 DR Tulu-8B 在长篇深度研究基准上匹配 OpenAI DR 的表现。

论文：arXiv:2511.19399（2025 年 11 月）

## 关键特性

### 三大组件

1. **Agent 库**（`dr-agent-lib`）：基于 MCP 的工具后端，高并发异步请求管理，灵活的 prompting 接口
2. **RL 训练**：基于 Open-Instruct，使用 GRPO + evolving rubrics 训练深度研究 agent
3. **SFT 训练**：基于 LLaMA-Factory 的监督微调

### Evolving Rubrics（演化评分标准）

核心理念：评分标准不是静态的，而是随训练进程**动态演化**——
- 初期使用较宽松的评分标准
- 随着模型能力提升，逐步引入更严格、更细粒度的标准
- 这种"curriculum learning"式的 rubric 演化有效引导模型从简单到复杂能力的发展

### 评测支持

仓库内置多个评测基准支持：
- **HealthBench**：医疗健康研究
- **Deep Research Bench**：深度研究能力
- **SimpleQA**：简单问答
- **Research QA**：研究问答
- **Genetic Diseases**：遗传病领域
- **2Wiki / WebWalker**：网络搜索与综合

### 部署要求

- 2 GPU 即可运行评测（vLLM 部署 + MCP 服务器）
- 支持交互式 CLI demo
- 依赖外部 API：Serper（搜索）、Semantic Scholar、Jina Reader

## 技术亮点

- **GRPO（Group Relative Policy Optimization）**：替代传统 PPO 的高效 RL 算法
- **MCP 工具后端**：标准化的外部工具调用接口
- **Rubric 作为奖励信号**：结构化评分标准替代简单的标量奖励

## 核心成果

- DR Tulu-8B **匹配 OpenAI DR**（Deep Research）在长篇研究基准上的表现
- 仅 8B 参数，相比商业闭源模型极为高效
- 完全开源：模型、数据、训练代码

## 与评测相关的启示

1. **Evolving Rubrics 概念**：Rubric 不应是静态的——这为 [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)引入了动态演化维度
2. **Rubric 驱动 RL**：与 [RL with Rubric Anchors](../sources/rl-rubric-anchors.md)（蚂蚁/浙大）和 [Rubicon](../sources/rubicon-news.md) 框架思路一致，形成"Rubric for RL"研究趋势
3. **评测覆盖度**：内置多基准评测框架，体现了模型开发与评测一体化的最佳实践
4. **小模型高效**：8B 模型匹配商业级 Deep Research，质量优化重于规模扩展

## 相关页面

- [RL with Rubric Anchors 论文](../sources/rl-rubric-anchors.md)
- [Rubicon 框架报道](../sources/rubicon-news.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [Tulu 3](../sources/2411-recent-eval.md)
- [LLM-as-Judge 方法论](../concepts/llm-as-judge.md)
- [RewardBench](../sources/rewardbench.md)

（来源：[GitHub - allenai/dr-tulu](https://github.com/allenai/dr-tulu)）
