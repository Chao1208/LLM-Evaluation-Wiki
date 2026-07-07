---
title: "Qwen2.5-VL Technical Report"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [多模态, VLM, Qwen, 阿里巴巴, 动态分辨率, MRoPE, 视频理解, Agent, OCR, 开源]
sources: [qwen-vl-2.5.pdf]
---

## 概述

Qwen2.5-VL（Qwen Team，阿里巴巴，arXiv:2502.13923，2025年2月）是 Qwen 视觉语言模型系列的最新旗舰，在视觉识别、目标定位、文档解析和长视频理解等方面取得显著进步。模型提供三个规模：**Qwen2.5-VL-3B、7B、72B**，覆盖从边缘 AI 到高性能计算的多种场景。旗舰 72B 模型匹配或超越 GPT-4o 和 Claude 3.5 Sonnet，尤其在文档理解和图表解析方面领先。

核心技术贡献：
1. 引入 Window Attention 优化视觉编码器计算效率
2. 动态 FPS 采样，将动态分辨率扩展到时间维度
3. MRoPE 升级为与绝对时间对齐的时序编码
4. 预训练数据从 1.2T 扩展至 4.1T tokens

## 关键要点

### 模型架构

**三组件设计**：Vision Encoder + Vision-Language Merger + LLM Decoder

| 配置 | Qwen2.5-VL-3B | Qwen2.5-VL-7B | Qwen2.5-VL-72B |
|------|--------------|--------------|---------------|
| ViT Hidden Size | 1280 | 1280 | 1280 |
| ViT Layers | 32 | 32 | 32 |
| ViT Num Heads | 16 | 16 | 16 |
| Patch Size | 14 | 14 | 14 |
| Window Size | 112 | 112 | 112 |
| Full Attention Layers | {7,15,23,31} | {7,15,23,31} | {7,15,23,31} |
| LLM Hidden Size | 2048 | 3584 | 8192 |
| LLM Layers | 36 | 28 | 80 |
| LLM KV Heads | 2 | 4 | 8 |
| Vocabulary Size | 151646 | 151646 | 151646 |
| Trained Tokens | 4.1T | 4.1T | 4.1T |

**Vision Encoder 创新**：
- 从头训练全新 ViT，采用 RMSNorm + SwiGLU FFN，与 LLM 架构对齐
- 仅 4 层使用 Full Self-Attention（layers 7, 15, 23, 31），其余层用 Window Attention（window 112x112 = 8x8 patches）
- 计算复杂度从二次方降为近似线性（相对于 patch 数量）
- 支持原生分辨率输入，无需归一化坐标

**Vision-Language Merger**：
- 将相邻 4 个 patch 特征分组拼接，通过 2 层 MLP 投射到 LLM 维度
- 有效压缩 4 倍视觉 token 数量

**MRoPE（Multimodal Rotary Position Embedding）对齐绝对时间**：
- 分解为 temporal、height、width 三维度
- 文本：三维使用相同 position ID（等价于 1D RoPE）
- 图像：temporal 恒定，height/width 反映空间位置
- 视频：temporal ID 与绝对时间对齐（非帧序号），可感知不同 FPS 下的真实时间间隔

### 训练数据策略

**预训练（~4.1T tokens，三阶段）**：

| 阶段 | 数据类型 | Token量 | 序列长度 | 训练部分 |
|------|---------|--------|---------|---------|
| Visual Pre-Training | Image Caption, Knowledge, OCR | 1.5T | 8192 | ViT only |
| Multimodal Pre-Training | + Pure text, Interleaved, VQA, Video, Grounding, Agent | 2T | 8192 | ViT & LLM |
| Long-Context Pre-Training | + Long Video, Long Agent, Long Document | 0.6T | 32768 | ViT & LLM |

**预训练数据细节**：
- **交错图文数据**：4 阶段质量评分系统（文本质量、图文相关性、互补性、信息密度平衡）
- **Grounding 数据**：使用绝对坐标（非归一化），覆盖 10,000+ 物体类别，含 point grounding
- **文档解析数据**：统一 HTML 格式表示文档结构（表格、图表、公式、手写、乐谱等）
- **OCR 数据**：多语言（法、德、西、葡、阿拉伯、俄、日、韩、越等），含 100 万合成图表 + 600 万真实表格
- **视频数据**：动态采样 FPS，长视频（>30min）合成多帧描述
- **Agent 数据**：跨平台（手机、网页、桌面）UI 截图 + 操作轨迹，含推理过程标注

**后训练（SFT + DPO）**：
- SFT 数据约 200 万条（50% 纯文本 + 50% 多模态），ViT 参数冻结
- 二阶段数据过滤：(1) Qwen2-VL-Instag 分类器分 8 主域 30 子域，(2) 规则过滤 + 模型打分
- Rejection Sampling：基于中间模型生成并筛选高质量 CoT 推理数据
- DPO：仅用图文和纯文本偏好数据，每样本只处理一次

