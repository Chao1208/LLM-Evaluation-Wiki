---
title: "评测框架与工具"
type: concept
created: 2026-04-22
updated: 2026-04-22
tags: [评测框架, 工具, benchmark, 开源, 评测平台]
sources: []
---

## 概述

LLM 评测框架是连接模型与 benchmark 的基础设施层，负责模型接入、数据集管理、评测执行、指标计算和结果呈现。随着大模型生态的快速发展，评测框架已分化为多个方向：综合评测平台覆盖数十个 benchmark 和多种模型后端，LLM-as-Judge 工具利用强模型自动评审生成质量，领域专项工具聚焦代码、检索、鲁棒性等特定维度，安全评测框架则面向 AI 对齐与可控性。

选择合适的评测框架取决于评测目标（排行榜复现 vs 快速迭代 vs 安全审计）、模型类型（开源本地 vs API 闭源）、评测维度（客观准确率 vs 主观质量 vs 推理性能）以及团队资源。本页汇总 12 个主要框架的定位、特色和适用场景，为评测选型提供导航。

## 框架对比

| 框架名称 | 机构 | 支持基准数 | 核心特色 | 许可证 | 适用场景 |
|---------|------|-----------|---------|--------|---------|
| [OpenCompass](../sources/opencompass-repo.md) | 上海 AI Lab | 70+ 数据集，~40 万题 | 一站式平台，三种推理后端，CascadeEvaluator，中国模型生态支持好 | Apache 2.0 | 国产模型全面评测、中文 benchmark |
| [lm-evaluation-harness](../sources/lm-evaluation-harness-repo.md) | EleutherAI | 60+ benchmark，数百子任务 | HuggingFace Open LLM Leaderboard 后端，学术事实标准，12+ 模型后端 | MIT | 学术论文复现、排行榜对齐 |
| [HELM](../sources/helm-repo.md) | Stanford CRFM | MMLU-Pro/GPQA/IFEval/WildBench 等 | 多维指标（偏见/毒性/效率），Web UI 逐条检视，VHELM/MedHELM 垂直扩展 | Apache 2.0 | 全面多维评测、垂直领域扩展 |
| [EvalScope](../sources/evalscope-repo.md) | 阿里/ModelScope | 数十个 benchmark + 多模态 + Agent | 多后端集成（Native/OpenCompass/VLMEvalKit/RAGEval），推理性能压测，Arena 模式 | Apache 2.0 | 一站式评测 + 性能压测、阿里生态 |
| [Inspect AI](../sources/inspect-ai-repo.md) | UK AISI | 200+ 预构建任务 | AI 安全评测定位，组合式设计（prompt/tool-use/multi-turn），政府级工程质量 | MIT | AI 安全评测、工具使用/多轮评测 |
| [PromptBench](../sources/promptbench-repo.md) | Microsoft Research | GLUE/MMLU/BBH/GSM8K 等 + 多模态 | 四层级对抗 prompt 攻击，DyVal 动态评测抗数据污染，PromptEval 高效采样 | MIT | prompt 鲁棒性评测、动态评测 |
| [AlpacaEval](../sources/alpaca-eval-repo.md) | Stanford Tatsu Lab | 805 条指令集 | LC Win Rates 与 Chatbot Arena 相关性 0.98，< $10/< 3 分钟，长度偏差控制 | Apache 2.0 | 指令遵循快速自动评测 |
| [FastChat / Chatbot Arena](../sources/fastchat-repo.md) | LMSYS (UC Berkeley) | MT-Bench 80 题 + Arena 众包 | 150 万+ 人类投票 Elo 排行榜，匿名对战，评测金标准 | Apache 2.0 | 人类偏好排名、模型服务 + 评测一体化 |
| [HumanEval](../sources/human-eval-repo.md) | [OpenAI](../entities/openai.md) | 164 题 Python 编程 | 基于执行的 pass@k 评测，代码生成评测范式开创者 | MIT | 代码生成能力评测 |
| [BEIR](../sources/beir-repo.md) | TU Darmstadt / Waterloo / HuggingFace | 17 个异质 IR 数据集 | 零样本跨领域检索评测，Dense/Sparse/Reranking 全覆盖 | Apache 2.0 | 检索模型/Embedding 评测、RAG 基础 |
| [simple-evals](../sources/simple-evals-repo.md) | [OpenAI](../entities/openai.md) | 8 个核心 benchmark | 零样本 + CoT 哲学，极简实现，官方模型跑分透明度保障 | MIT | 快速模型对比、官方分数复现 |
| [Prometheus](../sources/prometheus-repo.md) | KAIST AI | 自定义 rubric 评测 | 开源 LLM-as-Judge 模型，rubric 驱动 1-5 分细粒度评分 + 文字反馈 | Apache 2.0 | 细粒度 rubric 评测、替代 GPT-4 评审 |

