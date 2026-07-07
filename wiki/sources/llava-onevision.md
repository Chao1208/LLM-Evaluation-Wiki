---
title: "LLaVA-OneVision: Easy Visual Task Transfer"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [多模态, VLM, 训练数据, 视觉语言模型, task-transfer, 视频理解]
sources: [llava-onevision.pdf]
---

## 概述

LLaVA-OneVision 是 LLaVA-NeXT 系列博客探索的集大成之作，由 ByteDance、S-Lab (NTU)、CUHK、HKUST 联合开发。该工作的核心贡献是提出了一个统一的开源多模态大模型（LMM），能够同时在单图像、多图像和视频三大视觉场景中推动开源模型的性能边界。其设计允许跨模态/场景的强迁移学习，产生了新的涌现能力（emerging capabilities），特别是通过图像到视频的任务迁移展示了强大的视频理解能力。

论文发表于 arXiv:2408.03326（2024年10月），模型架构采用 Qwen-2 作为 LLM backbone、SigLIP 作为 vision encoder、2-layer MLP 作为 projector。提供 0.5B、7B、72B 三种规模。

## 关键要点

### 训练数据细节

LLaVA-OneVision 采用课程学习（curriculum learning）策略，将训练分为多个阶段，数据规模逐步递增：

| 阶段 | 数据类型 | 样本量 | 训练模块 |
|------|---------|--------|---------|
| Stage-1: Language-Image Alignment | LCS（LAION-CC-SBU）| 558K | 仅 Projector |
| Stage-1.5: High-Quality Knowledge Learning | 高质量知识数据（Re-Captioned、OCR、中文）| 4M | 全模型 |
| Stage-2 (Single-Image): Visual Instruction Tuning | 单图像指令数据 | 3.2M | 全模型 |
| Stage-2 (OneVision): Mixed Scenario Training | 单图+多图+视频混合 | 1.6M | 全模型 |

**总计约 9.4M 样本**（558K + 4M + 3.2M + 1.6M）。

**Stage-1.5 高质量知识数据**（4M 样本，99.8% 为合成数据）：
- Re-Captioned Detailed Description Data：用 LLaVA-NeXT-34B 对 COCO118K、BLIP558K、CC3M 重新生成描述，共 3.5M
- Document / OCR Data：UReader + SynDOG EN/CN，共 1.1M
- Chinese and Language Data：使用 ShareGPT4V 图像 + GPT-4V Azure API 生成 92K 中文详细描述
- Evo-Instruct 数据集：143K 样本，平衡语言理解能力

**Stage-2 单图像指令数据**（3.2M 样本）按三层层级组织：
- Vision Input：单图像、多图像、视频
- Language Instruction：5 类（General QA、General OCR、Doc/Chart/Screen、Math Reasoning、Language）
- Language Response：自由形式与固定形式

**OneVision 混合数据**（1.6M 样本）：
- 560K 多图像数据（来自 LLaVA-NeXT-Interleave）
- 350K 视频数据（本项目收集）
- 800K 单图像数据（从前一阶段采样高质量子集）

### 方法论

**架构设计**：
- LLM：Qwen-2（0.5B / 7B / 72B）
- Vision Encoder：SigLIP（ViT-SO400M/14@384）
- Projector：2-layer MLP
- 视觉表示：Higher AnyRes with Bilinear Interpolation

**视觉表示策略**（跨场景 token 分配）：
- Single-Image：(1+9) x 729 = 7290 tokens（最大）
- Multi-Image：12 x 729 = 8748 tokens
- Video：32 x 196 = 6272 tokens
- 设计原则：各场景最大 token 数相近，便于跨场景能力迁移

**训练策略**：
- 课程学习：分辨率和 token 数随阶段递增
- Stage-1 仅训练 Projector，后续阶段训练全模型
- Vision encoder 学习率为 LLM 的 1/5
- 单图像训练先行，OneVision 训练在其基础上扩展到多图像和视频

### 关键发现

**性能表现**（LLaVA-OneVision-72B）：
- 单图像：DocVQA 91.3%、ChartQA 83.7%、MMBench 85.9%、MMMU 56.8%
- 多图像：在 LLaVA-Interleave、MuirBench、BLINK 等任务上超越 GPT-4V
- 视频：ActivityNetQA 62.3%、VideoMME 66.2%、EgoSchema 62.0%
- 整体性能介于 GPT-4V 和 GPT-4o 之间，在部分 benchmark 上超越两者

**涌现能力**（9 个跨场景迁移案例）：
- S1: 图表联合理解（单图→多图）
- S2: GUI 多模态 Agent（单图+多图）
- S3: Set-of-Mark Prompting（首个开源 LMM 具备此能力）
- S4: 图像到视频编辑指令生成
- S5: 视频对比分析
- S6: 多摄像头自动驾驶视频理解
- S7: 组合子视频理解
- S8: 视频中的视觉提示理解
- S9: 视频理解中的图像引用

## 重要发现

1. **单模型统一三场景**：LLaVA-OneVision 是首个能同时在单图像、多图像和视频三个场景中达到 SOTA 的开源模型
2. **Task Transfer 是关键设计**：通过在图像上学习的能力可以零样本迁移到视频场景（得益于 AnyRes 将任何视觉输入视为图像序列的设计）
3. **数据质量重于数量**：99.8% 的高质量知识学习数据为合成数据，强调数据质量和计算效率
4. **课程学习有效**：渐进式训练（对齐→知识→指令→多场景）比一次性混合训练更高效
5. **OneVision 阶段的重要性**：在单图像模型基础上追加 1.6M 混合数据训练，是赋予多图像和视频能力的最简单高效方式
6. **合成数据的趋势**：高成本和版权限制使得大规模高质量真实数据难以获取，合成数据是可扩展的替代方案

## 问题与思考

1. **数据来源与多样性**：训练数据来自大量公开数据集（Cauldron、Cambrian 等数据集合集），如何确保数据多样性和无偏性？
2. **视频能力的上限**：视频仅使用 32 帧 x 196 tokens 的表示，对长视频的理解能力是否受限？
3. **OneVision 训练的数据配比**：560K 多图 + 350K 视频 + 800K 单图的比例是如何确定的？是否有消融实验支持？
4. **涌现能力的可靠性**：文中展示的 9 个涌现能力主要是定性示例，缺乏系统性的定量评估
5. **与 Cambrian-1 的比较**：两者在数据收集上有相似之处（均强调大规模高质量指令数据），但架构选择不同（LLaVA-OV 用单一 SigLIP，Cambrian-1 用多编码器 + SVA）
6. **Scale 的影响**：72B 模型的优势在复杂推理（如 EgoSchema）上更明显，暗示 LLM 规模对高级推理仍是瓶颈

## 相关页面

- [ShareGPT4V](../sources/sharegpt4v.md) - LLaVA-OneVision 使用了 ShareGPT4V 的图像和数据
- [Cambrian-1](../sources/cambrian-1.md) - 同期工作，采用不同的视觉编码器策略
