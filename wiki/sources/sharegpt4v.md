---
title: "ShareGPT4V: Improving Large Multi-Modal Models with Better Captions"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [多模态, VLM, 合成数据, caption, 数据增强, 预训练]
sources: [sharegpt4v.pdf]
---

## 概述

ShareGPT4V 是由中国科学技术大学和上海人工智能实验室联合发表的工作（arXiv:2311.12793，2023年11月），核心主张是：当前大多数多模态大模型（LMM）使用的图文对质量低下（如简短的 alt-text），严重制约了视觉与语言模态之间的对齐效果。通过用 GPT4-Vision 生成的高质量详细描述替换低质量 caption，即使仅替换 SFT 数据中 3.5% 的图文对，也能带来显著且一致的性能提升。

该工作引入了 ShareGPT4V 数据集，包含两部分：
- **ShareGPT4V**（100K）：由 GPT4-Vision 直接生成的高质量详细 caption，平均 942 字符
- **ShareGPT4V-PT**（1.2M）：由基于 100K 数据训练的 Share-Captioner 模型生成的扩展数据集，平均 826 字符

基于该数据集，作者还训练了 ShareGPT4V-7B 模型，在 11 个 benchmark 中 9 个取得最优性能。

## 关键要点

### 训练数据细节

**ShareGPT4V 100K 数据**（GPT4-Vision 生成）：
- 图像来源（约 100K 张）：
  - COCO：50K 张
  - LCS（LAION + CC + SBU）：30K 张
  - SAM：20K 张
  - TextCaps：500 张
  - WikiArt：500 张
  - Web-crawled（地标 + 名人）：1K 张
- Caption 特点：
  - 平均长度 942 字符（远超 COCO-Caption 的 52 字符、BLIP 的 54 字符）
  - 覆盖世界知识、物体属性、空间关系、美学评价
  - 使用 data-source specific prompt 确保内容相关性
  - 图像对模型可见（visible captioning），非 "想象" 式描述

**ShareGPT4V-PT 1.2M 数据**（Share-Captioner 生成）：
- 图像来源：
  - COCO：118K 张
  - SAM：570K 张
  - LLaVA-1.5 预训练数据：558K 张
- 生成方式：
  - 先用 100K GPT4-Vision caption 训练一个 Share-Captioner 模型
  - Share-Captioner 使用统一 prompt 为 1.2M 图像生成 caption
  - 无需针对不同数据源设计特定 prompt
- 生成成本：44 A100 GPU days（全部 caption 生成）
- 质量验证：人工评估中 Share-Captioner 35.3% 被偏好 vs GPT4-Vision 38.2%（26.5% 持平）

**Prompt 设计**：
- Base prompt：描述图像基本信息（物体属性、外观、空间关系）
- Data-source specific prompts：
  - COCO/LCS/SAM：物体类型、颜色、动作、位置、相对位置
  - Web-Celebrity：演员/人物、角色、动作、表情
  - Web-Landmark：地标名称、地理位置、相关知识
  - WikiArt：艺术品、风格、流派
  - Text：文字内容、传达的信息
- 附加约束："Instead of describing the imaginary content, only describing the content one can determine confidently from the image"

### 方法论

**两阶段数据构建**：
1. **Phase 1 - GPT4-Vision 直接生成**：
   - 100K 图像 + data-source specific prompt → GPT4-Vision → 100K 高质量 caption
   - 关键设计：让模型直接"看到"图像（区别于 LLaVA 的 bbox + short caption 输入方式）
   - 避免 hallucination：不依赖 bounding box 描述

2. **Phase 2 - Share-Captioner 扩展**：
   - 用 100K 高质量 caption 微调一个 caption 模型（基于 LMM 架构）
   - 该模型可以用统一 prompt 为任意图像生成详细描述
   - 扩展至 1.2M 张图像

**ShareGPT4V-7B 模型**：
- 架构：LLaVA-1.5 架构（CLIP-Large ViT 336x336 + 2-layer MLP + Vicuna-7B）
- 创新训练策略：
  - Pre-training：使用 ShareGPT4V-PT 数据，同时微调 vision encoder（后半 ViT blocks）、projector 和 LLM
  - SFT：665K 数据（LLaVA-1.5 的 SFT 数据，替换其中 23K caption 为 ShareGPT4V 的 100K caption）
  - 仅微调后半 vision encoder 效果最佳（比冻结提升 52.2 MME Perception）

