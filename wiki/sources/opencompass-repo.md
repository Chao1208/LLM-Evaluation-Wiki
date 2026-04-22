---
title: "OpenCompass 评测平台"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [评测平台, 开源工具, 综合评测, 分布式评测, LLM评测]
sources: [raw/github/opencompass/README.md]
---

## 概述

OpenCompass 是由上海人工智能实验室（Shanghai AI Lab / PJLab）开发的一站式大模型评测平台，旨在提供公正、开放、可复现的大模型评测基准。项目获得 Meta AI 官方推荐，作为 Llama 系列模型的验证工具之一。GitHub 仓库地址为 `open-compass/opencompass`。

OpenCompass 2.0 由三个核心组件构成：
- **CompassKit**：评测工具集，支持 LLM 和多模态模型评测
- **CompassHub**：基准浏览器，提供标准化的 benchmark 发现和使用界面
- **CompassRank**：排行榜系统，整合开源与闭源 benchmark 的模型排名

## 关键特性

1. **全面的模型和数据集支持**：预置 20+ HuggingFace 和 API 模型配置，70+ 数据集约 40 万道题目，覆盖五大能力维度
2. **高效分布式评测**：一行命令实现任务分发与分布式评估，数十亿参数模型仅需数小时完成全面评测
3. **多样化评测范式**：支持 zero-shot、few-shot、chain-of-thought 评测，兼容标准和对话式 prompt 模板
4. **模块化设计高可扩展**：支持自定义模型、数据集、任务分发策略和集群管理系统
5. **实验管理与报告**：通过配置文件完整记录每次实验，支持实时结果汇报
6. **多推理后端加速**：支持 HuggingFace（默认）、LMDeploy、vLLM 三种推理加速后端，一键切换
7. **CascadeEvaluator**：级联评估器，允许多个评估器按序列工作，构建自定义评测流水线
8. **LLM Judge 支持**：内置 `GenericLLMEvaluator`（LLM-as-Judge）和 `MATHVerifyEvaluator`（数学推理评测）
9. **ModelScope 集成**：支持按需从 ModelScope 加载数据集，无需全量下载

## 支持的模型

### 开源模型
Alpaca、Baichuan、BlueLM、ChatGLM2/3、Gemma/Gemma2、InternLM 系列、LLaMA/LLaMA3、Qwen/Qwen2.5、TigerBot、Vicuna、WizardLM、Yi 等，以及所有 HuggingFace AutoModel 兼容模型。

### API 模型
OpenAI（含 o1 系列）、Gemini、Claude、智谱 AI（ChatGLM）、百川、字节（云雀）、华为（盘古）、360、百度（ERNIEBot）、MiniMax（ABAB-Chat）、商汤（nova）、讯飞（星火）等。

## 支持的基准/数据集

通过 CLI 或 Python 脚本配置，涵盖但不限于以下数据集（可通过 ModelScope 按需加载）：

HumanEval、TriviaQA、CommonsenseQA、TyDiQA、StrategyQA、CMMLU、LAMBADA、PIQA、C-Eval、MATH、LCSTS、Xsum、WinoGrande、OpenBookQA、AGIEval、GSM8K、Natural Questions、RACE、SIQA、MBPP、[MMLU](../sources/mmlu.md)、HellaSwag、ARC、BBH、GAOKAO-BENCH、OCNLI、CMNLI 等。

此外还支持 SuperGPQA（知识评测）、MMMLU（多语言）、MuSR（复杂推理）、BABILong（长上下文）、RULER（长上下文多维度）、SciCode（科研编码）、NeedleBench（大海捞针）、AIME2024 等新兴 benchmark。

## 技术架构

- **安装方式**：支持 pip 安装（`pip install -U opencompass`）和源码安装，提供 `[full]`、`[lmdeploy]`、`[vllm]`、`[api]` 等可选依赖
- **配置系统**：v0.4.0 后将所有配置文件（datasets/models/summarizers）整合进 opencompass 包内
- **CLI + Python 脚本**：简单评测推荐 CLI 模式，复杂评测推荐 Python 脚本
- **数据并行**：通过 `--max-num-worker` 实现数据并行，`--hf-num-gpus` 实现模型并行
- **答案提取**：支持通过模型后处理进行答案提取（如 XFinder），更准确反映模型能力
- **评测配置约定**：`_ppl` 配置用于 base model（困惑度），`_gen` 配置用于 base model 和 chat model（生成式），`_llm_judge_gen` 配置用于 LLM Judge 评测

## Roadmap

- 主观评测：CompassArena 已发布
- 长上下文：已支持（排行榜在建）
- 代码评测：已支持非 Python 语言
- Agent 评测：已支持工具使用评测
- 鲁棒性：已支持多种攻击方法评测

## 与评测相关的启示

1. **一站式评测平台的价值**：OpenCompass 整合了模型接入、数据集管理、评测执行、结果汇总的完整流程，降低了评测门槛。对于构建评测体系，这种端到端设计值得参考。
2. **推理后端可切换**：支持 HuggingFace、LMDeploy、vLLM 三种后端，说明评测效率与推理优化密切相关，选择合适的推理框架可以大幅提升评测吞吐。
3. **LLM Judge 集成趋势**：内置 [LLM-as-Judge](../concepts/llm-as-judge.md) 评估器，反映了从纯规则匹配向 LLM 评审转变的行业趋势。CascadeEvaluator 支持多评估器级联，可构建"规则优先 + LLM 兜底"的混合评测流水线。
4. **覆盖中国模型生态**：对国产模型（百川、ChatGLM、Qwen、InternLM、Yi 等）和中文 benchmark（C-Eval、CMMLU、GAOKAO-BENCH）有良好支持，适合中国 LLM 评测需求。
5. **答案提取后处理**：通过 XFinder 等后处理模型进行答案提取，而非仅依赖正则匹配，有助于更准确反映模型真实能力。

## 相关页面

- [MMLU 基准](../sources/mmlu.md)
- [LLM-as-Judge 评测方法](../concepts/llm-as-judge.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [MT-Bench & Chatbot Arena](../sources/mt-bench-chatbot-arena.md)
- [Chain-of-Thought Prompting](../sources/chain-of-thought.md)
- [lm-evaluation-harness 评测框架](../sources/lm-evaluation-harness-repo.md)
- [HELM 评测框架](../sources/helm-repo.md)
