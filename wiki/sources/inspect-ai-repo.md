---
title: "Inspect AI: UK AISI 大模型评测框架"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [评测框架, UK-AISI, AI安全, prompt-engineering, tool-use, multi-turn, 可扩展]
sources: [raw/github/inspect_ai/README.md]
---

## 概述

Inspect 是由 **英国 AI 安全研究所**（UK AI Security Institute, AISI）开发的大语言模型评测框架，GitHub 仓库为 `UKGovernmentBEIS/inspect_ai`。该框架以 AI 安全评测为核心定位，同时提供了通用的 LLM 评测能力。

Inspect 的设计理念是提供丰富的内置组件和高度可扩展的架构，使评测研究者能够方便地组合 prompt engineering、tool usage、multi-turn dialog 和 model graded evaluations 等技术来构建复杂的评测方案。

## 关键特性

- **200+ 预构建评测任务**：内置超过 200 个即开即用的评测任务，可直接在任意模型上运行（详见 https://inspect.aisi.org.uk/evals/）
- **丰富的内置组件**：
  - Prompt engineering 工具
  - Tool usage（工具调用）支持
  - Multi-turn dialog（多轮对话）支持
  - Model graded evaluations（模型评分评测）
- **高度可扩展**：通过 Python 包扩展 Inspect 的能力，支持新的 elicitation（诱导）和 scoring（评分）技术
- **Web UI**：提供基于 TypeScript/React 的前端可视化界面，位于 `src/inspect_ai/_view/ts-mono/` 子模块
- **严格的代码质量**：使用 Ruff 格式化和 lint、MyPy 类型检查、pytest 测试框架，要求所有函数有类型注解
- **异步支持**：支持 asyncio 和 trio 双后端（通过 anyio），测试框架自动在两种后端下运行
- **文件系统抽象**：支持 `s3://` URL、`file://` URI 和本地路径，通过 `filesystem()` 统一操作
- **文档系统**：基于 Quarto 的完整文档站点（https://inspect.aisi.org.uk/）

## 支持的模型/基准

### 评测任务
Inspect 内置 200+ 评测任务，覆盖多个领域。由于 README 未详细列出所有评测，具体列表参见官方文档 https://inspect.aisi.org.uk/evals/。根据 UK AISI 的安全评测定位，预计覆盖：
- 通用能力评测
- AI 安全相关评测（对齐、安全性、可控性）
- 推理能力评测
- 工具使用能力评测
- 多轮交互评测

### 模型支持
框架设计为模型无关（model-agnostic），可对接任意 LLM 服务。

## 技术架构

Inspect 采用 Python 编写的后端 + TypeScript/React 的前端架构：

- **后端**（Python）：
  - 核心评测引擎：位于 `src/inspect_ai/`
  - 沙箱工具：`src/inspect_sandbox_tools/`，支持容器注入和 RPC 通信
  - 异步并发：使用 `tg_collect()` 替代 `asyncio.gather()`，支持 anyio 双后端
  - 模型代理生命周期管理：启动、通信、终止流程
  - 时区处理架构：统一的时间数据处理模式

- **前端**（TypeScript/React）：
  - 位于 git 子模块 `src/inspect_ai/_view/ts-mono/`
  - 提供可视化评测界面

- **构建与质量保证**：
  - `ruff format` / `ruff check --fix`：代码格式化和 lint
  - `mypy`：类型检查
  - `pytest`：测试框架
  - 严格要求 Google style docstring 和类型注解

## 与评测相关的启示

1. **政府主导的 AI 安全评测**：Inspect 由英国政府机构（UK AISI）开发，代表了主权国家层面对 AI 安全评测基础设施的投入。这与学术界或企业主导的评测框架形成互补，更关注安全性和可控性
2. **评测即组合**：Inspect 的设计理念是将评测分解为可组合的原子能力（prompt engineering、tool use、multi-turn、model grading），研究者可以自由组合这些组件构建复杂评测。这种模块化设计值得其他框架借鉴
3. **Model Graded Evaluations 的重要性**：框架内置支持模型评分评测（即 [LLM-as-Judge](../concepts/llm-as-judge.md)），反映了该方法在现代评测中的核心地位
4. **工程质量标杆**：严格的类型检查、双异步后端支持、S3 路径抽象等设计展现了产品级评测框架的工程标准
5. **200+ 预构建评测的覆盖度**：大规模预构建评测集降低了使用门槛，使评测研究者可以快速复现和对比结果

## 相关页面

- [LLM-as-Judge 评测方法](../concepts/llm-as-judge.md)
- [MT-Bench 与 Chatbot Arena](../sources/mt-bench-chatbot-arena.md)
- [HELM 评测框架](../sources/helm.md)
- [EvalScope 评测框架](../sources/evalscope-repo.md)
- [Rubric-Based Evaluation](../concepts/rubric-based-evaluation.md)
