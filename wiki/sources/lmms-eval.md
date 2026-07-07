---
title: "LMMs-Eval: Reality Check on the Evaluation of Large Multimodal Models"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [multimodal-evaluation, vlm, benchmark, evaluation-framework, data-contamination, livebench]
sources: [lmms-eval.pdf]
---

## 概述

LMMs-Eval 是由 NTU S-Lab（新加坡南洋理工大学）LMMs-Lab 团队提出的统一多模态模型评测框架，旨在为大型多模态模型（Large Multimodal Models, LMMs）提供标准化、可复现的评测基础设施。论文的核心贡献是提出并分析了 **Evaluation Trilemma（评测三难困境）**：wide-coverage（广覆盖）、low-cost（低成本）和 zero-contamination（零污染）三者无法同时实现，并提供了三个互补工具来平衡这一 trade-off：

1. **LMMs-Eval**：统一评测套件，覆盖 50+ 任务、10+ 模型（约 30 个变体）
2. **LMMs-Eval Lite**：精简评测集，通过 coreset selection 在保持排名一致性的同时将评测时间缩短约 9600 倍
3. **LiveBench**：动态评测基准，从新闻网站和论坛持续采集最新内容构建问答对，防止数据污染

论文发表于 arXiv:2407.12772（v2, 2025年5月），正式发表于顶级会议。

## 关键要点

### Evaluation Trilemma（评测三难困境）

论文的核心理论贡献是识别并命名了 LMM 评测中的不可能三角：

- **Wide Coverage（广覆盖）**：评测需覆盖模型能力的多个维度（视觉理解、OCR、推理、对话等）
- **Low Cost（低成本）**：全量评测 50+ 数据集在 8xA100 上耗时高达 1400+ 小时（72B 模型）
- **Zero Contamination（零污染）**：模型训练数据中可能包含 benchmark 数据，导致分数虚高

三者不可兼得：广覆盖意味着高成本；静态 benchmark 覆盖广但易被污染；人类 arena（如 LMSys）防污染但收集昂贵。

### LMMs-Eval: 统一评测框架

设计理念借鉴 lm-evaluation-harness，核心特性：

1. **One-command evaluation**：单一命令即可在多个模型和多个数据集上运行评测
2. **Standardized evaluation**：统一了 PPL-based 和 generation-based 两种评测方式的接口
3. **Detailed logging**：自动记录评测设置、模型输出和分数细项，确保可复现

已支持模型包括：LLaVA 全系列、InternVL、Qwen-VL、GPT-4V、Gemini、Claude 等 10+ 模型族。

代表性评测结果（部分模型）：

| 模型 | 参数 | AI2D | ChartQA | DocVQA | MMMU | RealworldQA |
|------|------|------|---------|--------|------|-------------|
| LLaVA-OV-72B(SI) | 72B | 85.1 | 84.9 | 93.5 | 57.4 | 73.8 |
| InternVL-1.5 | 26B | 79.0 | 83.8 | 92.4 | 43.1 | 65.0 |
| LLaVA-NeXT-72B | 72B | 77.4 | 77.0 | 84.4 | 46.7 | 62.0 |
| Qwen-VL-Chat | 7B | 45.9 | 60.1 | 66.3 | 27.7 | 1.7 |

### LMMs-Eval Lite: 高效精简评测

通过 coreset selection 方法构建精简版评测集：

- **方法**：使用 CLIP + BGE-M3 的拼接 embedding 进行 k-Center 聚类，选出代表性子集
- **效果**：从 90,223 样本精简至 9,134 样本（约 10%），评测时间缩短 9600 倍
- **保真度**：在 Flickr30k、AI2D、TextVQA 等数据集上，lite 版与 full 版的相关系数达 0.87-0.99

| 数据集 | Quire 方法 | k-means | Lite (Ours) |
|--------|-----------|---------|-------------|
| Flickr30k | 0.97 | 0.79 | 0.91 |
| AI2D | 0.45 | 0.87 | **0.98** |
| SeedBench | 0.27 | 0.87 | **0.87** |
| TextVQA | 0.99 | 0.98 | **0.99** |

### 数据污染分析

论文首次系统性地对 LMM 的多模态数据污染进行了定量分析：

