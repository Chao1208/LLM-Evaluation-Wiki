---
title: "Scaling Rectified Flow Transformers for High-Resolution Image Synthesis (Stable Diffusion 3)"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [图像生成, diffusion-model, rectified-flow, transformer, text-to-image, Stability-AI]
sources: [stable-diffusion-3.pdf]
---

## 概述

本文由 Stability AI 团队发表（arXiv:2403.03206，2024年3月），提出了 Stable Diffusion 3 的核心技术方案。论文聚焦三个核心贡献：(1) 提出改进的 Rectified Flow 训练方法，通过新的噪声采样策略（logit-normal 分布）将训练偏向感知相关的中间时间步；(2) 提出 Multimodal Diffusion Transformer (MM-DiT) 架构，为文本和图像使用独立权重并允许双向信息流动；(3) 进行大规模 scaling 研究，将模型扩展至 8B 参数，验证了验证损失与生成质量的强相关性。最终模型在 GenEval、T2I-CompBench 和人类偏好评估上超越了 SDXL、DALL-E 3 等现有最优模型。

## 关键要点

### 训练数据

- 使用 CogVLM 生成合成 caption，训练时采用 **50% 原始 caption + 50% 合成 caption** 的混合比例
- 在 GenEval benchmark 上验证：50/50 混合比仅原始 caption（overall 43.27%）显著提升至 49.78%
- 数据预处理包括：NSFW 内容过滤、美学评分低分过滤、基于聚类的去重（去除感知和语义重复）
- 预计算所有图像的 autoencoder latent 和文本编码器 embedding 以加速训练

### 方法论

**Rectified Flow 改进**：
- 对比了 61 种不同的 diffusion 公式组合（包括 EDM、LDM-Linear、Cosine、RF 等）
- 提出 logit-normal 时间步采样 `rf/lognorm(0.00, 1.00)`，将更多权重分配给中间时间步，一致性地取得最优排名
- Rectified Flow 在少步采样（5步、25步）时相比其他公式退化更少，具有采样效率优势

**MM-DiT 架构**：
- 基于 DiT (Peebles & Xie, 2023) 架构，但为文本和图像 token 使用**两组独立权重**
- 在 attention 操作时将两个模态的序列拼接，允许双向信息流动
- 相比 DiT、CrossDiT、UViT，MM-DiT 在验证损失、CLIP score、FID 上均表现最优
- 使用 QK-Normalization（RMSNorm）稳定大规模混合精度训练，防止 attention logit 发散

**文本编码**：
- 使用三个文本编码器：CLIP L/14、OpenCLIP bigG/14、T5 XXL
- 训练时以 46.3% 概率随机丢弃各编码器，推理时可灵活选择子集
- T5 对复杂提示（详细描述、长文本渲染）贡献最大，但移除后对美学质量影响有限

**多分辨率训练**：
- 先在 256x256 低分辨率预训练，再 finetune 到更高分辨率（768x768 等），支持混合宽高比
- 使用 bucketed sampling 确保每个 batch 内图像同质化（H*W 约等于 S^2）
- 提出 resolution-dependent timestep shifting：高分辨率图像需要更多噪声才能破坏信号，通过公式 $t_m = \frac{\sqrt{m/n} \cdot t_n}{1 + (\sqrt{m/n} - 1) t_n}$ 将时间步从低分辨率映射到高分辨率
- 高分辨率训练后使用 Direct Preference Optimization (DPO) 对齐人类偏好

**改进的 Autoencoder**：
- 将 latent 通道数从传统的 4 增加到 16，显著提升重建质量（FID 从 2.41 降到 1.06）
- 下采样因子保持 f=8

### 评测

**GenEval 结果**（depth=38, 1024^2, w/DPO）：
| 模型 | Overall | Single | Two | Counting | Colors | Position | Color Attr. |
|------|---------|--------|-----|----------|--------|----------|-------------|
| DALL-E 3 | 0.67 | 0.96 | 0.87 | 0.47 | 0.83 | 0.43 | 0.45 |
| SD3 (8B) | **0.74** | **0.99** | **0.94** | **0.72** | 0.89 | 0.33 | **0.60** |

**Scaling 特性**：
- 模型深度从 d=15 到 d=38（对应约 0.5B 到 8B 参数）
- 验证损失与 GenEval (r=-0.920)、T2I-CompBench (r=-0.898)、人类偏好 ELO (r=-0.982) 均呈强负相关
- 更大模型不仅性能更好，还需要更少的采样步数达到峰值性能

**人类偏好评估**（Figure 7）：
- 8B 模型在 visual aesthetics、prompt following、typography 三个维度上 win rate 均超越 PixArt-alpha、SDXL、SDXL-Turbo、Stable Cascade、Playground V2.5、Midjourney V6、DALL-E 3 w/o T5

## 重要发现

1. **Rectified Flow 优于传统 diffusion 公式**：在统一对比实验中，RF + logit-normal 采样在 CLIP 和 FID 上一致优于 LDM-Linear（eps/linear）和 EDM 等公式，尤其在少步采样场景优势显著
2. **MM-DiT 的模态特异性设计**：为不同模态使用独立权重但共享 attention 的设计，比纯共享权重（DiT）或纯交叉注意力（CrossDiT）更优
3. **合成 caption 的最优混合比是 50/50**：不同于 DALL-E 3 使用 95% 合成 caption，SD3 发现 50% 混合比在保留概念多样性的同时提升了 prompt following
4. **验证损失是可靠的性能预测指标**：可用于在不跑完整评测的情况下比较模型质量
5. **Scaling 无饱和迹象**：从 0.5B 到 8B 参数的 scaling 趋势平滑下降，暗示进一步扩大模型仍有收益

## 问题与思考

- SD3 使用 50/50 caption 混合比，而 DALL-E 3 使用 95% 合成 caption，这一差异可能源于 caption 质量和生成方式的不同。SD3 使用 CogVLM 而非专门训练的 captioner，合成 caption 可能存在更多遗忘现实概念的问题。
- MM-DiT 的双流设计虽然性能优越，但参数量是单流 DiT 的约 1.5-2 倍（同深度下），计算效率的 tradeoff 需要关注。
- 论文未报告 FID 在标准 benchmark（如 COCO-30K）上的绝对数值，主要依赖 GenEval 和人类偏好，这使得与旧文献的横向比较困难。
- Resolution-dependent timestep shifting 是一个优雅的理论发现，将分辨率变化形式化为信噪比的对数偏移，可能对其他 flow-based 模型也有价值。
- 模型开源（weights + code），对社区影响巨大，但 8B 规模的推理成本可能限制实际部署。

## 相关页面

- [DALL-E 3 System Card](dalle3-system-card.md)
