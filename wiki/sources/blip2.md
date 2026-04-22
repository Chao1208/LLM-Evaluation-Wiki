---
title: "BLIP-2: Bootstrapping Language-Image Pre-training with Frozen Image Encoders and Large Language Models"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [多模态, 视觉语言预训练, Q-Former, VQA, 图文检索, Salesforce]
sources: [2301.12597-blip2.pdf]
---

## 概述

BLIP-2（Salesforce Research, ICML 2023）提出了一种通用且高效的视觉-语言预训练方法，通过轻量级 **Querying Transformer（Q-Former）** 桥接冻结的图像编码器和冻结的大语言模型，以极少的可训练参数（仅 188M）实现了多项视觉-语言任务的 SOTA。

核心创新在于两阶段预训练策略：
1. **表示学习阶段**：从冻结的图像编码器引导视觉-语言表示学习
2. **生成学习阶段**：从冻结的 LLM 引导视觉到语言的生成学习

## 关键发现

1. **参数效率极高**：BLIP-2 在 VQAv2 上以 54 倍少的可训练参数超越 Flamingo80B 8.7%
2. **更强的图像编码器或更强的 LLM 都能带来更好性能**——这验证了 BLIP-2 作为通用视觉-语言预训练方法的泛化性
3. **零样本图像到文本生成**：展示了跟随自然语言指令的涌现能力
4. 冻结策略有效避免了灾难性遗忘，同时大幅降低计算成本

## 技术细节

### Q-Former 架构

Q-Former 是一个轻量级 Transformer，包含：
- 一组可学习的 query 向量（32 个，每个 768 维）
- 共享自注意力层的双路径设计：图像 Transformer + 文本 Transformer
- 总计 138M 参数（初始化自 BERT_base）

### 第一阶段：视觉-语言表示学习

三个联合优化目标：
- **ITC（Image-Text Contrastive）**：对齐图像和文本表示
- **ITM（Image-Text Matching）**：细粒度图文匹配（二分类）
- **ITG（Image-grounded Text Generation）**：基于图像生成文本

关键设计：三个目标共享 Q-Former 参数，但使用不同的注意力掩码策略控制 query 与文本的交互。

### 第二阶段：视觉到语言生成学习

- 将 Q-Former 输出通过全连接层投射到 LLM 输入维度
- 投射后的 query 嵌入作为 **soft visual prompts** 输入 LLM
- 支持两种 LLM 架构：decoder-based（OPT）和 encoder-decoder-based（FlanT5）

### 预训练数据

129M 图像，来源包括 COCO、Visual Genome、CC3M、CC12M、SBU、LAION400M，使用 CapFilt 生成合成 caption。

## 评测表现

| 任务 | 基准 | BLIP-2 最佳 | 对比 |
|------|------|-----------|------|
| VQA（零样本） | VQAv2 | 65.0% | Flamingo80B 56.3% |
| 图像描述（零样本） | NoCaps overall CIDEr | 121.6 | Flamingo 112.2（非零样本） |
| 图像描述（微调） | COCO Karpathy B@4 | 145.8 | BLIP 136.7 |
| 图文检索 | Flickr30K TR@1 | 97.6 | BLIP 97.2 |
| VQA（微调） | VQAv2 | 82.19/82.30 | 前 SOTA ~82.00 |

## 重要数据

- 预训练硬件：单台 16-A100(40G)，第一阶段 <6 天，第二阶段 <3 天
- FP16 预训练无性能损失
- 在 ScienceQA 上与 LLaVA 互补（GPT-4 complement 方案达 92.53%）

## 局限性

- 缺乏 in-context learning 能力（预训练数据每样本仅一个图文对）
- 图像到文本生成可能产生不准确内容（继承 LLM 的幻觉问题）
- 冻结 LLM 导致可能生成有害内容

## 与评测相关的启示

- 为多模态 LLM 评测（如 [MME](../sources/mme.md)、[MMBench](../sources/mmbench.md)）提供了重要的基线模型
- Q-Former 的模块化设计思想影响了后续多模态评测框架的设计
- 在 [LLaVA](../sources/llava.md) 论文中作为重要对比基线

## 相关页面

- [LLaVA: Visual Instruction Tuning](../sources/llava.md)
- [MME 多模态评测](../sources/mme.md)
- [MMBench](../sources/mmbench.md)
- [MM-Vet](../sources/mm-vet.md)
- [MathVista](../sources/mathvista.md)
- [LVLM-EHub](../sources/lvlm-ehub.md)
- [Meta](../entities/meta.md)

（来源：[2301.12597-blip2.pdf](../../raw/papers/2301.12597-blip2.pdf)）
