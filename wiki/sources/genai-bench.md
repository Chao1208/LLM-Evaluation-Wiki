---
title: "GenAI Arena: An Open Evaluation Platform for Generative Models"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [generative-ai, arena, text-to-image, text-to-video, image-editing, human-evaluation, elo-rating, mllm-judge]
sources: [genai-bench.pdf]
---

## 概述

GenAI Arena 是由 University of Waterloo 的 TIGER Lab 提出的开放评测平台，用于通过社区投票对多模态生成式 AI 模型进行排名。该平台覆盖三类生成任务：文本到图像生成（text-to-image）、文本到视频生成（text-to-video）和文本引导的图像编辑（text-guided image editing），共集成 35 个开源生成模型。平台自 2024 年 2 月 11 日运行至论文截止日期（2024 年 10 月 24 日），累计收集超过 9,000 票用户投票。

论文同时发布了 GenAI-Bench，将用户投票数据清洗后作为 preference data，用于评测 MLLM（Multimodal Large Language Model）的判断能力。核心发现是：即便最好的 MLLM（GPT-4o）在判断生成内容质量时，与人类偏好的一致率也仅为 **49.19%**，开源模型表现更差。

论文发表于 NeurIPS 2024 Datasets and Benchmarks Track（arXiv:2406.04485）。

## 关键要点

### 平台设计

GenAI-Arena 的核心设计原则：

1. **匿名投票**：用户输入 prompt，平台从两个匿名模型生成输出供并排比较。投票选项包括：A 更好、B 更好、平局（both good）、都差（both bad）。
2. **标准化推理**：统一所有模型的推理超参数和 prompt 格式，确保公平比较。
3. **GenAI-Museum**：预计算大量 prompt-output 对，实现即时比较和投票，降低 GPU 等待时间。

### 模型覆盖

| 任务 | 模型数量 | 代表模型 |
|------|---------|---------|
| Text-to-Image | 17 | OpenJourney, SDXL, PixArt-alpha, FLUX.1-dev, Playground V2.5, Kolors, HunyuanDiT |
| Image Editing | 9 | Pix2PixZero, SDEdit, InfEdit, InstructPix2Pix, MagicBrush, CosXLEdit |
| Text-to-Video | 11 | AnimateDiff, ModelScope, LaVie, StableVideoDiffusion, VideoCrafter2, OpenSora, CogVideoX-2B |

### Elo 排名系统

使用 Bradley-Terry 模型进行 Elo 评分估计（logistic regression + MLE），相较于直接 Elo 计算更加稳定，不受比赛顺序影响。

**Text-to-Image 排行榜（Top-5）**：

| 排名 | 模型 | Elo | 95% CI |
|------|------|-----|--------|
| 1 | Playground V2.5 | 1122 | +19/-20 |
| 2 | FLUX.1-dev | 1114 | +45/-42 |
| 3 | FLUX.1-schnell | 1085 | +43/-46 |
| 4 | Playground V2 | 1072 | +18/-22 |
| 5 | Kolors | 1069 | +32/-39 |

**Image Editing 排行榜 Top-1**: MagicBrush (Elo 1108)
**Text-to-Video 排行榜 Top-1**: StableVideoDiffusion (Elo 1148)

### 投票质量评估

对 350 条抽样投票进行专家审核：
- 86.57% 为有效投票（去除 NSFW 和技术问题）
- 有效投票中 76.24% 为"明确合理"，16.83% 为"模糊但合理"，6.93% 为"错误"
- 总计 93.07% 的投票可视为合理

### GenAI-Bench: MLLM 判断能力评测

GenAI-Bench 将清洗后的 arena 投票数据作为 ground truth，测试 MLLM 的视觉内容判断能力：

| 模型 | Image Generation | Image Editing | Video Generation | Average |
|------|-----------------|---------------|------------------|---------|
| GPT-4o | **45.59** | 53.54 | **48.46** | **49.19** |
| Gemini-1.5-Pro | 44.67 | **55.93** | 46.21 | 48.94 |
| LLaVA-1.5-7B | 37.00 | 26.12 | 30.40 | 31.17 |
| Idefics2 | 42.25 | 27.31 | 16.46 | 28.67 |
| MiniCPM-V-2.5 | 37.81 | 25.24 | 6.55 | 23.20 |
| Random | 25.36 | 25.90 | 25.16 | 25.47 |

MLLM 被要求输出 4 种标签之一：[[A>B]]、[[B>A]]、[[A=B=Good]]、[[A=B=Bad]]，与人类投票进行 exact match 计算准确率。

### 关键发现

1. **GPT-4o 仅 49.19% 准确率**：在评估生成内容质量方面，最强 MLLM 的准确率也仅略高于随机水平（25%），说明 MLLM 在复杂视觉推理场景中仍远不及人类判断。
2. **开源模型严重落后**：开源 MLLM 如 CogVLM（17.98%）、VideoLLaVA（21.47%）等准确率接近甚至低于随机基线，表明指令遵循和视觉推理能力的严重不足。
3. **Elo 系统的偏差**：winning fraction heatmap 揭示 Elo 评分可能因"easy games"与"hard games"的不均衡采样而产生偏差。
4. **无统治性模型**：所有 top 模型的平均胜率均未超过 80%，说明当前生成模型领域尚未出现"ChatGPT moment"级别的突破。

## 重要发现

1. **人类偏好评测 vs 自动指标的巨大鸿沟**：FID、CLIP、SSIM 等自动指标无法捕获用户对生成质量的主观满意度，而 MLLM 作为自动评测器的可靠性也极低（~49%），这凸显了生成式 AI 评测领域的核心困难。
2. **训练数据重要性**：Playground V2.5 使用私有数据集训练，与 SDXL 同架构但排名差距巨大（1122 vs 1020），表明训练数据质量可能比模型架构更关键。
3. **社区驱动评测的可行性**：93% 的投票质量可接受，证明开放平台收集的偏好数据具有可靠性，可作为 reward model 和 MLLM alignment 的训练信号。
4. **NSFW 过滤的必要性**：原始投票中大量 prompt 涉及不当内容（T2I 任务中 85.6% 被过滤的内容为性相关），需要 Llama Guard 等安全过滤器。

## 问题与思考

1. **49% 准确率的含义**：4 选项 exact match 的随机基线为 25%，GPT-4o 达到 49% 意味着其判断能力确实显著高于随机，但仍有巨大改进空间。这也说明将 MLLM 直接用作视觉生成质量的 judge 存在可靠性问题。
2. **与 LLM-as-Judge 范式的关系**：GenAI-Bench 本质上测试的是 MLLM 在 pairwise comparison 范式下的判断一致性，这与 HealRub 中 LLM-as-Judge 的评分可靠性问题高度相关。
3. **Elo 系统的局限**：在有限投票量下，Elo 估计的置信区间较大（多数模型 CI 在 +/-20-50），且对战分配不均匀可能引入系统偏差。
4. **GenAI-Bench prompt 来源的局限**：用户自发输入的 prompt 可能偏向特定类型（如创意艺术），不一定代表生成模型的真实应用分布。
5. **可扩展性思考**：该平台模式（arena + preference data + judge benchmark）可推广到其他生成任务（如音频生成、3D 生成），也为构建 reward model 提供了数据基础。

## 相关页面

- [多模态评测](../concepts/multimodal-evaluation.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [Chatbot Arena](../entities/chatbot-arena.md)
