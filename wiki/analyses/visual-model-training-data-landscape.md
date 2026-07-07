---
title: "视觉模型训练数据全景研究"
type: analysis
created: 2026-05-06
updated: 2026-05-06
tags: [训练数据, VLM, 图像生成, 视频生成, caption, 数据集, 数据管线]
sources: [raw/papers/llava-onevision.pdf, raw/papers/cambrian-1.pdf, raw/papers/sharegpt4v.pdf, raw/papers/stable-diffusion-3.pdf, raw/papers/dalle3-system-card.pdf, raw/papers/florence-2.pdf, raw/papers/datacomp.pdf, raw/papers/laion-5b.pdf, raw/papers/cogvideox.pdf, raw/papers/panda-70m.pdf, raw/papers/internvid.pdf, raw/papers/qwen-vl-2.5.pdf]
---

# 视觉模型训练数据全景研究

## 研究背景

本报告系统梳理了 2022-2026 年视觉模型（VLM、图像生成、视频生成、Caption 模型）的训练数据生态，覆盖数据规模、分布、制作方法、各阶段差异等核心维度。涵盖 12+ 篇论文和技术报告的深度分析。

---

## 一、数据集全景：规模与分布

### 1.1 公开大规模图像-文本数据集

| 数据集 | 规模 | 来源 | 过滤策略 | 文本质量 | 开源 |
|--------|------|------|----------|----------|------|
| LAION-5B | 58.5 亿对 | Common Crawl alt-text | CLIP cosine > 0.28 | 低（原始 alt-text） | 是 |
| DataComp-1B | 128 亿候选→14 亿精选 | Common Crawl | CLIP + 文本复杂度 | 低-中 | 是 |
| COYO-700M | 7 亿对 | Web 爬取 | 多维过滤 | 低 | 是 |
| CC12M | 1200 万对 | Web 爬取 | 严格过滤 | 中 | 是 |
| WebLI | 100 亿+ | Google 内部 | 未公开 | 未公开 | 否 |
| SA-1B | 11 亿 mask（1100 万图） | 人工+模型辅助 | N/A（分割任务） | N/A | 是 |

（来源：[LAION-5B](../sources/laion-5b.md)、[DataComp](../sources/datacomp.md)）

### 1.2 高质量合成 Caption 数据集

| 数据集 | 规模 | 生成方式 | 平均长度 | 用途 |
|--------|------|----------|----------|------|
| ShareGPT4V | 10 万 | GPT-4V 直接标注 | 942 字符 | VLM SFT |
| ShareGPT4V-PT | 124.6 万 | Share-Captioner（GPT-4V 蒸馏） | 826 字符 | VLM 预训练 |
| ALLaVA-Caption | 71.5 万 | GPT-4V 生成 | ~500 字符 | VLM 训练 |
| LLaVA-OneVision-SI | 340 万 | 多源合成 | 不等 | VLM 单图 SFT |
| Cambrian-7M | 700 万 | 15 源混合精选 | 不等 | VLM 训练 |
| FLD-5B | 54 亿标注（1.26 亿图） | 迭代专家模型 | 结构化标注 | Florence-2 预训练 |

（来源：[ShareGPT4V](../sources/sharegpt4v.md)、[LLaVA-OneVision](../sources/llava-onevision.md)、[Cambrian-1](../sources/cambrian-1.md)、[Florence-2](../sources/florence-2.md)）

### 1.3 视频-文本数据集

| 数据集 | 规模 | 视频来源 | Caption 方法 | 平均时长 |
|--------|------|----------|-------------|----------|
| WebVid-10M | 1000 万 clips | Stock footage | 原始描述 | ~18s |
| Panda-70M | 7080 万 clips | YouTube 3.8M 视频 | 多教师投票 | ~8.5s |
| InternVid | 2.34 亿 clips（700 万视频） | YouTube | ViCLIP + LLM | ~11.7s |
| CogVideoX 训练集 | 3500 万 clips | 未公开 | 多教师密集标注 | 不等 |
| HD-VILA-100M | 1 亿 clips | YouTube | ASR + 字幕 | ~13.4s |

