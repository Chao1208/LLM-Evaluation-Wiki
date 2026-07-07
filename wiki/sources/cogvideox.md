---
title: "CogVideoX: Text-to-Video Diffusion Models with An Expert Transformer"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [视频生成, text-to-video, 扩散模型, DiT, 3D-VAE, video-captioning, 清华大学, 智谱AI]
sources: [cogvideox.pdf]
---

## 概述

CogVideoX（清华大学 & 智谱 AI，ICLR 2025）是一个大规模 text-to-video 生成模型，基于 Diffusion Transformer（DiT）架构，能够生成 10 秒连续视频，帧率 16fps，分辨率最高 768x1360。该工作针对此前视频生成模型在长时间连贯性和动态运动方面的不足，提出了三大核心设计：3D Causal VAE、Expert Transformer 和渐进式训练策略，并配套了一套高质量视频数据处理和 dense captioning 流水线。

模型发布了 5B 和 2B 两个版本，包含 text-to-video 和 image-to-video 能力，是首个商用级别的开源视频生成模型。

## 关键要点

### 模型架构

1. **3D Causal VAE**：在空间和时间维度同时压缩视频，压缩率为 8x8x4（空间 8x8，时间 4x），16 个 latent channels。采用 temporally causal convolution 确保因果性，支持 context parallel 分布式训练。相比 2D VAE（如 SDXL），显著减少 flickering 并提升 PSNR（29.1 vs 28.4 baseline）

2. **Expert Transformer**：
   - 将文本和视觉 latent 在序列维度拼接后输入共享的 Transformer blocks
   - 使用 **Expert Adaptive LayerNorm**（Expert AdaLN）分别对文本和视觉两种模态进行独立归一化调制，解决不同模态特征空间差异
   - 采用 **3D Full Attention** 替代分离的 2D+1D attention，直接在时空维度建模全局依赖
   - 使用 **3D-RoPE** 位置编码，按 3/8、3/8、2/8 分配给 x、y、t 三个坐标维度

3. **渐进式训练**：四阶段从低分辨率到高分辨率（256x384 -> 480x720 -> 768x1360 -> 768x1360 fine-tune），配合 Multi-Resolution Frame Pack 策略在同一 batch 内混合不同时长和分辨率的视频

### 数据流水线

- **训练数据规模**：过滤后约 35M single-shot video clips，平均时长约 6 秒，另外使用 2B 图片（LAION-5B + COYO-700M）辅助训练
- **视频过滤**：基于 Video-LLaMA 训练 6 个分类器，过滤 editing、低质量、讲座类、文字主导、截屏等负面标签
- **Dense Video Captioning 流水线**（多教师方法）：
  1. 使用 Panda-70M 模型生成短 caption
  2. 使用 CogVLM 图像理解模型对每帧生成 dense image caption
  3. 用 GPT-4 将多帧图像 caption 汇总为完整视频 caption
  4. 用 GPT-4 生成的数据蒸馏训练 LLaMA2，加速大规模 caption 生成
  5. 进一步训练 CogVLM2-Caption 端到端视频描述模型

### 训练技巧

- **Explicit Uniform Sampling**：将 diffusion timestep 区间均匀分配给不同数据并行 rank，确保训练过程中时间步采样更均匀，稳定 loss 曲线
- v-prediction + zero SNR noise schedule
- 高质量 fine-tuning 阶段使用 top 20% 数据

### 模型规格

| 参数 | CogVideoX-2B | CogVideoX-5B |
|------|-------------|-------------|
| Layers | 30 | 42 |
| Attention Heads | 32 | 48 |
| Hidden Size | 1920 | 3072 |
| Position Encoding | sinusoidal | RoPE |
| Max Sequence Length | 82k | 82k |
| Training Precision | BF16 | BF16 |

## 重要发现

1. **自动化评测**（VBench 指标）：CogVideoX-5B 在 7 个指标中 5 个达到 SOTA（Human Action 96.8、Dynamic Degree 62.22、Multiple Objects 70.95、Dynamic Quality 69.5、GPT4o-MTScore 3.36），超越 Gen-2、Pika、OpenSora V1.2 等模型

2. **人类评估**：CogVideoX-5B 在所有 4 个维度（Sensory Quality、Instruction Following、Physics Simulation、Cover Quality）均优于 Kling（2024.7 版本），总分 2.74 vs 2.17

3. **VAE 对比**：CogVideoX 的 3D VAE 在 Flickering（85.5）和 PSNR（29.1）上均优于 Open-Sora（92.4 / 28.5）和 Open-Sora-Plan（90.2 / 27.6）

4. **Ablation 结论**：
   - 3D Full Attention 在训练稳定性和 FVD 上显著优于 2D+1D attention
   - Expert AdaLN 以更少参数超越 MMDiT 架构
   - Explicit Uniform Sampling 加速收敛并降低所有 timestep 的 loss

## 问题与思考

1. **与评测的关联**：CogVideoX 的 VBench 评测体系（Human Action、Scene、Dynamic Degree 等维度）为视频生成评测提供了重要参考，但论文指出某些指标（如 color）倾向于给静态视频高分，因此额外引入 Dynamic Quality 和 GPT4o-MTScore
2. **Dense Captioning 的价值**：多教师 dense captioning 流水线是数据质量的核心保障，直接影响生成视频的语义对齐。这种方法论对视频评测中的自动标注也有借鉴意义
3. **开源意义**：作为首个商用级开源视频生成模型，为视频生成评测提供了可复现的 baseline
4. **推理效率**：5B 模型在 H800 上生成 768x1360 5s 视频需要 500s / 76GB 显存，实际部署成本较高

## 相关页面

- [Panda-70M](../sources/panda-70m.md)
- [InternVid](../sources/internvid.md)
- [BLIP-2](../sources/blip2.md)
