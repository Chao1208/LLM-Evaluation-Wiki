---
title: "小米（Xiaomi）"
type: entity
entity_type: organization
created: 2026-05-06
updated: 2026-05-06
tags: [小米, Xiaomi, MiLM, MiMo-Audio, TTS]
sources: [raw/papers/tts-prism.pdf, raw/github/tts-prism]
---

# 小米（Xiaomi）

## 基本信息

- **全称**: Xiaomi Corporation / 小米集团
- **AI 研究部门**: MiLM Plus（小米大模型团队）
- **评测相关产品**: TTS-PRISM、MiMo-Audio

## 评测领域贡献

### TTS-PRISM（2026）

与清华大学合作提出的中文 TTS 多维度诊断评测框架：
- 12 维层次化评测体系（Basic Capability 8 维 + Advanced Expressiveness 4 维）
- Schema-driven instruction tuning：将评分标准嵌入模型训练
- 7B 模型超越 Gemini-2.5-Pro 等通用模型的人类对齐度
- 开源代码和模型权重（Apache 2.0）

（来源：[TTS-PRISM](../sources/tts-prism.md)）

### MiMo-Audio

小米自研的多模态音频大语言模型（7B 参数），100M 小时无监督预训练：
- 支持 ASR、TTS、语音对话、音频理解等多任务
- 使用 MiMo-Audio-Tokenizer 将音频编码为多通道离散 token
- 作为 TTS-PRISM 的 backbone，经 SFT 后用于语音质量诊断

## 相关页面

- [TTS-PRISM](../sources/tts-prism.md)
- [Rubric-Based 评测](../concepts/rubric-based-evaluation.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
