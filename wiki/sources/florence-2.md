---
title: "Florence-2: Advancing a Unified Representation for a Variety of Vision Tasks"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [视觉基础模型, 多任务学习, 数据引擎, sequence-to-sequence, Microsoft, 目标检测, 图像描述]
sources: [florence-2.pdf]
---

## 概述

本文由 Microsoft Azure AI 团队发表（arXiv:2311.06242，2023年11月），提出 Florence-2，一个新型视觉基础模型，采用统一的 prompt-based sequence-to-sequence 架构处理多种视觉任务（captioning、目标检测、grounding、分割等）。核心创新是共同开发了 **FLD-5B** 数据集：包含 **126M 图像**上的 **5.4B 综合视觉标注**（跨越 10+ 任务类型），通过自动数据引擎（specialist 模型标注 + 数据过滤 + 迭代模型精炼）生成。Florence-2 仅用 0.23B/0.77B 参数即在 zero-shot 和 fine-tuned 设置下均展现出强竞争力，甚至超越参数量大 100 倍的专用模型。

## 关键要点

### 训练数据 (FLD-5B)

**数据规模**：
- **126M 图像**，来自 ImageNet-22k、Object 365、Open Images、Conceptual Captions、LAION（经过滤）
- **500M 文本标注**（brief/detailed/more detailed 三种粒度）
- **1.3B 区域-文本标注**（region-text pairs）
- **3.6B 文本-短语-区域标注**（text-phrase-region triplets）
- 总计超过 **5B 标注**

**标注类型详细统计**（Table 2）：

| 标注类型 | 文本类型 | 图像数 | 平均 Token 数 | 区域数 | 平均区域数 |
|---------|---------|--------|-------------|-------|---------|
| Text | Brief | 235M | 7.95 | - | - |
| Text | Detailed | 126M | 31.65 | - | - |
| Text | More detailed | 126M | 70.53 | - | - |
| Region-Text | Phrase | 126M | - | 681M | 5.42 |
| Region-Text | Brief | 126M | - | 681M | 5.42 |
| Text-Phrase-Region | Brief | 235M | 7.95 | 1007M | 4.27 |
| Text-Phrase-Region | Detailed | 126M | 31.65 | 1289M | 10.25 |
| Text-Phrase-Region | More detailed | 126M | 70.53 | 1278M | 10.17 |

**数据引擎三阶段**：
1. **Specialist 模型初始标注**：使用多个离线专用模型（Azure OCR、Caption Model、Grounding Model、Object/Proposal Detector、Segmentation Model）和在线服务协同标注
2. **数据过滤与增强**：文本端用 SpaCy + DiHT 解析，过滤含过多对象的噪声文本；区域端通过置信度阈值 + NMS 去除噪声框
3. **迭代数据精炼**：用初步过滤数据训练多任务模型，再用该模型对原始数据进行增强标注，循环迭代提升质量

**与其他数据集对比**（Table 1）：

| 数据集 | 代表模型 | 图像数 | 标注数 | 空间层级 | 语义粒度 |
|-------|---------|--------|-------|---------|---------|
| JFT300M | ViT | 300M | 300M | Image-level | Coarse |
| WIT | CLIP | 400M | 400M | Image-level | Coarse |
| SA-1B | SAM | 11M | 1B | Region-level | Non-semantic |
| GrIT | Kosmos-2 | 91M | 137M | Image & Region | Fine-grained |
| **FLD-5B** | **Florence-2** | **126M** | **5B** | **Image & Region** | **Coarse to fine-grained** |

### 方法论

**模型架构**：
- Sequence-to-sequence 结构：Image Encoder + Multi-modality Encoder-Decoder
- **Vision Encoder**：DaViT（Dual Attention Vision Transformer），将图像映射为 visual token embeddings
- **Multi-modality Encoder-Decoder**：标准 Transformer encoder-decoder，处理 visual + language token embeddings
- 统一优化目标：标准语言建模 cross-entropy loss（对所有任务相同）

**任务统一**：
- 所有任务形式化为 translation 问题：给定图像 + task-specific prompt → 生成对应文本/坐标输出
- **文本输出**：直接序列化为 plain text
- **区域输出**：扩展 tokenizer 词表加入 1000 个 location bins（量化坐标），支持 box、quad box、polygon 三种表示
- 覆盖 13 种预训练任务：caption（3种粒度）、region proposal、object detection、dense region caption、phrase grounding、referring expression comprehension、open vocabulary detection、referring segmentation、region to text、text detection/recognition

**三类学习目标**：
1. **Image-level understanding**：图像分类、captioning → 高层语义理解
2. **Region/pixel-level recognition**：目标检测、分割、referring expression → 精细定位
3. **Fine-grained visual-semantic alignment**：phrase grounding → 文本短语与图像区域的对齐

**模型规模**：
- Florence-2-B：232M 参数（DaViT 90M + Encoder-Decoder 140M）
- Florence-2-L：771M 参数（DaViT 360M + Encoder-Decoder 410M）
- 初始化：Vision encoder 从 UniCL，Encoder-decoder 从 BART
- 训练：AdamW + cosine LR decay，batch size 2048/3072，图像 384x384，训练至 3B effective samples
- 高分辨率 tuning：768x768，0.5B/0.1B samples（base/large）

