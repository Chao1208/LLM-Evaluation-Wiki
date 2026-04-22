---
title: "HELM 评测框架（GitHub 仓库）"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [评测框架, 开源工具, Stanford, CRFM, 全面评测, 多模态评测]
sources: [raw/github/helm/README.md]
---

## 概述

HELM（Holistic Evaluation of Language Models）是由 Stanford 大学 CRFM（Center for Research on Foundation Models）开发的开源 Python 评测框架，旨在对基础模型（包括 LLM 和多模态模型）进行全面、可复现和透明的评估。项目 GitHub 地址为 `stanford-crfm/helm`，PyPI 包名为 `crfm-helm`。

HELM 最初作为一项大规模评测研究发表（即 [HELM 论文](../sources/helm.md)，发表于 TMLR 2023），后续发展为一个通用的评测框架，已扩展到视觉语言模型（VHELM）、文本到图像模型（HEIM）、音频语言模型、医疗领域（MedHELM）等多个方向。

## 关键特性

1. **标准化数据集和基准**：支持 MMLU-Pro、GPQA、IFEval、WildBench 等主流 benchmark，所有数据集采用统一格式
2. **统一模型接口**：通过统一接口访问来自多家供应商的模型（OpenAI、Anthropic Claude、Google Gemini 等）
3. **超越准确率的多维指标**：衡量效率（efficiency）、偏见（bias）、毒性（toxicity）等多个维度
4. **Web UI 检视**：提供 Web 界面逐条检查 prompt 和 response
5. **Web 排行榜**：跨模型和跨 benchmark 比较结果的在线排行榜
6. **可复现性**：框架支持复现所有已发表的评测结果

## 官方排行榜

HELM 维护多个方向的官方排行榜：

### 旗舰排行榜
- **HELM Capabilities**：核心能力评测
- **HELM Safety**：安全性评测
- **VHELM**（Holistic Evaluation of Vision-Language Models）：视觉语言模型评测

### 领域/维度排行榜
覆盖医疗、金融、多语言、世界知识、法规合规等多种领域和维度。详见 [HELM 官网](https://crfm.stanford.edu/helm/)。

## 相关论文与扩展

HELM 框架已被用于多项评测研究：

| 研究 | 关注领域 |
|------|---------|
| Holistic Evaluation of Language Models | 42 场景 x 7 指标类别，30 模型全面评测（TMLR 2023） |
| VHELM | 视觉语言模型全面评测 |
| HEIM | 文本到图像模型评测 |
| Image2Struct | 视觉语言模型结构提取 benchmark |
| Enterprise Benchmarks | 企业级 LLM 评测 |
| The Mighty ToRR | 表格推理与鲁棒性 benchmark |
| Efficient Benchmarking | 高效评测方法 |
| Reliable and Efficient Amortized Model-based Evaluation | 可靠高效的分摊模型评估 |
| MedHELM | 医疗任务全面评测（Nature Medicine 2025） |
| Holistic Evaluation of Audio-Language Models | 音频语言模型评测 |

## 技术架构

- **安装**：`pip install crfm-helm`
- **核心命令**：
  - `helm-run`：运行评测（指定 run-entries、suite、max-eval-instances）
  - `helm-summarize`：汇总评测结果
  - `helm-server`：启动 Web 结果展示服务
- **评测流程**：运行 benchmark → 汇总结果 → Web 展示，三步完成
- **架构特点**：评测场景（scenarios）、适配器（adapters）、指标（metrics）解耦设计

## 与评测相关的启示

1. **"全面性"理念的标杆**：HELM 的核心哲学是"全面评测"——不仅关注准确率，还系统性地衡量效率、偏见、毒性、鲁棒性等多个维度。这一理念对构建评测体系具有重要指导意义。与纯粹追求排行榜分数的评测不同，HELM 倡导多维度、多场景的综合评估。
2. **从论文到平台的演进**：HELM 从一篇评测论文（[HELM 论文](../sources/helm.md)）发展为一个可扩展的评测平台，说明评测研究的可持续性需要工程化的框架支撑。
3. **领域垂直扩展模式**：通过 MedHELM（医疗）、Enterprise Benchmarks（企业）、ToRR（表格推理）等扩展，展示了通用评测框架向垂直领域延伸的路径。这种模式对构建行业特定评测体系具有参考价值。
4. **多模态评测整合**：VHELM（视觉语言）、HEIM（文本到图像）、音频语言模型评测的集成，反映了基础模型评测从纯文本向多模态扩展的趋势。
5. **透明可检视**：Web UI 支持逐条检视 prompt 和 response，这种透明度对评测结果的可信度至关重要，值得在自建评测系统中借鉴。
6. **Stanford 学术公信力**：作为 Stanford CRFM 的旗舰项目，HELM 在学术界具有较高的公信力和引用率，其评测方法论和结果被广泛认可。

## 相关页面

- [HELM 全面评测（论文摘要）](../sources/helm.md)
- [MMLU 基准](../sources/mmlu.md)
- [IFEval 指令遵循](../sources/ifeval.md)
- [LLM-as-Judge 评测方法](../concepts/llm-as-judge.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [OpenCompass 评测平台](../sources/opencompass-repo.md)
- [lm-evaluation-harness 评测框架](../sources/lm-evaluation-harness-repo.md)
- [AlpacaEval 自动评估器](../sources/alpaca-eval-repo.md)