（来源：[Panda-70M](../sources/panda-70m.md)、[InternVid](../sources/internvid.md)、[CogVideoX](../sources/cogvideox.md)）

---

## 二、训练阶段与数据差异

### 2.1 VLM 多阶段训练范式

以 LLaVA-OneVision 为代表的 SOTA VLM 采用 4 阶段渐进式训练：

| 阶段 | 名称 | 数据规模 | 数据特征 | 目标 |
|------|------|----------|----------|------|
| Stage 1 | 模态对齐 | ~120 万 | 图文对，短 caption | 对齐视觉-语言表示 |
| Stage 2 | 高质量知识 | ~340 万 | 合成长 caption + 知识 QA | 注入视觉理解能力 |
| Stage 3 | 单图指令微调 | ~340 万 | 多轮对话、推理任务 | 指令遵循 |
| Stage 4 | OneVision（多图+视频） | ~270 万 | 多图对比、视频理解 | 统一多模态 |

（来源：[LLaVA-OneVision](../sources/llava-onevision.md)）

**关键发现**：
- Stage 1 数据质量对下游性能影响巨大——用 ShareGPT4V 替换仅 3.5% 的 SFT 数据即带来显著提升
- 各阶段数据需**逐步精细化**：Stage 1 容忍噪声 alt-text，Stage 3 要求高质量合成对话
- 视频阶段引入时，需防止"遗忘"——通过混入单图数据缓解

### 2.2 图像生成模型训练阶段

以 Stable Diffusion 3 为代表：

| 阶段 | 数据规模 | 分辨率 | Caption 策略 | 目的 |
|------|----------|--------|-------------|------|
| 预训练 | 数十亿对 | 256×256 | 50% 原始 + 50% 合成 | 学习图像分布 |
| 高分辨率微调 | 数亿对 | 512-1024 | 合成为主 | 提升细节 |
| 美学微调 | 数百万对 | 1024+ | 高美学分数样本 | 提升视觉质量 |

（来源：[Stable Diffusion 3](../sources/stable-diffusion-3.md)）

**DALL-E 3 的 Caption 革命**：
- 训练专用 image captioner → 用合成 caption 完全替代原始 alt-text
- 95% 合成 caption + 5% ground truth caption（防止 captioner 幻觉传播）
- 效果：文本渲染能力 +300%，复杂组合提示遵循大幅提升

（来源：[DALL-E 3](../sources/dalle3-system-card.md)）

### 2.3 视频生成模型训练阶段

| 阶段 | 代表方案 | 数据特征 |
|------|----------|----------|
| 图像预训练 | CogVideoX | 先在图像上训练基础生成能力 |
| 低分辨率视频 | CogVideoX | 短视频、低帧率、基础运动 |
| 高分辨率精调 | CogVideoX | 高质量视频子集、长时序 |
| 联合训练 | Sora（推测） | 图像+视频+文本多模态联合 |

---

## 三、数据制作方法论

### 3.1 大规模爬取 + 过滤（LAION 路线）

**流程**：Web 爬取 → 图文配对 → 多维过滤 → 去重 → 安全检查

**DataComp 验证的最佳实践**：
1. **CLIP Score 过滤**最有效：cosine similarity 阈值过滤可将数据从 128 亿压缩至 14 亿，性能反而提升
2. **文本复杂度过滤**有效：过滤过短/过长/重复 alt-text
3. **Image-based 去重**（如 SemDeDup）进一步提升质量
4. **关键发现**：在 medium scale (128M) 上表现最好的策略未必在 large scale (1B+) 上最优

（来源：[DataComp](../sources/datacomp.md)）

### 3.2 模型合成 Caption（ShareGPT4V / DALL-E 3 路线）

**两阶段蒸馏范式**（已成为行业标准）：

```
Step 1: 昂贵大模型（GPT-4V）标注种子集（~100K）
Step 2: 用种子集训练轻量 Captioner
Step 3: 轻量 Captioner 批量生成（~1M+）
```