### 评测

**Zero-shot 性能**（Table 4）：
- COCO caption：Florence-2-L 达 135.6 CIDEr（Karpathy test），参数量不到 80B Flamingo 的 1%（Flamingo 84.3 CIDEr）
- Flickr30k grounding：zero-shot Recall@1 = 84.4，超越 1.6B 参数的 Kosmos-2（78.7）
- RefCOCO/RefCOCO+/RefCOCOg：比 Kosmos-2 分别提升约 4%/8%/8%
- Referring expression segmentation (RES)：35.8% mIOU，此前基础模型未支持此任务

**Generalist 模型性能**（Table 5，fine-tuned）：
- COCO Caption Karpathy：Florence-2-L 达 143.3 CIDEr，超越 80B Flamingo (138.1) 和 55B PaLI-X (149.2 但使用外部 OCR)
- TextVQA：73.5% accuracy，无需外部 OCR 输入即达 SOTA
- VQAv2：81.7%，接近 17B PaLI (84.3) 水平

**Region-level 任务**（Table 6，fine-tuned generalist）：
- COCO Detection val2017 mAP：43.4（超越 UniTAB 的 41.4）
- Flickr30k test R@1：85.2（超越 UNINEXT 的 60.6）
- RefCOCO test-A/test-B Accuracy：95.3/92.0
- RefCOCO RES val mIOU：78.0（首次 generalist 模型达此水平）

**作为 backbone 的迁移能力**（Table 7-9）：
- COCO Object Detection (Mask R-CNN)：DaViT-B + Florence-2 预训练达 53.6/46.4 AP，超越所有对比方法
- DINO framework：59.2 AP，超越 ViT-B (55.0) 4.2 个点
- ADE20K Semantic Segmentation：54.9/55.5 mIOU，超越 FCMAE (52.1) 和 BEiTv2 (54.1)
- **4x 训练效率提升**：相比 ImageNet-1k 预训练，Florence-2 预训练在 Mask-RCNN 和 DINO 上分别提升 6.9/5.5 AP，且仅需 1/4 的训练 epoch

**Model/Data Scaling**（Table 10-11）：
- Large 模型 (0.77B) 全面优于 Base 模型 (0.23B)：caption +5.7 CIDEr, detection +2.9 AP, grounding +1.9 R@1
- 数据从 0.12M 扩展到 12M：caption 从 102.8 提升到 118.7 CIDEr, detection 从 16.1 到 19.7 AP

### 消融实验

**多任务迁移**：预训练包含 image + region + pixel 三个层级任务的模型，在所有下游任务上一致优于仅含 image-level 或 image+region-level 的模型

**训练设置**（Table 12）：
- 冻结 vision encoder 严重损害 region/pixel 级任务（detection AP 从 19.7 降到 6.9）
- 使用语言预训练权重初始化 encoder-decoder 有助于需要文本理解的任务（如 captioning、grounding）

## 重要发现

1. **大规模自动标注数据引擎是可行的**：通过 specialist 模型协同 + 迭代精炼，可以低成本构建比人工标注更大规模且质量可控的数据集
2. **统一 seq2seq 架构能处理所有视觉任务**：无需 task-specific head，通过 prompt 区分任务，单一模型即可 handle classification、detection、captioning、grounding、segmentation
3. **小模型 + 好数据 > 大模型 + 差数据**：0.77B 的 Florence-2 在多个 zero-shot 任务上超越 80B Flamingo，体现了数据质量和多样性的重要性
4. **多层级预训练的迁移价值**：在 image + region + pixel 三个层级联合预训练的表征，在所有粒度的下游任务上都表现最好
5. **Vision encoder 必须 unfreeze**：region/pixel 级任务需要 backbone 学习空间精确的特征，冻结会导致严重性能下降

## 问题与思考

- FLD-5B 的迭代精炼策略（用模型标注数据 → 训练更好模型 → 再标注）与 DALL-E 3 的 captioner 训练思路相似，反映了"synthetic data loop"在多模态 AI 中的普遍趋势。
- Florence-2 的 location token（1000 bins 量化坐标）方案简洁但可能限制了空间精度。对于高分辨率图像，1000 bins 意味着每个 bin 约对应 1-2 个像素，这在精细分割任务中可能成为瓶颈。
- 模型使用 DaViT 而非更流行的 ViT/Swin 作为 vision encoder，这一选择的消融未充分展开。
- 论文的评测非常全面（zero-shot + generalist fine-tune + backbone transfer + ablations），为如何评估多任务基础模型提供了范式参考。
- Florence-2 的 compact size (0.23B/0.77B) 使其非常适合作为视觉理解的基础组件嵌入到更大系统中（如为 diffusion 模型提供 caption/detection 能力）。

## 相关页面

- [DALL-E 3 System Card](dalle3-system-card.md)
- [Stable Diffusion 3](stable-diffusion-3.md)
