---
title: "EvalScope: 模型评测一站式解决方案"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [评测框架, ModelScope, 多模态, 性能测试, Arena, 可视化, 一站式平台]
sources: [raw/github/evalscope/README.md]
---

## 概述

EvalScope 是由 [ModelScope 社区](https://modelscope.cn/)（阿里巴巴达摩院）创建的一站式大模型评测框架，GitHub 仓库为 `modelscope/evalscope`。项目前身名为 `llmuses`，2024 年 7 月更名为 EvalScope。2025 年 8 月发布了 1.0 版本重构，建立了更加模块化和可扩展的 API 层。

EvalScope 的定位是为大模型开发者提供从通用能力评测、多模型对比到性能压测的全流程支持，是目前中文生态中功能最全面的开源评测框架之一。

## 关键特性

### 综合评测能力
- **内置大量 benchmark**：MMLU、C-Eval、GSM8K、ARC、MATH-500、GPQA-Diamond、SuperGPQA、SimpleQA、IFEval、HumanEval、LiveCodeBench、SWE-bench 等数十个主流评测集
- **多模态支持**：LLM、VLM（视觉语言模型）、Embedding、Reranker、AIGC（文生图、图像编辑）、音频（Fleurs、LibriSpeech）
- **代码评测**：支持 HumanEval、LiveCodeBench、MBPP、MultiPL-E、SWE-bench，并支持沙箱环境运行
- **Agent 评测**：支持 ToolBench、BFCL-v3/v4、tau-bench/tau2-bench 等 Agent 能力评测
- **NER 评测**：支持 CoNLL-2003、OntoNotes5 等命名实体识别评测

### 多后端集成
- **Native 后端**：自有评测引擎
- **OpenCompass 后端**：侧重文本评测
- **VLMEvalKit 后端**：侧重多模态评测
- **RAGEval 后端**：支持 MTEB/CMTEB 和 RAGAS 的 RAG 评测
- 第三方工具集成（ToolBench、Needle-in-a-Haystack 等）

### 推理性能压测
- 支持 TTFT（首 token 延迟）、TPOT（token 生成延迟）、吞吐量等关键指标
- SLA 自动调优：自动测试特定延迟/吞吐条件下的最大并发
- 支持 `/v1/completions` 和 `/v1/chat/completions` 端点
- 支持 Embedding 和 Rerank 模型压测
- 结果记录到 Wandb、SwanLab、ClearML

### 交互与可视化
- **WebUI 可视化**：基于 Gradio 的交互界面，支持模型对比、报告总览、详细分析
- **Arena 模式**：多模型 Pairwise Battle，生成胜率排行榜
- **HTML 报告**：支持评测进度追踪和 HTML 格式可视化报告

### 灵活的使用方式
- 命令行 `evalscope eval` / Python API `run_task(TaskConfig)` / YAML 配置文件
- 支持 OpenAI API、Anthropic Claude API、本地模型多种接入方式
- Agent Skill 支持：自然语言驱动评测（"评测 Qwen2.5-7B 在 gsm8k 上的表现"）

## 支持的模型/基准

### 模型接入方式
- 本地模型：通过 ModelScope 加载
- API 模型：OpenAI API 兼容、Anthropic Claude API、vLLM 部署的服务

### 主要支持的 benchmark（不完全列表）

| 类别 | 评测集 |
|------|--------|
| 通用知识 | MMLU, C-Eval, SuperGPQA, Humanity's Last Exam |
| 数学推理 | GSM8K, MATH-500, AIME24/25, AMC, MathQA |
| 代码生成 | HumanEval, LiveCodeBench, MBPP, MultiPL-E, SWE-bench |
| 指令遵循 | IFEval, Multi-IF, IFBench |
| 事实准确性 | SimpleQA, Chinese SimpleQA |
| 多模态 | MathVista, MMMU, MMBench, MMStar, OCRBench, DocVQA |
| Agent 能力 | ToolBench, BFCL-v3/v4, tau-bench, Terminal-Bench |
| RAG | RAGAS, MTEB/CMTEB |
| 长文本 | DocMath, FRAMES, Needle-in-a-Haystack, MRCR |
| 推理效率 | ProcessBench, MuSR |
| 健康医疗 | HealthBench |
| NER | CoNLL-2003, OntoNotes5, WNUT2017 |

## 技术架构

EvalScope 采用三层架构：

1. **输入层**
   - 模型来源：API 模型（OpenAI API）、本地模型（ModelScope）
   - 数据集：标准 benchmark（MMLU/GSM8K 等）、自定义数据（MCQ/QA）

2. **核心功能层**
   - 多后端评测：Native、OpenCompass、MTEB、VLMEvalKit、RAGAS
   - 性能监控：支持多种模型服务 API 和数据格式
   - 工具扩展：ToolBench、Needle-in-a-Haystack 等

3. **输出层**
   - 结构化报告：JSON、Table、Logs
   - 可视化平台：Gradio、Wandb、SwanLab

v1.0 引入了标准化的数据模型（benchmarks、samples、results）、基于注册表的组件设计（benchmarks 和 metrics）、以及重写的核心 evaluator。

## 与评测相关的启示

1. **一站式 vs 专精**：EvalScope 代表了"大而全"的评测框架思路，通过多后端集成覆盖各类评测场景，降低了评测门槛但增加了系统复杂度
2. **中文评测生态**：作为阿里 ModelScope 生态的一部分，EvalScope 对中文评测场景支持较好（C-Eval、Chinese SimpleQA 等），适合国内模型开发者
3. **性能压测的重要性**：除了准确性评测外，EvalScope 将推理性能压测（TTFT、TPOT、吞吐量）纳入框架，体现了工业界对模型服务性能的重视
4. **评测效率优化**：支持 `eval_batch_size` 加速评测、SLA 自动调优等功能，关注评测本身的效率
5. **Arena 模式的价值**：Pairwise Battle 排名机制提供了不同于静态 benchmark 的动态评测视角，与 [MT-Bench/Chatbot Arena](../sources/mt-bench-chatbot-arena.md) 的理念一致
6. **Agent 评测趋势**：大量集成 Agent 相关 benchmark（ToolBench、BFCL、tau-bench），反映了当前评测从静态问答向动态交互能力评测的演进

## 相关页面

- [LLM-as-Judge 评测方法](../concepts/llm-as-judge.md)
- [MT-Bench 与 Chatbot Arena](../sources/mt-bench-chatbot-arena.md)
- [MMLU Benchmark](../sources/mmlu.md)
- [VLMEvalKit](../sources/vlmevalkit.md)
- [HELM 评测框架](../sources/helm.md)
- [MathVista](../sources/mathvista.md)
- [IFEval](../sources/ifeval.md)
- [OpenAI](../entities/openai.md)