**ShareGPT4V 实践**：
- GPT-4V 标注 10 万图 → 训练 Share-Captioner → 生成 124.6 万 caption
- 人工评估：Share-Captioner 35.3% 偏好 vs GPT-4V 38.2%（接近持平）
- 成本：44 A100 GPU-days 生成全量数据

**DALL-E 3 实践**：
- 训练 image captioner（描述性+简洁性两种模式）
- 完全替代 alt-text 训练扩散模型
- 95/5 合成/真实混合防止幻觉累积

（来源：[ShareGPT4V](../sources/sharegpt4v.md)、[DALL-E 3](../sources/dalle3-system-card.md)）

### 3.3 人工标注 + 模型辅助（SA-1B / Florence-2 路线）

**Florence-2 的 FLD-5B 迭代精炼法**：
1. 初始化：用现有模型（SAM、DINO、PaddleOCR 等）生成初始标注
2. 训练：在初始标注上训练 Florence 模型
3. 精炼：用训练后的模型重新标注，过滤低质量
4. 迭代：重复 2-3 步直到质量收敛

126M 图像 × 10 种任务 = 54 亿标注，成本远低于纯人工

（来源：[Florence-2](../sources/florence-2.md)）

### 3.4 视频 Caption 管线

**Panda-70M 多教师投票法**：
- 输入：3.8M YouTube 高清视频 → 自动切分为 70.8M clips
- 3 位教师：视频 captioner（GIT2）、字幕提取（ASR/OCR）、视频标题
- 交叉投票选出最佳 caption
- 精细标注子集（2.4M clips）通过人工 BLIP-2 重新标注

**CogVideoX 密集标注法**：
- 多教师管线：视觉描述（InternVL2）+ 时序变化（差帧分析）+ 镜头语言
- 输出：长文本密集描述（~100-200 词/clip）
- 用于训练 Expert Transformer 架构

（来源：[Panda-70M](../sources/panda-70m.md)、[CogVideoX](../sources/cogvideox.md)）

### 3.5 数据混合与配比策略

**Cambrian-1 的 Vision-Centric 方法**：
- 从 15 个公开源汇集 980 万样本
- 按 7 个类别标签分类（General VQA / OCR / Reasoning / Chart / Language / Grounding / Conversation）
- 每类设置采样上限（threshold capping），防止单源主导
- 最终精选 700 万，取得比 980 万更好的性能

**LLaVA-OneVision 的阶段隔离法**：
- 每阶段独立配比，下一阶段不重用上一阶段数据
- Stage 2-3 中学术/推理类数据比例逐步提高
- 视频阶段混入 15-20% 单图数据防遗忘

（来源：[Cambrian-1](../sources/cambrian-1.md)、[LLaVA-OneVision](../sources/llava-onevision.md)）

---

## 四、核心发现与趋势

### 4.1 "少而精"胜过"多而杂"

这是贯穿所有论文的最强信号：

| 证据 | 来源 |
|------|------|
| DataComp 14 亿精选 > 128 亿原始 | DataComp |
| Cambrian 700 万精选 > 980 万全量 | Cambrian-1 |
| ShareGPT4V 替换 3.5% 数据即提升全局 | ShareGPT4V |
| DALL-E 3 合成 caption 完胜原始 alt-text | DALL-E 3 |
| Florence-2 迭代精炼 > 一次性大规模标注 | Florence-2 |

### 4.2 合成数据成为主流

2024-2026 年，几乎所有 SOTA 模型都大量使用合成数据：

- **VLM**：合成 caption + 合成对话（ShareGPT4V、ALLaVA）
- **图像生成**：合成 caption 替代 alt-text（DALL-E 3、SD3 50% 合成）
- **视频生成**：多教师合成描述（CogVideoX、Panda-70M）
- **通用基础模型**：迭代自我标注（Florence-2）

### 4.3 多阶段渐进训练成为标准

所有 SOTA 模型都采用 3-5 阶段训练，核心模式：