- **图像污染检测**：使用 SEED-tokenizer 将图像编码为 32 token 的 1D 序列，构建 8-gram 查找表检测重复
- **文本污染检测**：使用 8 n-gram 匹配（排除高频 meaningless n-grams）

关键发现：
- ChartQA 图像重叠率 68.64%、文本重叠率 26.52%（与 LLaVA-NeXT 训练数据）
- DocVQA 图像重叠率 36.08%
- COCO2014 图像重叠率 46.05%、文本重叠率 22.19%
- 三类污染模式：**完全相同图像**、**相似图像**、**相似问题结构**

### LiveBench: 动态防污染评测

LiveBench 从 60+ 新闻网站（BBC、CNN、WSJ、新华社等）和论坛持续采集内容，自动生成评测问答对：

**数据管道**：
1. 截取网页截图 → Claude-3.5-Sonnet 进行 OCR + 图像描述 + 时效性信息提取
2. Quiz Model 生成 4 类问题（基于 Bloom's Taxonomy）：Concrete Recognition、Real-world Application、Analytical Understanding、Divergent Thinking & Creation
3. Checker & Finalizer 模型验证和润色 QA 对
4. QA Scorer 对问题质量评分（Authenticity、Logical Coherence、Clarity and Precision，1-10 分）
5. 每月约 100-300 条高质量问答进入最终题库

**评测结果（LiveBench-2024-09）**：

| 模型 | Overall | Recognition | Analysis | Thinking | Realworld |
|------|---------|-------------|----------|----------|-----------|
| GPT-4o | **92.0** | 91.7 | **93.8** | 94.8 | **87.6** |
| GPT-4o-mini | 91.9 | **94.6** | 93.4 | **95.3** | 84.3 |
| Claude-3.5-sonnet | 90.3 | 94.6 | 93.4 | 95.3 | 85.8 |
| Gemini-1.5-Pro | 84.5 | 85.4 | 83.8 | 88.6 | 80.1 |
| Qwen2-VL-72B | 85.9 | 86.7 | 88.8 | 89.0 | 79.2 |
| InternVL2-8B | 69.6 | 65.6 | 74.8 | 77.5 | 60.4 |

关键发现：GPT-4o 领先所有模型；开源模型与商业模型差距仍然显著。

## 重要发现

1. **开源模型在 LiveBench 上远落后于 GPT-4V**：静态 benchmark 上部分开源模型已接近 GPT-4V，但在防污染的动态评测上差距明显，说明部分性能提升可能源自数据污染而非真实能力提升。
2. **Evaluation Trilemma 的普遍性**：该框架可推广到所有模型评测场景（不仅限于 LMM），是评测方法论的重要理论贡献。
3. **Coreset Selection 的有效性**：通过 embedding + k-Center 算法可以在保持评测排名一致性的前提下将数据量压缩至 10%，为快速迭代实验提供了实用工具。
4. **多模态污染比想象中严重**：ChartQA、VQAv2、COCO2014 等常用 benchmark 已确认被包含在 LLaVA-NeXT 训练数据中，影响评测结果的可信度。

## 问题与思考

1. **LiveBench 的质量 vs 规模 trade-off**：每月仅 100-300 条高质量 QA 进入题库，覆盖面有限。论文承认 QA 质量可能低于人工标注。
2. **Coreset Selection 的泛化性**：当前方法使用 CLIP + BGE 进行 embedding，不同模型对数据点难度的感知可能不同，同一 lite set 对所有模型是否同样有效？
3. **污染检测的局限性**：基于 n-gram 和 image token 的方法可能遗漏语义相似但表面不同的污染形式（如 paraphrase）。
4. **与 HealRub 的关联**：LMMs-Eval 的标准化评测管道思路可参考用于 HealRub 的自动化评测流程；LiveBench 的动态题库思路可启发 rubric evaluation 的防作弊机制。
5. **框架开源**：LMMs-Eval 代码和 LiveBench 排行榜均已开源，可作为 LMM 评测的基础设施直接使用（GitHub: EvolvingLMMs-Lab/lmms-eval）。

## 相关页面

- [多模态评测](../concepts/multimodal-evaluation.md)
- [lm-evaluation-harness](../entities/lm-evaluation-harness.md)
- [数据污染](../concepts/data-contamination.md)
- [LLaVA](../entities/llava.md)
