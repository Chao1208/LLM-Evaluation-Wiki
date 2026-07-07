---
title: "VBench: Comprehensive Benchmark Suite for Video Generative Models"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [视频生成, benchmark, 多维度评测, 人类偏好, 自动化评测, 视频质量]
sources: [vbench.pdf]
---

## 概述

VBench（S-Lab, NTU & Shanghai AI Lab & CUHK, CVPR 2024）是一个面向视频生成模型的综合性评测基准，将"视频生成质量"这一宽泛概念解构为 **16 个细粒度、层次化、解耦的评测维度**，每个维度配有专门设计的 prompt suite 和自动化评测方法。VBench 具备三大特性：1) 全面的评测维度覆盖；2) 评测结果与人类感知高度对齐；3) 提供有价值的洞察指导模型开发。

项目开源于 https://vchitect.github.io/VBench-project/

## 关键要点

### 16 个评测维度

VBench 从两个顶层维度展开：

**Video Quality（视频质量）—— "不考虑文本对齐，视频本身是否好看？"**

Temporal Quality（时间质量）:
1. **Subject Consistency** — 主体外观跨帧一致性（DINO 特征相似度）
2. **Background Consistency** — 背景场景跨帧一致性（CLIP 特征相似度）
3. **Temporal Flickering** — 时间闪烁程度（帧间像素差的 MAE）
4. **Motion Smoothness** — 运动平滑性（视频帧插值模型 AMT 重建误差）
5. **Dynamic Degree** — 动态程度（RAFT 光流强度，非静态视频占比）

Frame-Wise Quality（帧级质量）:
6. **Aesthetic Quality** — 美学质量（LAION aesthetic predictor）
7. **Imaging Quality** — 成像质量（MUSIQ 图像质量评估器）

**Video-Condition Consistency（视频-条件一致性）—— "视频是否与用户意图一致？"**

Semantics（语义）:
8. **Object Class** — 物体类别正确性（GRiT 检测）
9. **Multiple Objects** — 多物体组合能力（GRiT 检测）
10. **Human Action** — 人体动作准确性（UMT 动作分类）
11. **Color** — 颜色准确性（GRiT 描述比对）
12. **Spatial Relationship** — 空间关系正确性（GRiT + IoU 规则）
13. **Scene** — 场景一致性（Tag2Text 场景标注比对）

Style（风格）:
14. **Appearance Style** — 外观风格一致性（CLIP 特征相似度）
15. **Temporal Style** — 时间风格一致性（ViCLIP 视频-文本相似度）
16. **Overall Consistency** — 整体一致性（ViCLIP 总体视频-文本一致性）

### Prompt Suite 设计

- 每个评测维度约 **100 个精心设计的 prompt**（总计约 946 个）
- 按 8 个内容类别额外组织 prompt suite：Animal, Architecture, Food, Human, Lifestyle, Plant, Scenery, Vehicles
- Prompt 设计关注高效性和代表性，避免耗时的大规模采样

### 人类偏好对齐验证

- 对每个维度收集人类偏好标注（4 个模型 x N prompt x 5 组 x 6 配对 = 大量两两比较）
- VBench 自动评分的 win ratio 与人类偏好 win ratio 在所有 16 个维度上呈现**高相关性**（Spearman 相关系数普遍 > 0.9）

## 重要发现

### 模型评测结果（LaVie, ModelScope, VideoCrafter, CogVideo）

| 维度 | LaVie | ModelScope | VideoCrafter | CogVideo |
|------|-------|------------|--------------|----------|
| Subject Consistency | 91.41% | 89.87% | 86.24% | **92.19%** |
| Background Consistency | **97.47%** | 95.29% | 92.88% | 95.42% |
| Temporal Flickering | **98.80%** | 98.28% | 97.60% | 97.64% |
| Motion Smoothness | 96.38% | **95.79%** | 91.79% | **96.47%** |
| Dynamic Degree | 49.72% | 66.39% | **89.72%** | 42.22% |
| Aesthetic Quality | **54.94%** | 52.06% | 44.41% | 38.18% |
| Object Class | **91.82%** | 82.25% | 87.34% | 73.40% |
| Color | **86.39%** | 81.72% | 78.84% | 79.57% |

### 关键洞察

1. **时间一致性与动态程度的 trade-off**：Subject Consistency、Background Consistency 高的模型通常 Dynamic Degree 低（如 LaVie 生成近似静态视频"作弊"），而 VideoCrafter Dynamic Degree 高但时间一致性差
2. **内容类别差异显著**：同一模型在不同内容类别（Food vs Human）表现差异巨大，建议按类别分别评估
3. **数据质量 > 数据数量**：Food 类别只占 WebVid-10M 的 11% 但 Aesthetic Quality 最高，Human 占 26% 但表现差
4. **T2V vs T2I 差距**：视频生成模型在 Multiple Objects 和 Spatial Relationship 上显著逊于 T2I 模型（尤其是 SDXL），凸显组合性能力不足
5. **模型隐藏潜力**：CogVideo 在 Food 类别 Aesthetic Quality 突出，暗示合适数据策略可释放潜力

## 评测方法论特色

- **层次化分解**：将模糊的"视频质量"解构为可独立测量的细粒度维度
- **维度解耦**：每个维度的 prompt 和评测方法专门设计，避免维度间干扰
- **参考基线**：提供 Empirical Max（WebVid-10M 最佳视频）、Empirical Min（高斯噪声/拼接帧）、WebVid-Avg 三个参考基线
- **可扩展性**：框架可持续纳入新模型，形成开放排行榜

## 问题与思考

1. **评测维度完备性**：16 个维度是否覆盖了视频质量的全部方面？安全性、多样性等未纳入
2. **维度权重问题**：不同应用场景对各维度的重视程度不同，如何给出综合分数？
3. **自动化工具的局限**：GRiT、CLIP、DINO 等工具本身存在误差，是否会引入系统性偏差？
4. **Prompt 覆盖**：每维度约 100 条 prompt 是否足够代表真实用户需求？
5. **动态视频评测**：当前评测主要基于帧级特征，对长时间依赖和复杂叙事的捕获能力有限
6. **与 ImageReward/HPSv2 等 preference model 的互补**：VBench 侧重客观维度分解，preference model 侧重主观整体偏好，二者结合可能更全面

## 相关页面

- [CLIP](../entities/clip.md)
- [FID 指标](../concepts/fid.md)
- [视频生成评测](../concepts/video-generation-evaluation.md)