```
弱标注大规模数据（对齐）
    → 高质量中规模数据（知识注入）
        → 精标注小规模数据（指令调优）
            → 偏好/奖励数据（对齐人类偏好）
```

### 4.4 视觉编码器共享与解耦

- VLM 和 Caption 模型普遍使用 CLIP ViT 系列作为视觉编码器
- 图像生成模型（SD3、DALL-E 3）使用独立的 VAE 编码器
- 视频模型（CogVideoX）使用 3D VAE
- 趋势：视觉编码器从冻结→部分解冻→全量微调（ShareGPT4V 首倡）

### 4.5 数据量级对比总览

| 模型类型 | 预训练数据量 | 微调数据量 | 总训练图像/视频 |
|---------|------------|-----------|--------------|
| VLM (LLaVA-OV) | ~120 万对 | ~600 万对 | ~940 万 |
| VLM (Qwen2-VL) | 未公开（估计 10 亿+） | 未公开 | 未公开 |
| 图像生成 (SD3) | 数十亿对 | 数亿对 | 数十亿 |
| 图像生成 (DALL-E 3) | 未公开（估计 10 亿+） | 未公开 | 未公开 |
| 视频生成 (CogVideoX) | 3500 万 clips | — | 3500 万 |
| Caption (Florence-2) | 1.26 亿图×10 任务 | 子任务微调 | 1.26 亿 |
| 基础数据池 (LAION-5B) | — | — | 58.5 亿对 |

### 4.6 尚未解决的挑战

1. **数据质量度量**：缺乏统一标准衡量"训练数据质量"，各论文使用不同代理指标
2. **数据污染**：训练集与评测集重叠问题日益严重，DataComp 和 LMMs-Eval 均指出此问题
3. **合成数据退化**：连续蒸馏可能导致信息损失（"model collapse"风险）
4. **版权与伦理**：LAION-5B 因版权问题已部分下线，行业需要新的大规模开源数据方案
5. **视频数据成本**：视频 caption 成本约为图像的 10-100 倍，制约视频生成模型发展

---

## 五、方法论建议

基于本次调研，对构建视觉模型训练数据的建议：

### 对 VLM 训练
1. 采用 3-4 阶段渐进训练，每阶段数据精细程度递增
2. 预训练使用 100-300 万高质量合成 caption（而非数十亿噪声 alt-text）
3. SFT 阶段使用 GPT-4V 级合成对话，关键是多样性而非数量
4. 数据混合遵循 category balancing，设置每源上限

### 对图像生成训练
1. 投资训练专用 captioner，用合成 caption 替代原始 alt-text
2. 混合比例：90-95% 合成 caption + 5-10% 真实描述（防幻觉）
3. 多分辨率渐进训练：低分辨率→高分辨率
4. 美学过滤在最后阶段启用

### 对视频生成训练
1. 图像预训练 → 视频微调的两阶段范式已验证有效
2. 视频 caption 采用多教师投票或密集描述方法
3. 高质量短视频（8-15s）> 低质量长视频
4. 时序一致性在数据层面难以保证，需依赖模型架构（3D VAE/temporal attention）

---

## 相关页面

- [视觉模型评测方法全景研究](visual-model-evaluation-methods.md)
- [多模态大模型评测](../concepts/multimodal-evaluation.md)
- [LLaVA-OneVision](../sources/llava-onevision.md)
- [Cambrian-1](../sources/cambrian-1.md)
- [ShareGPT4V](../sources/sharegpt4v.md)
- [Stable Diffusion 3](../sources/stable-diffusion-3.md)
- [DALL-E 3](../sources/dalle3-system-card.md)
- [Florence-2](../sources/florence-2.md)
- [DataComp](../sources/datacomp.md)
- [LAION-5B](../sources/laion-5b.md)
- [CogVideoX](../sources/cogvideox.md)
- [Panda-70M](../sources/panda-70m.md)
- [InternVid](../sources/internvid.md)
- [VBench](../sources/vbench.md)
