---
title: "Cambrian-1: A Fully Open, Vision-Centric Exploration of Multimodal LLMs"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [多模态, VLM, 视觉编码器, 数据策划, vision-centric, benchmark]
sources: [cambrian-1.pdf]
---

## 概述

Cambrian-1 是纽约大学（NYU）团队开发的 vision-centric 多模态大语言模型（MLLM）系列，由 Shengbang Tong、Ellis Brown、Penghao Wu 等人主导，Yann LeCun 和 Saining Xie 参与指导。与大多数聚焦于语言模型改进的 MLLM 工作不同，Cambrian-1 从视觉表示的角度系统性地探索了 MLLM 的设计空间，涵盖五大支柱：视觉表示、连接器设计、指令调优数据、训练策略和评测方法。

论文发表于 arXiv:2406.16860（2024年12月），核心贡献包括：（1）在 20+ 种视觉编码器上进行系统性比较；（2）提出 Spatial Vision Aggregator（SVA）连接器；（3）构建 Cambrian-10M 数据池并策划出 Cambrian-7M 高质量子集；（4）引入 CV-Bench 视觉中心评测基准；（5）发现并解决"答案机器现象"（Answer Machine Phenomenon）。

## 关键要点

### 训练数据细节

**Cambrian-10M**（原始数据池）：
- 约 9,784K（~9.8M）数据点
- 来自 15+ 个公开数据源
- 涵盖类别：General conversation、OCR、Code、Math、Science、Counting、Language-only
- 数据来源高度不平衡：部分类别（如 Science）仅有极少样本

**数据策划过程**（Cambrian-10M → Cambrian-7M）：

1. **Threshold Capping（阈值上限）**：
   - 对每个数据源设定最大样本数阈值 t
   - 测试 t = 150k、250k、350k、450k
   - 发现 t 在 250k-350k 之间效果最佳（"elbow effect"）
   - 目的：削减头部数据源的过度表示，缓解数据分布的长尾效应

2. **Category Balancing（类别配比）**：
   - 固定 1.35M 数据集进行配比实验
   - 探索不同类别比例对性能的影响
   - 关键发现：
     - General、OCR、Language 三者的平衡最为关键
     - 过多 OCR 数据会损害 General VQA 和 Vision-Centric 性能
     - Knowledge 类性能受 OCR + Chart + Reasoning + Perception 多因素影响
     - Science 数据比例过低会导致性能严重下降

3. **最终 Cambrian-7M**：
   - 约 7M 样本（从 9.8M 中策划）
   - 性能优于使用全量 10M 数据（Average: 55.9 vs 54.8）

**Targeted Internet Data Collection Engine**：
- 自动从互联网收集科学类视觉数据
- 使用 GPT-4 识别物理、化学等子领域话题
- 从 Wikipedia 搜索可靠图文对
- 用 GPT-3.5 生成 instruction-type Q&A
- 生成 161K 科学数据点（比原有科学数据增加 400%）

### 方法论

**视觉编码器研究**（20+ 种模型的系统评估）：
- Language-Supervised：SigLIP、OpenCLIP、DFN-CLIP、EVA-CLIP-02 等
- Self-Supervised：DINOv2、MAE、I-JEPA、MoCo v3 等
- Other：SAM、MiDaS、Stable Diffusion、SupViT 等
- 核心发现：
  - SigLIP ViT-SO400M/14@384 在 language-supervised 模型中综合最优
  - DINOv2 ViT-L/14@518 在 self-supervised 模型中最优
  - 多编码器组合能持续提升性能，尤其在 vision-centric 任务上

**Spatial Vision Aggregator (SVA)**：
- 动态、空间感知的连接器，整合多个视觉编码器的特征
- 核心设计原则：
  1. Spatial inductive bias：每个 query token 对齐到特征图的特定子区域
  2. Multi-layer aggregation：在 LLM 的多层中重复插入 cross-attention
- 超参数：D（cross-attention 层数）、G（query group 数）
- 最终配置：D=3, G=1, 在 LLM 层间 stride=3 插入
- 最终仅使用 576 visual tokens（LLaVA-NeXT 的 1/5）

**Instruction Tuning Recipe**：
- 2-stage 训练：先 1.2M adapter data 预训练连接器，再 737K/7M instruction tuning
- Unfreeze vision encoder 普遍有益（Finding 4）
- 2-stage 优于 1-stage（Finding 3）

