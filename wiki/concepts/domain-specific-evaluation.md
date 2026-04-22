---
title: "领域专项评测"
type: concept
created: 2026-04-22
updated: 2026-04-22
tags: [领域评测, 医疗, 金融, 法律, 翻译, 长上下文, 创作]
sources: []
---

## 概述

通用 benchmark（如 [MMLU](../benchmarks/mmlu-benchmark.md)、HellaSwag）能衡量 LLM 的一般推理和知识水平，但无法反映模型在特定专业领域中的真实表现。领域专项评测（Domain-Specific Evaluation）针对医疗、法律、金融、翻译、创作等垂直场景，设计专用数据集、评测指标和评估流程，以揭示通用模型在专业任务中的能力缺口。

领域评测之所以必要，原因包括：

- **专业知识门槛高**：医学诊断、法律合规等任务需要深厚的领域知识，通用评测无法覆盖
- **错误后果严重**：医疗和法律场景中的模型错误可能直接危害用户
- **评测指标不同**：金融预测需要回报率指标，翻译需要 BLEU/ChrF++，创作需要人工审美评估，通用 accuracy 指标不适用
- **领域适配差距显著**：研究反复表明通用 LLM 在垂直领域任务上表现远不如领域专家

## 领域评测全景

| 领域 | 基准/研究 | 评测方式 | 数据规模 | 核心发现 |
|------|----------|---------|---------|---------|
| 医疗 | [HealthBench](../benchmarks/healthbench.md) | Rubric-Based，GPT-4.1 作 Judge | 5,000 对话，48,562 criteria | 医师编写高风险场景 + 对抗性测试 |
| 医疗 | [ChatGPT 医学准确性研究](../sources/chatgpt-medical-accuracy.md) | 33 位医师 Likert 量表评分 | 284 个医学问题 | 准确度中位数 5.5/6，但重复评估波动大 |
| 医疗 | [MIMIC-CDM 临床决策研究](../sources/llm-clinical-decision.md) | 与临床医生对照的诊断准确率 | 2,400 个临床案例 | LLM 诊断准确率 45-55%，医生 73-89% |
| 法律 | [BigLaw Bench](../benchmarks/biglaw-bench.md) | Rubric-Based，三层架构 | 16 种子任务 + 检索 + 工作流 | 覆盖交易与诉讼两大类法律实务 |
| 金融 | [CNFinBench](../benchmarks/cnfinbench.md) | 安全-有效性双轨 Rubric | 13,000+ 实例，15 个子任务 | 聚焦中国金融合规场景 |
| 金融 | [PIXIU/FLARE](../sources/pixiu-flare.md) | 多任务 NLP 评测 | 5 类金融 NLP 任务 | 通用 LLM 在金融任务上表现不佳，领域适配至关重要 |
| 翻译 | [GPT 翻译评测](../sources/gpt-mt-evaluation.md) | BLEU、ChrF++ 自动指标 | 多语言对，WMT 测试集 | GPT-4 接近商用翻译系统，低资源语言差距明显 |
| 创作 | [Dramatron](../sources/dramatron.md) | 专家人工评估 + 用户访谈 | 15 位专业编剧参与 | 层次化生成有效，原创性和深度仍不足 |
| 长上下文 | [长上下文扩展评测](../sources/effective-long-context.md) | Needle-in-a-Haystack + 长文档 QA | 4K-32K tokens 梯度测试 | "lost in the middle" 现象，中间位置信息易被忽略 |

## 医疗

医疗是领域专项评测中研究最为深入的方向，因其直接关系患者安全。

### HealthBench

[HealthBench](../benchmarks/healthbench.md) 由 [OpenAI](../entities/openai.md) 开发，包含 5,000 对话和 48,562 个评测 criteria。其特点是采用 [Rubric-Based 评测](rubric-based-evaluation.md) 方法，由医师团队编写高风险临床场景，包含对抗性红队测试和 34 条 Consensus Criteria。评分使用 GPT-4.1 作为 [LLM Judge](llm-as-judge.md)。

### ChatGPT 医学准确性研究

[Johnson 等人的研究](../sources/chatgpt-medical-accuracy.md) 邀请 33 位覆盖 17 个专科的执业医师，对 284 个医学问答进行评分。结果显示准确度中位数为 5.5/6（"几乎完全正确"），完整性达到满分。但间隔 8-17 天的重复评估显示答案质量波动显著，且不同专科表现存在差异。

### MIMIC-CDM 临床决策研究

[Hager 等人（Nature Medicine, 2024）](../sources/llm-clinical-decision.md) 构建了基于真实患者数据的 MIMIC-CDM 数据集（2,400 个临床案例），评估结果揭示了关键矛盾：**LLM 在医学问答上表现优秀，但在实际临床决策中显著落后于医生**。所有测试 LLM 的诊断准确率仅 45-55%，而临床医生达到 73-89%（P < 0.001）。值得注意的是，专门针对医学微调的模型（如 Clinical Camel、Meditron）并未显著优于通用模型。

**关键结论**：医学 QA 的高分不能等同于临床决策能力。评测设计需区分知识问答与实际应用场景。

## 法律与金融

### 法律：BigLaw Bench

