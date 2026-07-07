---
title: "Improving Image Generation with Better Captions (DALL-E 3)"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [图像生成, diffusion-model, synthetic-captions, text-to-image, OpenAI, prompt-following]
sources: [dalle3-system-card.pdf]
---

## 概述

本文由 OpenAI 团队发表（2023），核心论点是：text-to-image 模型的 prompt following 能力可以通过在高质量合成 caption 上训练来大幅提升。作者假设现有模型的 prompt following 不足源于训练数据中噪声和不准确的 image caption（如互联网 alt-text 往往简短、不相关甚至错误）。为解决此问题，团队训练了一个专用的 image captioner，用其对整个训练集重新生成高质量描述性 caption，然后在这些合成 caption 上训练 diffusion 模型。最终构建的 DALL-E 3 在 prompt following、文本渲染、风格和连贯性等方面显著超越 DALL-E 2 和 Stable Diffusion XL。

## 关键要点

### 训练数据

- 核心创新是 **Dataset Recaptioning**：用专门训练的 image captioner 为训练集中的每张图像生成合成 caption
- Captioner 基于预训练的 CLIP image embedding + 语言模型架构，按 Yu et al. (2022a) CoCa 方法联合预训练
- 两阶段 fine-tune captioner：
  - 第一阶段：生成 **Short Synthetic Captions (SSC)**，偏向描述图像主体
  - 第二阶段：生成 **Descriptive Synthetic Captions (DSC)**，详细描述主体、背景、文本、风格、色彩等
- 最终 DALL-E 3 使用 **95% 合成 caption + 5% ground-truth caption** 的混合比训练
- 训练使用 T5-conditioned image diffusion model，batch size 2048，训练 500,000 步（对应约 10 亿张训练图像）

### 方法论

**Captioner 训练**：
- 使用 CLIP image embedding F(i) 作为视觉条件，构建条件语言模型
- 通过两组精心构造的小数据集分别 fine-tune 出 SSC 和 DSC captioner
- DSC captioner 生成的描述涵盖：图像主体、周围环境、背景、文本内容、风格、色彩等

**合成 caption 与 ground-truth 的混合**：
- 直接使用 100% 合成 caption 会导致模型过拟合 captioner 的分布特征（如特定标点、起始词模式）
- 混合 ground-truth caption 起到正则化作用，保持对自然语言提示的泛化能力
- 实验测试了 65%、80%、90%、95% 合成比例，结果显示**更高合成比例总是提升 CLIP score**
- 65% 混合比在所有评估中远落后于其他比例，最终被放弃

**实际使用中的 caption upsampling**：
- 高合成比例训练导致模型在推理时需要高度描述性的提示
- 使用 GPT-4 将用户的简短提示"上采样"为详细描述性 caption
- 此技巧可消歧复杂关系、添加细节，显著提升生成质量

**模型架构**（Appendix A）：
- 基于文本条件 U-Net latent diffusion model，3阶段
- 使用 Rombach et al. (2022) 的 VAE（8x 下采样），训练在 256px 图像上，模型输入为 32x32 latent
- 文本条件通过 T5 XXL 编码器，输出 latent 通过 cross-attention 送入 U-Net
- DALL-E 3 额外训练了 diffusion decoder 替代 VAE decoder，改善精细细节（文本、人脸），使用 consistency distillation 压缩至 2 步

### 评测

**自动评测结果**（Table 1）：

| Metric | DALL-E 3 | DALL-E 2 | Stable Diffusion XL |
|--------|----------|----------|---------------------|
| MSCOCO Captions CLIP Score | **32.0** | 31.4 | 30.5 |
| Drawbench short (GPT-V) | **70.4%** | 49.0% | 46.9% |
| Drawbench long (GPT-V) | **81.0%** | 52.4% | 51.1% |
| T2I-C B-VQA Colors | **81.1%** | 59.2% | 61.9% |
| T2I-C B-VQA Shape | **67.5%** | 54.7% | 61.9% |
| T2I-C B-VQA Texture | **80.7%** | 63.7% | 55.2% |