## 分类

### 综合评测平台

面向多 benchmark、多模型的全面评测需求，提供从模型接入到结果汇总的端到端流程。

- **[OpenCompass](../sources/opencompass-repo.md)**：覆盖 70+ 数据集，支持 HuggingFace/LMDeploy/vLLM 三种推理后端，对中国模型和中文 benchmark 支持最佳。内置 LLM Judge 和 CascadeEvaluator。
- **[lm-evaluation-harness](../sources/lm-evaluation-harness-repo.md)**：学术评测事实标准，HuggingFace Open LLM Leaderboard 后端。支持 12+ 模型后端，YAML 配置驱动的任务系统，被数百篇论文引用。
- **[HELM](../sources/helm-repo.md)**：Stanford CRFM 旗舰项目，强调"超越准确率"的多维评测（偏见、毒性、效率）。已扩展至 VHELM（视觉语言）、MedHELM（医疗）、HEIM（文生图）等垂直方向。
- **[EvalScope](../sources/evalscope-repo.md)**：阿里 ModelScope 生态的一站式方案，集成 Native/OpenCompass/VLMEvalKit/RAGEval 四种后端，独有推理性能压测（TTFT/TPOT/吞吐量）和 Arena 模式。

### LLM-as-Judge 评测

使用强 LLM 作为自动评审，评估模型输出的主观质量，与客观指标评测互补。详见 [LLM-as-Judge](llm-as-judge.md)。

- **[AlpacaEval](../sources/alpaca-eval-repo.md)**：成对比较 + Length-Controlled Win Rates，与 Chatbot Arena 相关性 0.98。评测成本 < $10，时间 < 3 分钟，是 LLM-as-Judge 的最佳实践范例。
- **[FastChat / MT-Bench / Chatbot Arena](../sources/fastchat-repo.md)**：MT-Bench 提供 80 道多轮开放式评测题（GPT-4 Judge），Chatbot Arena 通过 150 万+ 人类匿名投票构建 Elo 排行榜，是评测方法的"校准锚点"。
- **[Prometheus](../sources/prometheus-repo.md)**：开源评测模型（基于 Llama-2-Chat），通过自定义 score rubric 实现 1-5 分细粒度评分 + 文字反馈。支持绝对评分和相对评分双模式，是开源 LLM-as-Judge 的先驱。

### 领域专项评测

聚焦特定能力维度，提供该领域内深度且专业的评测方案。

- **[HumanEval](../sources/human-eval-repo.md)**（代码生成）：164 道手写 Python 编程题，基于执行结果的 pass@k 指标，开创了功能正确性评测范式。轻量但影响深远，后续催生 HumanEval+、MBPP、SWE-bench 等。
- **[BEIR](../sources/beir-repo.md)**（信息检索）：17 个异质数据集覆盖事实检索、问答、论证挖掘等 IR 任务，测试零样本跨领域迁移能力。支持 Dense/Sparse/Reranking 多种检索架构，是 RAG 系统评测的基础。
- **[PromptBench](../sources/promptbench-repo.md)**（鲁棒性）：四层级对抗 prompt 攻击（字符/词/句子/语义），DyVal 动态评测缓解数据污染，PromptEval 用 5% 数据达到 2% 误差。揭示了 prompt 变化对模型表现的显著影响。

