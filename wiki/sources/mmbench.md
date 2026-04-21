---
title: "MMBench: Is Your Multi-modal Model an All-around Player?"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [benchmark, multimodal, VLM, evaluation, CircularEval]
sources: [2307.06281v5.pdf]
---

## 概述

MMBench 由上海 AI 实验室（Shanghai AI Lab）Liu 等人提出，是一个系统性评估多模态模型综合能力的基准。该基准包含约 3000 道单选题，覆盖 20 种细粒度能力维度，并创新性地提出 CircularEval 策略以降低模型因选项位置偏好导致的评测随机性。

## 关键发现

- 当前多模态模型在细粒度能力上表现不均衡，部分模型在某些 L-3 叶子能力上存在明显短板
- CircularEval 策略显著提升了评测结果的稳定性和可靠性
- 层级化能力分类体系能够更精确地定位模型的优势与不足
- 开源模型与商用模型之间在多数能力维度上仍存在差距

## 方法论

- **能力分类体系**: 采用三级层级结构
  - L-1（顶层）：感知（Perception）、推理（Reasoning）
  - L-2（中层）：粗粒度感知、细粒度感知、属性推理、关系推理、逻辑推理
  - L-3（叶子层）：20 种具体能力（如物体定位、动作识别、空间关系理解等）
- **题目格式**: 单选题（A/B/C/D），配合图像输入
- **CircularEval 策略**: 对每道题进行多轮评测，每轮将正确答案循环移至不同选项位置，只有所有轮次均回答正确才算通过，有效消除模型的选项位置偏好
- **评测规模**: 约 3000 道经人工筛选和质量控制的题目

## 重要数据

- 约 3000 道单选题
- 20 种细粒度（L-3）能力维度
- 3 级层级化能力分类体系
- CircularEval 通过多轮循环选项位置降低随机性

## 局限性

- 单选题格式限制了对开放式生成能力的评测
- CircularEval 增加了评测的计算成本（每道题需多次推理）
- 能力分类的边界划分存在一定主观性
- 数据集规模相对有限，部分能力维度的题目数量较少

## 相关页面

- [MME](./mme.md)
- [MM-Vet](./mm-vet.md)
- [MathVista](./mathvista.md)
- [LVLM-EHub](./lvlm-ehub.md)
- [VLMEvalKit](./vlmevalkit.md)
