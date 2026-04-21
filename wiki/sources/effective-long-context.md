---
title: "Effective Long-Context Scaling of Foundation Models"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [long-context, Llama, Meta, evaluation, foundation-models]
sources: [2024.naacl-long.260.pdf]
---

## 概述

本文由 Meta 的 Xiong et al. 发表于 NAACL 2024，研究如何有效地将基础模型的上下文窗口从标准长度扩展到 32K tokens。文章基于 Llama 2 模型，通过持续预训练（continual pretraining）和位置编码外推（positional encoding extrapolation）技术，实现了长上下文处理能力的显著提升，并在多种长上下文任务上进行了系统性评测。

## 关键发现

- **持续预训练可有效扩展上下文长度**：在长文本数据上进行额外预训练，能够使模型在保持短上下文能力的同时获得长上下文处理能力
- **RoPE 位置编码缩放是关键技术**：通过调整旋转位置编码（Rotary Position Embedding, RoPE）的频率参数，使模型能处理超出训练时见过的长度的序列
- **长上下文能力不是"免费"的**：需要精心设计训练策略、数据混合比例和学习率调度，否则可能损害短上下文性能
- **在长上下文 benchmark 上表现优异**：扩展后的模型在长文档问答、摘要、检索等任务上表现显著提升
- **模型对上下文中不同位置的信息利用不均**：中间位置的信息容易被忽略（"lost in the middle" 现象）

## 方法论

### 位置编码扩展
- 采用 **RoPE ABF（Adjusted Base Frequency）** 方法
- 调整 RoPE 的基频参数，使位置编码能覆盖更长的序列
- 相比简单的线性插值（linear interpolation），ABF 方法在长距离依赖上表现更好

### 持续预训练策略
- 在原始 Llama 2 预训练权重基础上进行持续训练
- 混合使用短序列和长序列训练数据
- 逐步增加训练序列长度，而非直接跳到目标长度
- 训练数据包含多种长上下文场景：书籍、长文章、代码仓库等

### 评测框架
- **长上下文任务**：
  - 长文档问答（如 NarrativeQA、QuALITY）
  - 长文档摘要
  - 检索增强生成中的长上下文利用
  - "大海捞针"（Needle-in-a-Haystack）检索测试
- **短上下文任务**：在标准 benchmark（如 MMLU、HellaSwag）上验证能力保持
- **上下文长度梯度测试**：测试不同长度（4K、8K、16K、32K）下的表现变化

## 重要数据

- 将 Llama 2 的上下文窗口从 **4K 扩展到 32K tokens**
- 在长上下文 benchmark 上取得显著性能提升
- 在标准短上下文 benchmark 上性能基本保持，degradation 极小
- Needle-in-a-Haystack 测试中，在 32K 长度下检索准确率保持较高水平
- 持续预训练使用了约 **数十亿 tokens** 的长文本数据

## 局限性

- 32K 上下文虽有提升，但与后续出现的 128K+ 上下文模型相比仍有差距
- 基于 Llama 2 进行实验，结论是否能推广到其他架构的模型需要验证
- "lost in the middle" 问题未被完全解决
- 长上下文评测 benchmark 本身可能无法全面反映真实使用场景
- 未讨论推理成本的增加及其在实际部署中的可行性

## 相关页面

- [Meta](../entities/meta.md)
- [HELM 整体性评测](helm.md)
- [LLM 评测综述（Chang et al.）](llm-eval-survey-chang.md)
