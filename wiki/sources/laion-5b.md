---
title: "LAION-5B: An Open Large-Scale Dataset for Training Next Generation Image-Text Models"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [dataset, multimodal, CLIP, image-text, open-source, large-scale, NeurIPS-2022, Common-Crawl]
sources: [laion-5b.pdf]
---

## 概述

本文由 Christoph Schuhmann、Romain Beaumont、Richard Vencu、Cade Gordon、Ross Wightman、Mehdi Cherti 等来自 LAION、UC Berkeley、Gentec Data、TU Darmstadt、Hessian.AI、University of Washington、Technical University of Munich、Stability AI、EleutherAI、Juelich Supercomputing Center 等机构的研究者于 2022 年发表于 NeurIPS 2022 Datasets and Benchmarks Track。LAION-5B 是一个包含 5.85 billion CLIP-filtered image-text pairs 的开放数据集，是当时最大的公开多模态数据集，旨在为研究社区提供训练大规模视觉-语言模型（如 CLIP、Stable Diffusion、GLIDE）所需的数据基础设施。

## 关键要点

### 数据集构建

- **数据来源**：Common Crawl（公共网络爬虫，自 2008 年运行，每月快照约 300 TiB / 30 亿网页）
- **提取方式**：从 Common Crawl 的 WAT 元数据文件中解析 HTML IMG 标签的 alt-text，构成 image-text pairs
- **语言检测**：使用 CLD3（Compact Language Detector 3）将文本分为 English、其他语言、无法检测三类
- **下载**：使用 Trio 和 Asks Python 库进行异步下载，约 300 个 worker 并行，每个 worker 处理 10,000 条 URL
- **后处理过滤**：去除少于 5 字符的文本、小于 5KB 的图片、以及潜在恶意/冗余图片

### 规模

- **总规模**：5.85 billion image-text pairs（从原始约 50 billion 图片中过滤得到）
- **三个子集**：
  - LAION-2B-en：2.32 billion 英文 image-text pairs
  - LAION-2B-multi：2.26 billion 多语言 pairs（覆盖 100+ 语言，top-5：俄语 10.6%、法语 7.4%、德语 6.6%、西班牙语 6.6%、中文 6.3%）
  - LAION-1B-nolang：1.27 billion 无法检测语言的 pairs（主要是产品名、地名等）
- **对比**：比此前最大的公开英文 image-text 数据集大 20 倍以上（此前最大为 YFCC100M 的 ~15M 有效 pairs）

### CLIP 过滤方法论

- 使用 OpenAI 的 ViT-B/32 CLIP 模型计算 image-text cosine similarity
- **英文样本**：cosine similarity < 0.28 被移除
- **非英文样本**：使用多语言 CLIP ViT-B/32，cosine similarity < 0.26 被移除
- 该过滤步骤移除了约 90% 的原始数据（从 ~50B 降至 ~6B）

### 安全与内容标注

- 使用 Q16 分类器和自建 NSFW 分类器（基于 CLIP embeddings）标记不当内容
- NSFW 检测结果：约 3% 图片被标记为 NSFW
- 提供 watermark 检测分数
- 不删除而是标记潜在问题内容，让用户自行决定是否使用
- **强烈建议仅用于学术研究目的**

### 元数据格式

每个 image-text pair 提供 Apache Parquet 格式的元数据：
- 64-bit 整数标识符
- 图片 URL
- 文本字符串
- 图片高度和宽度
- cosine similarity 分数
- NSFW 和 watermark 检测分数

## 重要发现

### CLIP 复现实验

| Model | Pre-training | ImageNet (INet) | INet-v2 | INet-R | INet-S | ObjNet | VTAB+ |
|---|---|---|---|---|---|---|---|
| B/32 | CLIP WIT | 63.3 | 56.0 | 69.4 | 42.3 | 44.2 | 45.4 |
| B/32 | LAION-400M | 62.9 | 55.1 | 73.4 | 49.4 | 43.9 | 45.6 |
| B/32 | LAION-2B-en | 65.7 | 57.4 | 75.9 | 52.9 | 48.7 | 47.9 |
| L/14 | CLIP WIT | 75.6 | 69.8 | 87.9 | 59.6 | 69.0 | 55.7 |
| L/14 | LAION-400M | 72.8 | 65.4 | 84.7 | 59.6 | 59.9 | 51.8 |
| L/14 | LAION-2B-en | 75.2 | 67.7 | 87.4 | 63.3 | 65.5 | 54.6 |

- 在 LAION-400M 上训练的 CLIP 模型与 OpenAI 原始 CLIP 性能匹配或接近
- LAION-2B-en 的更大规模在 ViT-B/32 和 ViT-L/14 上均带来一致提升
- 首次实现 OpenAI CLIP 的开源复现（ViT-L/14 模型）

### 缩放行为

- 在 VTAB+（35 任务平均）上观察到 log-log 线性的 scaling behavior
- 同时增加模型规模和数据规模都能提升 zero-shot 性能
- 训练 ViT-L/14 于 LAION-2B-en（75.4%）优于训练于 LAION-400M（73.9%），在相同 compute 下

### 生成模型验证

- Stable Diffusion 使用 LAION-5B 子集（LAION-2B-en、laion-high-resolution、laion-aesthetics）成功训练
- GLIDE 在 LAION-5B 数据上 fine-tune 后产生可比较的生成质量
- LAION-Aesthetic（120M 子集）专门支持审美质量较高的图像生成

## 问题与思考

1. **开放数据的民主化价值**：在 LAION-5B 发布前，所有用于训练 CLIP 级别模型的大规模数据集都是封闭的（OpenAI WIT 400M、Google ALIGN 1.8B、BASIC 6.6B）。LAION-5B 使得学术界首次能够复现这些实验
2. **数据质量 vs 规模的权衡**：CLIP 过滤移除了 90% 的数据，说明网络爬取的原始数据噪声极高。但 cosine similarity 0.28 的阈值是否最优？DataComp 后续研究表明更精细的过滤策略可进一步提升质量
3. **安全与伦理隐患**：
   - Alt-text 质量参差不齐（可能是 SEO 垃圾或与图片无关的文本）
   - 使用 CLIP 过滤本身会放大模型偏见
   - 尽管标记了 NSFW，但仍有未检测到的有害内容
   - 作者明确声明"仅限学术研究使用"
4. **对下游的深远影响**：LAION-5B 直接支撑了 Stable Diffusion 的训练，间接推动了整个生成式 AI 的开源生态。但同时也引发了关于训练数据中版权内容的法律争议
5. **与 DataComp 的关系**：DataComp 的 CommonPool（12.8B）在 LAION-5B 的基础上进一步扩大了规模，并通过更精细的过滤（DataComp-1B）证明了数据质量优先于数据数量的原则

## 相关页面

- [DataComp](../sources/datacomp.md)
- [CLIP](../concepts/clip.md)