### 关键发现

**替换实验的显著效果**：
- 在 LLaVA-7B SFT 数据中仅替换 3.5% 为 GPT4-Vision caption：
  - LLaVA-Bench 提升、MME 提升、多项 VQA 提升
- 在 LLaVA-1.5-7B 中替换对应比例：类似提升模式
- 在 Qwen-VL-Chat-7B 中替换 14.5%：同样有效

**ShareGPT4V-7B 性能**（Table 3）：
| Benchmark | ShareGPT4V-7B | 此前最佳 |
|-----------|---------------|---------|
| LLaVA-Bench (Wild) | 72.6 | 70.7 (LLaVA-1.5) |
| MME Perception | 1567.4 | 1531.3 (LLaVA-1.5) |
| MME Cognition | 376.4 | 295.4 (LLaVA-1.5) |
| MMBench | 68.8 | 67.7 (LLaVA-1.5) |
| SEED-Bench | 69.7 | 68.2 (LLaVA-1.5) |
| MM-Vet | 37.6 | 35.4 (LLaVA-1.5) |
| QBench | 63.4 | 62.1 (LLaVA-1.5) |
| SQA (Image) | 68.4 | 71.6 (LLaVA-1.5) |
| VQA-v2 | 80.6 | 80.0 (LLaVA-1.5) |

在 11 项 benchmark 中 9 项超越当时所有 7B 级别模型。

**消融实验关键结论**：
- ShareGPT4V 在 pre-training 和 SFT 两个阶段均有效（Table 4）
- Pre-training caption 质量比数量更重要：ShareGPT4V-PT-558K 远优于 BLIP-558K（Table 5）
- Pre-training 数据量在 100K-1200K 范围内性能持续增长，约 1000K 后趋于饱和（Figure 6）
- 解冻后半 vision encoder（12 blocks from block 12）效果最佳，兼顾性能和显存（Table 6）

## 重要发现

1. **Caption 质量是 LMM 性能的核心瓶颈**：现有数据集（COCO-Caption 平均 52 字符、BLIP 平均 54 字符）的简短描述严重制约了视觉-语言对齐
2. **GPT4-Vision 的"看图说话"能力远优于"想象描述"**：直接输入图像生成的 caption 比 LLaVA 式的 bbox+short caption 输入方式更准确、更少幻觉
3. **少量高质量数据即可显著提升性能**：仅替换 3.5% 的 SFT 数据就能带来一致性提升，ROI 极高
4. **合成数据可以有效替代人工标注**：Share-Captioner 的质量与 GPT4-Vision 相当（人工评估差距仅 2.9%），且生成成本可控
5. **Pre-training 阶段微调 vision encoder 的必要性**：与传统做法（冻结 vision encoder）不同，高质量 caption 使得微调 vision encoder 成为可行且有益的策略
6. **数据增强的通用性**：该方法在 LLaVA-7B、LLaVA-1.5-7B、LLaVA-1.5-13B、Qwen-VL-Chat-7B 等多个模型上均有效

## 问题与思考

1. **成本与可扩展性**：100K GPT4-Vision caption 的 API 成本如何？对于更大规模的数据集是否经济可行？
2. **Hallucination 问题**：虽然论文强调"only describing content one can determine confidently"，但 GPT4-Vision 生成的 942 字符长描述中是否仍存在幻觉？
3. **数据多样性偏差**：100K 图像中 COCO 占 50%，是否导致 caption 风格和内容偏向 COCO 的场景分布？
4. **Share-Captioner 的泛化上限**：基于 100K 数据训练的 captioner 在域外图像上的质量能否保持？
5. **对下游 VLM 训练的深远影响**：LLaVA-OneVision 和 Cambrian-1 均使用了 ShareGPT4V 数据，说明高质量 caption 已成为 VLM 训练的标配
6. **与后续工作的比较**：InternVL-Chat 等更新模型是否也采用了类似的合成 caption 策略？这一范式是否已成为行业标准？

## 相关页面

- [LLaVA-OneVision](../sources/llava-onevision.md) - 使用 ShareGPT4V 图像和 GPT-4V 中文 caption 数据
- [Cambrian-1](../sources/cambrian-1.md) - 数据池中包含 ShareGPT4V 相关数据
