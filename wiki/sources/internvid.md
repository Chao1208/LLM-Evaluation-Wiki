---
title: "InternVid: A Large-scale Video-Text Dataset for Multimodal Understanding and Generation"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [视频数据集, video-text, 对比学习, ViCLIP, 视频检索, 动作识别, 视频生成, OpenGVLab]
sources: [internvid.pdf]
---

## 概述

InternVid（OpenGVLab, Shanghai AI Lab & 多所高校，arXiv 2023 / ICLR 2024 投稿）提出了一个以视频为中心的大规模多模态数据集，包含超过 7 百万 YouTube 视频、共 234M 视频片段，总时长约 760K 小时，附带 4.1B words 的详细描述。其核心贡献是开发了一套可扩展的、利用 LLM 自主构建高质量视频-文本数据集的方法，并基于该数据训练了 ViCLIP 视频-文本表示学习模型，在 zero-shot 动作识别和视频检索任务上达到领先水平。

## 关键要点

### 数据构建流水线

1. **数据收集**：
   - 从 YouTube 收集 7.1M 公开视频，平均时长 6.4 分钟，覆盖 16 个场景类别
   - 收集策略：(a) 热门频道和高评分视频（2M）；(b) 基于 6,104 个动作/活动查询词检索（5.1M），动作词来源于 ATUS（2017-2022）、Kinetics、SomethingSomething、UCF101 等
   - 质量要求：时长 10s-30min，分辨率 >= 360P（85% 为 720P），排除已有数据集中的视频
   - 完整收集元数据：audio, title, subtitles, tags, descriptions

2. **视频切割**（Trimming）：
   - 使用 PySceneDetect（threshold=27）进行场景切割
   - 将平均 5 分钟的长视频分割为约 10 秒的短片段
   - 过滤静止片段和极端动态片段
   - 最终获得 234M 视频片段，时长 2s 到 30s+

3. **多尺度视频标注**（Multiscale Video Captioning）：
   - **Fine scale**：使用 Tag2Text 逐帧描述（低 fps），再用预训练语言模型合成为完整视频描述
   - **Coarse scale**：使用 BLIP2 对中间帧进行 image captioning
   - 这种多尺度方法在保持可扩展性的同时提升了 caption 的视频-文本对应质量

4. **数据特征与子集**：
   - 计算每个 clip 的 aesthetic score（开源模型）和 UMT-SIM score（视频-文本相似度）
   - 提供多种子集：InternVid-10M、InternVid-50M、InternVid-200M
   - **InternVid-10M-FLT**：基于 UMT-SIM top 30% 的高质量子集
   - **InternVid-10M-DIV**：多样性采样子集
   - **InternVid-Aesthetics**：aesthetic score >= 4 的高美学质量子集，用于视频生成

### 数据规模

| 属性 | 数值 |
|------|------|
| 视频总数 | 7.1M |
| 片段总数 | 234M |
| 总时长 | 760.3K 小时 |
| 平均片段时长 | 11.7 秒 |
| 平均 caption 长度 | 17.6 words |
| 分辨率 | 720P（85%），其余 360P-512P |
| 场景类别 | 16 个 |
| 动作/活动类型 | ~6,000 |
| 独特动词数 | 212,155（vs WebVid10M 的 109,485） |

### ViCLIP 模型

- **架构**：基于 ViT-L，包含 video encoder + text encoder，从 CLIP 初始化
- **训练目标**：InfoNCE 对比学习 loss，优化全局视频-文本对齐
- **效率优化**：video masking（随机 patch masking）加速训练，不损害效果
- **训练配置**：64 NVIDIA A100 GPUs，3 天，50M video-text pairs
- **Unmasked pretraining**：最后 0.5 epoch 使用完整视频输入（lr=4e-6）弥合 mask 与下游任务的 gap

### InternVid-ICL（Interleaved Video-Text）

- 利用数据集自然结构生成 7.1M 交错视频-文本数据对
- 三种格式：(a) 同视频顺序排列 clips + captions；(b) 加入 ASR 文本；(c) 多视频拼接模拟对话
- 适用于 Flamingo 风格的 in-context video learning

## 重要发现

1. **Zero-shot 动作识别**（Kinetics 400/600/700）：
   - ViCLIP + InternVid-10M-FLT 达到 SOTA：K400 top-1 64.80%、K600 top-1 62.20%、K700 top-1 54.30%
   - 显著超越 ViCLIP + WebVid10M（59.88、58.66、50.23）和各种 CLIP/EVA-CLIP 变体

2. **Zero-shot 视频检索**（MSR-VTT, LSMDC, DiDeMo, MSVD, ANet）：
   - ViCLIP + InternVid-10M-FLT 在 MSR-VTT T2V R@1 达到 42.4%（vs WebVid10M 的 35.6%）
   - 数据规模从 10M 增加到 200M，性能线性提升

3. **Fine-tuned 动作识别**：
   - ViCLIP + InternVid-200M 在 K400 达到 88.7% top-1（fine-tuned），接近 supervised SOTA

4. **Text-to-Video Generation**：
   - InternVid-Aesthetics-18M + WebVid10M 训练的 t2v baseline：FVD 从 705.25 降至 616.51（UCF101），CLIPSIM 从 0.2657 提升至 0.2951（MSR-VTT）
   - 生成视频无水印（vs WebVid10M 的水印问题）

5. **Video-Centric Dialogue**：VideoChat-ViCLIP 在所有评估维度超越 VideoChat（Eva-g），平均分 2.64 vs 2.29

6. **数据规模效应**：zero-shot 动作识别性能随数据量（10M -> 200M）线性增长；retrieval 性能在 50M 后增长趋缓

## 问题与思考

1. **Caption 质量 vs 规模的权衡**：InternVid 的 multiscale captioning 方法比 VideoChat 直接生成效果更好（Table 11），但相比 Panda-70M 的多教师+人工验证方法，质量上可能仍有差距。不过 InternVid 的 234M clips 规模远超 Panda-70M 的 70.8M
2. **对评测的价值**：InternVid 的子集划分策略（按 aesthetic score、UMT-SIM 筛选）为视频评测数据集构建提供了参考方法论。aesthetic score 和 video-text correlation 可作为视频质量评估的自动化指标
3. **数据多样性**：覆盖 11 个国家/语言的视频来源，16 个类别，212K 独特动词，相比以英语口述视频为主的数据集（HowTo100M、HD-VILA-100M）更多样
4. **False Negative 问题**：论文发现同视频不同 clips 共享相似表示，在对比学习中可能导致 false negative，影响学习效果。这是大规模视频对比学习中的一个重要开放问题
5. **开放许可**：CC BY 4.0 协议，仅提供 YouTube video IDs 而非原始视频，符合 YouTube 政策

## 相关页面

- [CogVideoX](../sources/cogvideox.md)
- [Panda-70M](../sources/panda-70m.md)
- [BLIP-2](../sources/blip2.md)
