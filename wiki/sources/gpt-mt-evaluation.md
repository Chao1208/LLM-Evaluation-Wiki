---
title: "Is ChatGPT A Good Translator? Yes With GPT-4 As The Engine"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [evaluation, machine-translation, GPT, ChatGPT, multilingual]
sources: [2302.09210v1.pdf]
---

## 概述

该论文由 Jiao 等人（Microsoft）于 2023 年发表，系统评估了 GPT 系列模型（ChatGPT / GPT-4）的机器翻译能力。研究跨越多个语言对，将 GPT 模型与 Google Translate、DeepL 等商用翻译系统进行对比，使用 BLEU 和 ChrF++ 等标准指标衡量翻译质量，揭示了大语言模型在翻译任务上的潜力与不足。

## 关键发现

- GPT-4 作为翻译引擎时，在多数语言对上表现接近甚至超越商用翻译系统
- ChatGPT（GPT-3.5）的翻译质量与专用翻译系统仍有差距，尤其在低资源语言上
- 高资源语言对（如英-德、英-中）上 GPT 模型表现更好，低资源语言对差距明显
- Prompt 设计对翻译质量有显著影响
- GPT 模型在需要上下文理解的翻译场景中展现优势

## 方法论

- **评测模型**: ChatGPT（GPT-3.5-turbo）、GPT-4
- **对比系统**: Google Translate、DeepL 等商用机器翻译系统
- **语言对**: 涵盖多个语言对，包括高资源和低资源方向
- **评测指标**:
  - BLEU：基于 n-gram 精确率的翻译质量指标
  - ChrF++：基于字符级 n-gram 的指标，对形态丰富语言更友好
- **评测数据**: 使用 WMT 等标准机器翻译测试集
- **Prompt 策略**: 探索不同 prompt 设计对翻译质量的影响

## 重要数据

- 多个语言对的对比评测
- BLEU 和 ChrF++ 双指标评估
- GPT-4 在高资源语言对上接近商用翻译系统水平
- ChatGPT 在低资源语言上表现较弱

## 局限性

- 评测时点的模型版本可能已迭代，结论的时效性有限
- BLEU 和 ChrF++ 等自动指标无法完全反映翻译的流畅度和地道性
- 未进行大规模人类评估
- 翻译场景局限于句子级，未充分测试文档级翻译能力
- 低资源语言的测试数据量有限
- 未考虑专业领域（如法律、医学）翻译的表现

## 相关页面

- [OpenAI](../entities/openai.md)