**CV-Bench（Cambrian Vision-Centric Benchmark）**：
- 2638 个人工检验的样例
- 将传统视觉 benchmark 转化为 VQA 格式
- 4 个任务：Spatial Relationship (2D)、Object Counting (2D)、Depth Order (3D)、Relative Distance (3D)
- 数据来源：ADE20K、COCO、Omni3D

### 关键发现

**Benchmark 分析**（Finding 1-2）：
- 多数现有 benchmark 不能真正衡量 vision-centric 能力
- SQA-I、MMMU、MathVista 等视觉输入关闭后性能下降 < 5%（依赖 LLM 知识而非视觉理解）
- TextVQA 和 GQA 存在近 40% 的语言偏差
- Benchmark 可分为 4 类集群：General、Knowledge、Chart & OCR、Vision-Centric

**训练策略发现**（Finding 3-6）：
- Finding 3: Two-stage 训练有益，更多 adapter data 进一步提升
- Finding 4: Unfreeze vision encoder 普遍有益；SSL 模型在 vision-centric 任务上特别受益
- Finding 5: 高分辨率编码器大幅提升 chart & vision-centric 性能；ConvNet 架构天然适合高分辨率
- Finding 6: Language supervision 优势明显，但足够数据 + proper tuning 可以缩小 SSL 差距

**多编码器组合**（Finding 7-8）：
- 组合多个编码器（包括 SSL 模型）能增强性能，尤其在 vision-centric 任务上
- SVA 的 spatial inductive bias 和 deep interaction 有助于更好聚合和压缩视觉特征
- 最终组合：OpenAI CLIP + SigLIP + OpenCLIP ConvNeXt-XXL + DINOv2

**State-of-the-art 性能**（Cambrian-1-34B）：
- 使用 576 tokens 超越使用 2880 tokens 的 LLaVA-NeXT 和 Mini-Gemini-HD
- 在 OCR & Chart 和 Vision-Centric 类别上领先尤为明显
- 与 GPT-4V、Gemini-Pro、MM-1 在多项 benchmark 上可比

## 重要发现

1. **Vision-centric 视角的必要性**：当前 MLLM 评测过度依赖 LLM 能力，忽视真正的视觉理解；CV-Bench 填补了这一空白
2. **数据策划比数据规模更重要**：Cambrian-7M（策划后）性能优于 Cambrian-10M（未策划），表明数据质量和平衡性的关键作用
3. **多编码器组合是有效策略**：不同编码器在不同任务上各有优势，组合使用可获得互补收益
4. **SVA 是高效的视觉压缩方案**：576 tokens 即可达到甚至超越 2880 tokens 的性能
5. **Answer Machine Phenomenon**：过度的短答案 VQA 训练会导致模型丧失对话能力，通过在训练数据中加入 system prompt 可有效缓解
6. **Self-supervised 模型的潜力**：DINOv2 等 SSL 模型在 vision-centric 任务上表现接近甚至超越部分 CLIP 模型，值得更多探索

## 问题与思考

1. **数据策划的可复现性**：threshold capping 和 category balancing 的最优超参数是否与 LLM backbone 相关？换用其他 LLM 是否需要重新搜索？
2. **SVA 的计算开销**：多编码器 + 多层 cross-attention 的推理速度和显存消耗如何？论文未详细报告 latency
3. **CV-Bench 的局限性**：仅覆盖 4 类基础视觉任务（2D 空间关系、物体计数、3D 深度、相对距离），是否足以全面评估 vision-centric 能力？
4. **与 LLaVA-OneVision 的互补**：Cambrian-1 强调多编码器和视觉中心，LLaVA-OV 强调数据规模和跨场景迁移，两者理念可否结合？
5. **Targeted Data Engine 的泛化性**：自动收集科学数据的 pipeline 能否扩展到其他稀缺领域（如医疗、金融）？
6. **Benchmark 发现的启示**：如果大多数 benchmark 无法真正测试视觉能力，那当前 MLLM 的"进步"有多少是视觉理解的真正提升？

## 相关页面

- [LLaVA-OneVision](../sources/llava-onevision.md) - 同期工作，不同训练策略
- [ShareGPT4V](../sources/sharegpt4v.md) - Cambrian 数据池中包含的数据源之一