### 安全评测

面向 AI 安全性、对齐和可控性的专项评测。

- **[Inspect AI](../sources/inspect-ai-repo.md)**：英国 AI 安全研究所（UK AISI）开发，200+ 预构建评测任务，组合式设计支持 prompt engineering、tool usage、multi-turn dialog 和 model graded evaluations 的自由组合。代表主权国家层面的 AI 安全评测基础设施投入。

### 轻量对比

面向快速验证和官方分数透明化。

- **[simple-evals](../sources/simple-evals-repo.md)**：[OpenAI](../entities/openai.md) 开源的极简评测库，覆盖 MMLU/MATH-500/GPQA/DROP/MGSM/HumanEval/SimpleQA/HealthBench 8 个核心 benchmark。采用零样本 + CoT 设定，不使用 few-shot 或角色扮演，目标是确保官方发布的模型分数可复现。

## 选型指南

- **学术论文复现 / 排行榜对齐**：首选 [lm-evaluation-harness](../sources/lm-evaluation-harness-repo.md)，它是 Open LLM Leaderboard 的后端，评测结果在学术界认可度最高。
- **国产模型 / 中文评测**：首选 [OpenCompass](../sources/opencompass-repo.md) 或 [EvalScope](../sources/evalscope-repo.md)，两者对国产模型 API 和中文 benchmark（C-Eval、CMMLU）支持完善。
- **多维度全面评测（偏见/毒性/效率）**：首选 [HELM](../sources/helm-repo.md)，其"超越准确率"的理念在学术界具有标杆意义。
- **快速指令遵循评测**：首选 [AlpacaEval](../sources/alpaca-eval-repo.md)，< $10、< 3 分钟、与人类高度一致。
- **人类偏好金标准**：[Chatbot Arena](../sources/fastchat-repo.md) 的 Elo 排名是当前最具公信力的 LLM 评测。
- **代码生成评测**：[HumanEval](../sources/human-eval-repo.md) 是最小必要起点，复杂场景可扩展至 SWE-bench。
- **检索 / RAG 评测**：[BEIR](../sources/beir-repo.md) 提供跨领域零样本检索基准。
- **AI 安全审计**：[Inspect AI](../sources/inspect-ai-repo.md) 提供政府级安全评测任务集。
- **Prompt 鲁棒性**：[PromptBench](../sources/promptbench-repo.md) 是唯一系统性评估对抗 prompt 攻击的框架。
- **低成本 rubric 评审替代 GPT-4**：[Prometheus](../sources/prometheus-repo.md) 提供开源细粒度评测模型。
- **官方分数复现 / 极简对比**：[simple-evals](../sources/simple-evals-repo.md) 提供 OpenAI 官方评测的透明参考实现。

## 相关页面

### 框架与工具详情
- [OpenCompass 评测平台](../sources/opencompass-repo.md)
- [lm-evaluation-harness 评测框架](../sources/lm-evaluation-harness-repo.md)
- [HELM 评测框架](../sources/helm-repo.md)
- [EvalScope 评测框架](../sources/evalscope-repo.md)
- [Inspect AI 评测框架](../sources/inspect-ai-repo.md)
- [PromptBench 评测库](../sources/promptbench-repo.md)
- [AlpacaEval 自动评估器](../sources/alpaca-eval-repo.md)
- [FastChat / Chatbot Arena 平台](../sources/fastchat-repo.md)
- [HumanEval 代码评测](../sources/human-eval-repo.md)
- [BEIR 信息检索评测](../sources/beir-repo.md)
- [simple-evals 轻量评测](../sources/simple-evals-repo.md)
- [Prometheus 评测模型](../sources/prometheus-repo.md)

### 相关概念与实体
- [LLM-as-Judge](llm-as-judge.md)
- [LMSYS / Stanford](../entities/lmsys.md)
- [OpenAI](../entities/openai.md)
- [Google](../entities/google.md)
