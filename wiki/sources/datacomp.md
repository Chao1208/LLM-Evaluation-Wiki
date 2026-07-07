---
title: "DataComp: In Search of the Next Generation of Multimodal Datasets"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [dataset, benchmark, multimodal, CLIP, data-filtering, data-centric-AI, NeurIPS-2023, image-text]
sources: [datacomp.pdf]
---

## 概述

本文由 Samir Yitzhak Gadre、Gabriel Ilharco、Alex Fang 等来自 University of Washington、Columbia University、Tel Aviv University、Apple、UT Austin、LAION、AI2 等机构的研究者于 2023 年发表于 NeurIPS 2023 Datasets and Benchmarks Track。DataComp 是一个面向多模态数据集设计的 benchmark，颠覆了传统 benchmark "固定数据集、比模型"的范式，转为"固定训练代码和计算预算、比数据集"。其核心贡献包括：一个 12.8B image-text pairs 的候选池 CommonPool、一个标准化的 CLIP 训练和评估流程（38 个下游任务）、以及最终产出的高质量数据集 DataComp-1B。

## 关键要点

### 数据集构建

- **CommonPool**：从 Common Crawl 中提取的 12.8B image-text pairs 候选池，是目前最大的公开 image-text 数据集
- 构建流程包括四步：URL 提取与下载（~88B 候选 → ~16.8B 下载）、NSFW 检测与去除（~19% 被标记为 NSFW，降至 ~13.6B）、评估集去重（~3% 为近重复，降至 ~13.1B）、最终随机采样得到 12.8B 的 xlarge pool
- 采用 CC-BY-4.0 许可发布为 URL 索引形式
- 提供每个样本的元数据：image URL、alt-text、原始分辨率、CLIP features、CLIP similarity scores

### 规模与计算

- 四个计算规模（small/medium/large/xlarge），候选池从 12.8M 到 12.8B，训练预算从 12.8M 到 12.8B samples seen
- 模型架构固定：small 用 ViT-B/32，medium 用 ViT-B/32，large 用 ViT-B/16，xlarge 用 ViT-L/14
- GPU 小时：small 约 4 小时（1 x A100），xlarge 约 81 小时（512 GPUs）

### 方法论

- **两个竞赛 track**：
  1. **Filtering track**：从 CommonPool 中选子集训练
  2. **BYOD（Bring Your Own Data）track**：可使用任意外部数据源
- **评估**：38 个 zero-shot 图像分类和检索任务，包括 ImageNet、ImageNet-V2、DTD、EuroSAT、SUN-397、MSCOCO 等
- **Baseline 过滤方法**：无过滤、随机子集、基础过滤（语言+caption 长度+图片大小）、CLIP score 过滤、text-based 过滤、image-based 过滤

## 重要发现

### 过滤效果

| 过滤策略 (xlarge) | 数据集大小 | ImageNet Acc | 38任务平均 |
|---|---|---|---|
| No filtering | 12.8B | 0.612 | 0.621 |
| LAION-2B filtering | 1.3B | 0.637 | 0.636 |
| CLIP score (L/14 30%) | 3.8B | 0.655 | 0.650 |
| Image-based ∩ CLIP score (L/14 30%) | 1.4B | **0.679** | **0.663** |

- **CLIP score 过滤是最有效的单一策略**：在所有规模上均显著优于不过滤的 baseline
- **Image-based ∩ CLIP score 的交集策略**在大多数规模和任务上表现最佳
- 更严格的过滤 → 更小但更好的数据集：小且高质量的子集优于大而未过滤的全集
- CLIP score 过滤的最优选取比例约为 30%（选取 pool 中 CLIP score 最高的 30%）

### DataComp-1B 结果

| Dataset | 大小 | 架构 | ImageNet Acc |
|---|---|---|---|
| OpenAI's WIT | 0.4B | ViT-L/14 | 75.5 |
| LAION-2B | 2.3B | ViT-L/14 | 73.1 |
| **DataComp-1B** | **1.4B** | **ViT-L/14** | **79.2** |

- DataComp-1B 以仅 1.4B 样本（比 LAION-2B 更小）实现了 79.2% ImageNet zero-shot accuracy
- 超过 OpenAI 原始 CLIP ViT-L/14 达 3.7 个百分点
- 相比在 LAION-2B 上训练更大的 ViT-g/14 模型，DataComp-1B 实现了 9x 的计算成本降低

### 跨规模一致性

- 不同过滤策略的排名在各规模间高度一致（rank correlation 0.71-0.90）
- small/medium 规模的实验可以有效预测 large/xlarge 规模的效果

### BYOD Track

- 添加外部数据源（CC12M、YFCC15M、RedCaps、Shutterstock）可在 large 规模上提升 ImageNet accuracy 4.3 个百分点
- 仅 109M 额外数据点（不到 pool 的 1%）即可提升 CLIP 过滤子集 1.2pp

## 问题与思考

1. **Data-centric AI 的里程碑**：DataComp 将数据集设计从"一次性工程"提升为"可量化比较的科学问题"，这对整个 ML 社区都有深远影响
2. **CLIP score 过滤的局限**：使用 CLIP 模型本身来过滤训练数据可能引入偏差——过滤后的数据偏向 CLIP 已经"理解"的内容，可能限制模型学到新知识的能力
3. **安全与伦理**：尽管进行了 NSFW 过滤和面部模糊处理，从公共互联网收集数据仍存在隐私、有害内容和偏见问题
4. **评测局限**：仅使用 ImageNet accuracy 作为主要指标的相关性为 0.99，但在个别任务上可能呈负相关，需要更多元的评估视角
5. **对 LLM 评测的启示**：DataComp 的"固定模型比数据"思路可以迁移到 LLM 领域——为 instruction tuning 数据集建立类似的 benchmark 将非常有价值

## 相关页面

- [LAION-5B](../sources/laion-5b.md)
- [CLIP](../concepts/clip.md)
