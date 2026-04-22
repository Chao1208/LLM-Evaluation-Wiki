---
title: "LLaVA: Visual Instruction Tuning"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [多模态, 视觉指令微调, LLaVA, 指令遵循, GPT-4数据生成, NeurIPS]
sources: [2304.08485-llava.pdf]
---

## 概述

LLaVA（Large Language and Vision Assistant，NeurIPS 2023）是首个将**指令微调（instruction tuning）**从纯文本扩展到**多模态图文领域**的工作。由 UW-Madison、Microsoft Research 和 Columbia University 联合提出。

核心贡献：
1. 提出 **visual instruction tuning** 概念，用 GPT-4 自动生成多模态指令遵循数据
2. 构建端到端的大型多模态模型（LMM），连接视觉编码器和 LLM
3. 提出两个评测基准：**LLaVA-Bench (COCO)** 和 **LLaVA-Bench (In-the-Wild)**
4. 开源数据、模型和代码

## 关键发现

1. **视觉指令微调有效**：instruction tuning 显著提升模型跟随指令的能力（提升超过 50 分）
2. **GPT-4 生成的数据质量高于 ChatGPT**：尤其在空间推理等方面
3. **三种数据类型互补**：conversation + detailed description + complex reasoning 三者结合效果最佳（85.1%）
4. **模型规模重要**：13B > 7B（差 1.08%），但预训练阶段更关键（跳过则降 5.11%）
5. **GPT-4 互补方案**：LLaVA + GPT-4(judge) 在 ScienceQA 上达到 92.53% SOTA

## 技术细节

### 架构

简洁的三模块设计：
- **视觉编码器**：CLIP ViT-L/14（冻结）
- **投射层**：单层线性矩阵 W，将视觉特征 Z_v 映射到语言嵌入空间 H_v = W · Z_v
- **语言模型**：Vicuna（基于 LLaMA 的指令微调模型）

### 数据生成管线

利用 GPT-4（纯文本）基于 COCO 图像的 caption 和 bounding box 符号表示，生成三类指令数据：
- **Conversation（对话）**：58K，多轮视觉问答
- **Detailed Description（详细描述）**：23K，图像全面描述
- **Complex Reasoning（复杂推理）**：77K，需要逻辑推理的问答

总计 **158K** 条指令遵循样本。

### 两阶段训练

| 阶段 | 数据 | 可训练参数 | 目标 |
|------|------|----------|------|
| Stage 1: 特征对齐预训练 | CC-595K 图文对 | 仅投射层 W | 对齐视觉-语言特征 |
| Stage 2: 端到端微调 | 158K 指令数据 | W + LLM 全参数 | 指令遵循能力 |

训练配置：8× A100，Stage 1 用 1 epoch，Stage 2 用 3 epochs。

## 评测表现

### LLaVA-Bench (In-the-Wild)

60 张多样化图像（室内外、绘画、模因等），GPT-4 作为评审：

| 模型 | Conversation | Detail | Complex | 总分 |
|------|-------------|--------|---------|------|
| LLaVA | 57.3±1.9 | 52.5±6.3 | 81.7±1.8 | 67.3±2.0 |
| BLIP-2 | 54.6±1.4 | 29.1±1.2 | 32.9±0.7 | 38.1±1.0 |
| OpenFlamingo | 19.3±0.5 | 19.0±0.5 | 19.4±0.7 | 19.1±0.4 |

LLaVA 比 BLIP-2 高 +29%，比 OpenFlamingo 高 +48%。

### ScienceQA

| 方法 | 准确率 |
|------|--------|
| LLaVA | 90.92% |
| LLaVA + GPT-4 (complement) | 90.97% |
| LLaVA + GPT-4 (judge) | **92.53%** |
| MM-CoT_Large | 91.68% |
| GPT-3.5 w/ CoT | 75.17% |

## 评测方法论贡献

1. **GPT-4 作为多模态评审**：提出用 GPT-4 评估生成质量的量化方法，计算相对于参考答案的分数
2. **LLaVA-Bench**：两个新的指令遵循评测基准，后续被广泛采用
3. **视觉指令遵循能力的系统评测框架**：conversation / description / reasoning 三维度

## 与评测相关的启示

- 开创了多模态模型指令遵循评测的范式
- LLaVA-Bench 成为后续 VLM 评测的重要基线（被 [MM-Vet](../sources/mm-vet.md)、[MMBench](../sources/mmbench.md) 等引用）
- GPT-4 作为多模态评审的方法直接影响了 [LLM-as-Judge](../concepts/llm-as-judge.md) 在多模态领域的扩展

## 相关页面

- [BLIP-2](../sources/blip2.md)
- [MM-Vet](../sources/mm-vet.md)
- [MMBench](../sources/mmbench.md)
- [MathVista](../sources/mathvista.md)
- [LLM-as-Judge 方法论](../concepts/llm-as-judge.md)
- [GPT-4 Technical Report](../sources/gpt4-technical-report.md)
- [Meta](../entities/meta.md)

（来源：[2304.08485-llava.pdf](../../raw/papers/2304.08485-llava.pdf)）