- Drawbench long 使用 GPT-4 "upsampled" caption 后差距进一步扩大
- T2I-CompBench 上 DALL-E 3 在颜色、形状、纹理绑定上全面 SOTA

**人类评估结果**（Table 2，ELO 分数）：

| Dataset | DALL-E 3 | Midjourney 5.2 | SDXL | DALL-E 2 |
|---------|----------|----------------|------|----------|
| DALL-E 3 Eval (prompt following) | **153.3** | -104.8 | -189.5 | - |
| DALL-E 3 Eval (style) | **74.0** | 30.9 | -95.7 | - |
| MSCOCO (coherence) | **71.0** | 48.9 | -84.2 | - |
| Drawbench | **61.7** | - | -34.0 | -79.3 |

- 在 prompt following、style、coherence 三个维度上，DALL-E 3 均以大幅优势领先所有竞争对手

**评测方法论创新**：
- 使用 GPT-V（vision-enabled GPT-4）进行自动化 Drawbench 评估
- 人类评估采用 170 条 prompt 的专用评估集（DALL-E 3 Eval），覆盖真实使用场景
- 对每对图像收集 3 位评价者的评分，使用 Nichol et al. (2022) 的 ELO 算法计算分数
- 公开所有评估样本和代码以支持可复现研究

### Caption 类型影响

三种模型对比（均训练 500k 步）：
1. 仅 ground-truth caption → baseline
2. 95% Short Synthetic Captions → 略优于 baseline
3. 95% Descriptive Synthetic Captions → **显著优于** baseline，在合成 caption 评估上优势更大

关键发现：合成 caption 训练的模型不仅在合成 caption 上评估更好，**在 ground-truth caption 评估上也略优于** baseline，证明没有使用合成 caption 的负面影响。

## 重要发现

1. **训练数据质量是 prompt following 的瓶颈**：互联网 alt-text 普遍简短、不准确、与图像无关，导致模型无法学习精确的文本-图像对应
2. **Descriptive Synthetic Captions 全面优于 Short Synthetic Captions 和 Ground-truth**：详细描述性 caption 为模型提供了更丰富的学习信号
3. **95% 合成比例最优**：更高比例总是更好，但需保留少量 ground-truth 做正则化
4. **GPT-4 caption upsampling 是强大的推理技巧**：将简短用户提示扩展为详细描述，可消歧义并提升生成质量
5. **合成 caption 的局限性**：captioner 在空间关系描述上不可靠（"左边"、"下面"等），且容易幻觉物种/品种名称等细节

## 问题与思考

- 论文明确声明不涵盖 DALL-E 3 的训练或实现细节，仅聚焦合成 caption 对 prompt following 的影响。实际模型可能有更多未公开的改进。
- 95% 合成 caption 的最优比例与 SD3 的 50% 形成有趣对比。可能的解释：(1) OpenAI 的 captioner 质量更高、幻觉更少；(2) OpenAI 的模型架构更擅长利用详细描述；(3) 评测指标偏向 prompt following 而非概念多样性。
- GPT-V 作为自动评估器的可靠性值得关注：论文承认 GPT-V 在计数任务上不优于随机，因此补充了人类评估。这提示 LLM-as-Judge 在视觉领域仍有明显盲区。
- Caption upsampling 思路对评测有重要启示：如果模型需要详细提示才能发挥最佳水平，那么使用简短提示的评测可能低估了模型的真实能力。
- 文本渲染能力的改进归因于 captioner 能识别图像中的文本并将其写入 caption，但由于 T5 编码器将词作为整体 token 处理（而非字符级），文本渲染仍不完全可靠。

## 相关页面

- [Stable Diffusion 3](stable-diffusion-3.md)
- [Florence-2](florence-2.md)