[BigLaw Bench](../benchmarks/biglaw-bench.md) 由 [Harvey AI](../entities/harvey-ai.md) 开发，采用三层评测架构：Core（16 种法律子任务）、Workflows（复合型法律任务）、Retrieval（文档检索）。覆盖交易类（9 种）和诉讼类（7 种）任务，使用正分/负分 Rubric 评分机制，最终得分反映模型完成律师级工作成果的百分比。

### 金融：CNFinBench

[CNFinBench](../benchmarks/cnfinbench.md) 聚焦中国金融合规场景，包含 13,000+ 实例和 15 个子任务，采用安全-有效性双轨评测体系。

### 金融：PIXIU/FLARE

[PIXIU 框架](../sources/pixiu-flare.md) 提出了 FLARE（Financial Language Understanding and Prediction Evaluation）基准，涵盖情感分析、文本分类、信息抽取、文本生成、问答 5 类金融 NLP 任务。核心发现是通用 LLM 在金融任务上表现不佳，领域适配（domain adaptation）至关重要。配套的 FinMA 模型通过金融指令数据微调后表现显著提升。

## 翻译

[Jiao 等人（Microsoft, 2023）](../sources/gpt-mt-evaluation.md) 系统评估了 GPT 系列模型的机器翻译能力，使用 BLEU 和 ChrF++ 标准指标，对比 Google Translate、DeepL 等商用系统。

主要发现：
- GPT-4 在高资源语言对（如英-德、英-中）上接近甚至超越商用翻译系统
- ChatGPT（GPT-3.5）与专用翻译系统仍有差距，低资源语言尤为明显
- Prompt 设计对翻译质量有显著影响
- 自动指标（BLEU、ChrF++）无法完全反映翻译的流畅度和地道性，缺乏大规模人类评估是该研究的局限

## 创作

[Dramatron](../sources/dramatron.md)（CHI 2023）探索了 LLM 在创意写作中的应用，采用层次化故事生成（hierarchical story generation）流程，从 logline 逐步细化到完整剧本对白。

评估方法上，该工作采用混合方法论：自动指标（perplexity、多样性）+ 15 位专业编剧/剧作家的人工评估 + 半结构化访谈。多数专业创作者认为系统有助于激发创意，但 LLM 生成内容在原创性和叙事深度上仍与人类专业创作有明显差距。

创作领域的评测难点在于**缺乏客观标准**，高度依赖专家主观判断，评估成本高且难以规模化。

## 长上下文

[Meta 的研究](../sources/effective-long-context.md)（NAACL 2024）探索将 Llama 2 从 4K 扩展到 32K tokens，并设计了系统性的长上下文评测框架：

- **Needle-in-a-Haystack**：在长文本中检索特定信息片段
- **长文档问答**：NarrativeQA、QuALITY 等 benchmark
- **上下文长度梯度测试**：4K/8K/16K/32K 不同长度下的表现变化
- **短上下文保持验证**：确保长上下文能力不损害原有短上下文性能

核心发现是 "lost in the middle" 现象——模型对上下文中间位置的信息利用不足，这对依赖长文档的法律、金融等领域应用尤为关键。

## 共性挑战

跨领域评测面临若干共性问题：

- **领域专家标注成本高**：HealthBench 需要医师团队编写 criteria，BigLaw Bench 需要 18 人法律研究团队，Dramatron 需要 15 位专业编剧。高质量领域评测的构建成本远超通用 benchmark
- **安全关键性**：医疗和法律场景中，模型错误可能直接造成伤害。MIMIC-CDM 研究显示 LLM 诊断准确率仅约 50%，在高风险场景中部署需极度审慎
- **监管与合规要求**：金融领域受严格监管（CNFinBench 专注合规），医疗领域需符合临床指南（MIMIC-CDM 评测指南遵循率），法律领域需确保来源可靠性（BigLaw Bench 评分含 Source Reliability 维度）
- **评测指标碎片化**：各领域使用不同指标体系（医学用 Likert 量表 / 诊断准确率，翻译用 BLEU/ChrF++，创作用专家主观评估），跨领域对比困难
- **数据获取受限**：医疗数据涉及隐私（MIMIC 需授权），法律数据涉及保密（BigLaw Bench 完整数据集不公开），限制了独立复现
- **通用模型 vs 领域微调**：MIMIC-CDM 和 PIXIU 的研究均表明，简单的领域微调未必带来显著提升，领域适配策略仍需深入探索

## 相关页面

### 资料摘要
- [ChatGPT 医学准确性评估](../sources/chatgpt-medical-accuracy.md)
- [LLM 临床决策评估（MIMIC-CDM）](../sources/llm-clinical-decision.md)
- [PIXIU/FLARE 金融 NLP 评测](../sources/pixiu-flare.md)
- [GPT 机器翻译评测](../sources/gpt-mt-evaluation.md)
- [Dramatron 剧本协作写作](../sources/dramatron.md)
- [长上下文扩展评测](../sources/effective-long-context.md)

### 评测基准
- [HealthBench](../benchmarks/healthbench.md) — 医疗领域 Rubric-Based 评测
- [BigLaw Bench](../benchmarks/biglaw-bench.md) — 法律领域三层评测架构
- [CNFinBench](../benchmarks/cnfinbench.md) — 中国金融合规评测

### 相关概念
- [Rubric-Based 评测](rubric-based-evaluation.md)
- [LLM-as-Judge](llm-as-judge.md)