### 评测结果

**与 SOTA 模型对比（72B）**：

| 基准 | Claude-3.5 Sonnet | GPT-4o | InternVL2.5-78B | Qwen2.5-VL-72B |
|------|-------------------|--------|----------------|---------------|
| MMMU | 68.3 | 69.1 | 70.1 | **70.2** |
| MMMU-Pro | 51.5 | 51.9 | 48.6 | **51.1** |
| MathVista | 67.7 | 63.8 | 72.3 | **74.8** |
| MMBench-EN-V1.1 | 80.9 | 83.1 | 87.4 | **88.4** |
| MMStar | 65.1 | 64.7 | 69.5 | **70.8** |
| MME | 1920 | 2328 | **2494** | 2483 |
| MMVet | 70.1 | 69.1 | 72.3 | **74.0** |

**文档/OCR 能力**：
- CC-OCR: **79.8**（超越 Claude-3.5 Sonnet 62.5、GPT-4o 66.9）
- OCRBench: **885**（超越 InternVL2.5-78B 854）
- DocVQA: **96.4**（SOTA）

**视频理解**：
- MVBench: **70.4**（超越 GPT-4o 64.6）
- LVBench: **47.3**（超越 GPT-4o 30.8）
- Charades-STA mIoU: **50.9**（超越 GPT-4o 35.7），验证 MRoPE 时序对齐效果

**Grounding 能力**：
- RefCOCO val: 93.7（超越 Gemini 1.5 Pro 73.2）
- ODinW（开放词汇检测）: 43.1 mAP
- CountBench: **93.6**（超越 Gemini 1.5 Pro 85.5、GPT-4o 87.9）

**Agent 能力**：
- ScreenSpot: 87.1（vs GPT-4o 18.1）
- ScreenSpot Pro: **43.6**（新 SOTA）
- Android Control Low_EM: **93.7**（超越 GPT-4o 19.4）

**纯文本保持能力**：
- MMLU-Pro: 71.2、MATH: 83.0、HumanEval: 87.8、IFEval: **86.3**
- 与同规模纯文本 LLM 相当，验证多模态训练未损害语言能力

### 小模型表现

- Qwen2.5-VL-7B 在 MMStar 达 63.9%，与 GPT-4o 64.7% 接近
- Qwen2.5-VL-3B 在 Android Control Low_EM 达 84.4，超越 Claude（19.4）

## 重要发现

1. **Window Attention 高效可行**：仅用 4 层全注意力 + 28 层窗口注意力即可在保持性能的同时大幅降低 ViT 计算量，使原生高分辨率输入成为实际可部署方案
2. **绝对时间对齐 MRoPE 显著提升视频理解**：在 Charades-STA 视频时序定位任务上远超其他模型（50.9 vs GPT-4o 35.7），证明时间感知对视频理解至关重要
3. **数据质量比数量更重要**：4 阶段评分过滤交错数据、rejection sampling + model-based filtering 等精细化数据策略是性能提升的关键
4. **统一 HTML 格式处理文档**：将表格、图表、公式、手写等统一为带坐标的 HTML 格式，实现了文档元素的通用解析能力
5. **多模态训练不损害文本能力**：Qwen2.5-VL-72B 在纯文本任务上达到与 Qwen2.5-72B 相当水平，SFT 50% 纯文本配比有效保护了语言能力
6. **Agent 能力涌现**：通过统一的函数调用格式和跨平台操作轨迹数据，模型可直接作为 GUI Agent 使用

## 问题与思考

1. **计算成本未公开**：论文未报告总训练计算量（4.1T tokens 的训练成本是多少 GPU-hours？），难以评估该方案的可复现性
2. **闭源数据依赖**：部分训练数据为 "in-house" 和 "proprietary"，限制了完全复现
3. **动态分辨率的 token 开销**：原生分辨率虽然提升性能，但高分辨率图像的 token 数量巨大（如 11427 tokens），推理成本仍然是实际部署的瓶颈
4. **评测公平性**：72B 模型与其他 72B+ 模型对比合理，但 Qwen2.5-VL 使用了 4.1T 多模态 tokens 训练，数据量远超多数对比模型
5. **与 VTC-CLS 等 token 压缩方法结合**：Qwen2.5-VL 的大量视觉 token（万级别）正是 VTC-CLS 等压缩方法的理想应用场景
6. **开源价值**：3B/7B/72B 全系列开源，为社区研究和应用提供了强大基座

## 相关页面

- [BLIP-2: Bootstrapping Language-Image Pre-training](../sources/blip2.md)
- [LLaVA: Visual Instruction Tuning](../sources/llava.md)
- [VTC-CLS: Training-free Visual Token Compression](../sources/internvl-2.5.md)

（来源：[qwen-vl-2.5.pdf](../../raw/papers/qwen-vl-2.5.pdf)）
