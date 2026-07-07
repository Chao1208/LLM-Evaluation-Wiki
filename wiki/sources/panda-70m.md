---
title: "Panda-70M: Captioning 70M Videos with Multiple Cross-Modality Teachers"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [视频数据集, video-captioning, 多模态, 多教师蒸馏, video-text, Snap]
sources: [panda-70m.pdf]
---

## 概述

Panda-70M（Snap Inc. & UC Merced & University of Trento，CVPR 2024）提出了一个包含 70.8M 视频片段及对应文本标注的大规模视频-语言数据集。该工作的核心洞察是：没有单一模型能为所有类型的视频生成高质量 caption（单模型最多只能覆盖 31% 的视频），但多个跨模态教师模型联合可覆盖 84.7% 的视频。

数据集从 3.8M 高分辨率（720p）长视频（HD-VILA-100M）出发，通过语义感知切割算法将其分割为 70.8M 个语义一致的短片段（平均 8.5 秒），再利用多教师标注流水线生成高质量 caption（平均 13.2 words）。

## 关键要点

### 数据流水线

1. **Semantics-aware Video Splitting**（两阶段切割）：
   - Stage 1：基于 PySceneDetect 进行 shot boundary detection（cutscene_threshold=25, min_scene_len=15 frames），并处理复杂转场（fade-in/out）
   - Stage 2：基于 ImageBind 特征的语义相似度进行 stitching（合并相邻语义相似片段）
   - 最终将 3,790,459 个长视频切分为 70,817,169 个短片段，平均时长 8.477 秒
   - 引入 **Max Running LPIPS** 指标量化片段语义一致性，优于传统 subtitle alignment（0.256 vs 0.408）和 PySceneDetect（0.256 vs 0.247，但平均视频长度更长 7.9s vs 4.1s）

2. **多教师标注**（Cross-Modality Teachers）：
   - 从 31 个 captioning 模型的大池中，通过人工评估筛选出 8 个代表性教师模型
   - 6 个基础模型：Video-LLaMA（video VQA）、VideoChat（video VQA）、VideoChat Text（NLP-based video VQA）、Video-ChatGPT（video VQA）、BLIP-2（image captioning）、MiniGPT-4（image VQA）
   - 教师利用不同模态输入：视频（V）、字幕（S）、元数据（M，含 title + description）
   - 关键发现：单个模型最高选择率仅 17.85%（BLIP-2 opt6.7b），但 8 个模型联合覆盖 76.8% 视频

3. **Fine-grained Video-to-Text Retrieval**（标注选择）：
   - 收集 100K 视频子集，人工标注最佳 caption
   - Fine-tune Unmasked Teacher（UMT）模型进行 fine-grained retrieval
   - Finetuned UMT 达到 35.90% R@1 准确率（vs pretrained 21.82%）
   - 在全数据集上自动选择最佳 caption 作为最终标注

4. **Student Captioning Model**（知识蒸馏）：
   - 双分支架构：视觉分支（Video-LLaMA 架构）+ 文本分支（Text Q-Former 处理元数据和字幕）
   - Backbone: Vicuna-7B + ViT + Q-Former
   - 学生模型以 7.7% 的偏好率优势超越任何单个教师模型

### 数据规模与特点

| 属性 | 数值 |
|------|------|
| 源视频数 | 3.8M（HD-VILA-100M） |
| 切分后片段数 | 70.8M |
| 平均视频时长 | 8.5s |
| 总时长 | 166.8K 小时 |
| 平均 caption 长度 | 13.2 words |
| 分辨率 | 720p |
| 领域 | 开放域 |

### 对比已有数据集

- vs ASR 标注数据集（HowTo100M、HD-VILA-100M）：Panda-70M 的 caption 精确描述视频中的主体和动作，而 ASR 转录通常与视觉内容语义不匹配
- vs 人工标注数据集（MSR-VTT、VATEX）：规模大 3 个数量级
- 视频质量更高（720p、语义连贯、无水印）

## 重要发现

1. **Video Captioning**（MSR-VTT + MSVD）：使用 Panda-2M 预训练的 Video-LLaMA 在 BLEU-4 上比官方权重提升 17.7%（MSR-VTT）和 18.5%（MSVD）

2. **Video-Text Retrieval**（MSR-VTT + DiDeMo + MSVD）：使用 Panda-5M 预训练的 UMT 在 zero-shot text-to-video R@1 上分别提升 7.0%、0.6%、4.9%

3. **Text-to-Video Generation**（UCF101 + MSR-VTT）：使用 Panda-2M 预训练的 AnimateDiff 在 UCF101 FVD 上降低 77.4（499.3 -> 421.9），在 MSR-VTT CLIPSim 上提升至 0.2880

4. **学生模型表现**：双模态学生模型（video+text inputs）达到 21.4% 偏好率，接近所有教师联合的 23.3%

## 问题与思考

1. **多教师策略的普适性**：论文发现单模型覆盖率上限约 31%，这对视频评测中的自动标注有重要启示——需要多模型集成才能保证标注质量。CogVideoX 的 dense captioning 流水线直接引用了 Panda-70M 的视频标注模型
2. **语义切割的重要性**：Max Running LPIPS 作为语义一致性指标，可能对视频评测中的样本预处理有参考价值
3. **数据局限性**：源数据（HD-VILA-100M）以口述类视频为主（news、documentary films、instructional videos），非口述类视频覆盖不足。且为保证语义一致性，牺牲了内容多样性和视频平均时长
4. **隐私保护**：使用 NLTK 替换所有人名为 "person"，过滤有害内容

## 相关页面

- [CogVideoX](../sources/cogvideox.md)
- [InternVid](../sources/internvid.md)
- [BLIP-2](../sources/blip2.md)
