---
title: "TTS-PRISM: 感知推理语音诊断模型"
type: source
created: 2026-05-06
updated: 2026-05-06
tags: [语音评测, TTS, 多维度评测, rubric-based, LLM-as-Judge, 中文, 小米]
sources: [raw/papers/tts-prism.pdf, raw/github/tts-prism]
---

## 概述

TTS-PRISM（A Perceptual Reasoning and Interpretable Speech Model for Fine-Grained Diagnosis）是由清华大学与小米 MiLM Plus 联合提出的中文 TTS 多维度诊断评测框架。针对传统 MOS 单一标量无法诊断细粒度声学缺陷的问题，提出 12 维度层次化评测体系 + schema-driven instruction tuning 方法，将 Rubric-Based LLM-as-Judge 范式拓展到语音合成质量评估领域。

- **论文**: arXiv:2604.22225（2026 年 4 月）
- **作者**: Xi Wang, Jie Wang, Xingchen Song, Baijun Song 等
- **机构**: 清华大学、小米 MiLM Plus、东京大学
- **开源**: https://github.com/xiaomi-research/tts-prism（Apache 2.0）
- **模型**: TTS-PRISM-7B（基于 MiMo-Audio backbone）

## 关键要点

### 1. 12 维度层次化评测体系

分为两层：

**Basic Capability Layer（1-5 分，8 维）**：

| 维度 | 评测内容 |
|------|----------|
| Pronunciation Accuracy | 发音准确性（多音字、声调、翘舌/鼻音） |
| Audio Clarity | 音频清晰度（噪声、失真、电子杂音） |
| Intonation | 语调匹配（陈述/疑问/感叹的音高轮廓） |
| Pauses | 停顿合理性（语法边界、时长） |
| Speech Rate | 语速适配（内容匹配度、变速合理性） |
| Speaker Consistency | 说话人一致性（音色、发声方式） |
| Style Consistency | 风格一致性（语域不跳变） |
| Emotion Consistency | 情感一致性（情绪线稳定、转换平滑） |

**Advanced Expressiveness Layer（0-2 分 Bonus，4 维）**：

| 维度 | 评测内容 |
|------|----------|
| Stress | 重音（语义焦点的音高/响度/时长强调） |
| Lengthening | 延长（短语边界/情感词的音节延展） |
| Paralinguistics | 副语言（笑声、叹息、呼吸等非词汇声） |
| Emotion Expression | 情感表达（声学线索传达特定情绪的强度） |

每个维度每个分数等级都有 1-2 句明确的声学标准描述（详见 `Scoring_Criteria.md`）。

### 2. 数据构建：Gemini 机标 + 人工修正

200K 中文样本训练集的标注流程：
- **正样本来源**: 真人录音 + 高质量 TTS（NVSpeech、FireRedTTS-2 定义表现力天花板；CosyVoice 3 等多范式 TTS）
- **负样本来源**: 韵律扰动（不自然停顿、语速突变）、音素替换（同音字、噪声注入）、一致性破坏（音色/情绪拼接）
- **文本领域**: 小说 37.5%、对话 12.8%、文学 20%、新闻 3%、口语 10.9%、其他 11.8%
- **标注方式**: Gemini-2.5-Pro 对每条样本独立标注 12 个维度（每维一次 prompt 调用，防止指令漂移）
- **人工修正**: INSTRUCTSCORE 方法纠正 Stress/Lengthening 维度幻觉；11K 专家标注 "Pronunciation Gold Subset"（声调辨析/多音字）

### 3. Schema-Driven Instruction Tuning

- **Backbone**: MiMo-Audio（小米 7B 音频 LLM，100M 小时无监督预训练）
- **训练目标**: 交替生成序列 `[R1, S1, R2, S2, ..., R12, S12]`（rationale 在 score 之前）
- **核心设计**: Rationale 严格以 scoring criteria 为锚点，而非自由 CoT → 作为逻辑正则器防止幻觉和过拟合数字
- **推理**: 单次前向传播输出全部 12 维评分 + 理由（JSON 格式），无需 12 次独立推理
- **采样策略**: Audio understanding 任务使用 greedy decoding（do_sample=False），保证评分确定性

### 4. 实验结果

**1,600 样本 Gold Test Set**（20% OOD，全人工共识标注）：

| 模型 | 参数量 | Basic Capability (SRCC) | Advanced Expressiveness (SRCC) |
|------|--------|------------------------|-------------------------------|
| Step-Audio-R1 | 33B | 0.423–0.690 | 0.413–0.713 |
| Qwen3-Omni | 30B | 0.150–0.685 | 0.324–0.615 |
| Gemini-2.5-Pro | — | 0.530–0.756 | 0.551–0.807 |
| **TTS-PRISM** | **7B** | **0.492–0.826** | **0.618–0.841** |

TTS-PRISM 在多数维度上超越所有通用模型（含 Gemini-2.5-Pro），仅在 Pronunciation Accuracy 上弱于 Gemini（ASR 预训练的 error-tolerant 偏差难以通过 SFT 消除）。

### 5. TTS 系统诊断画像（Diagnostic Flags）

基于 12 维分数为 6 个主流 TTS 系统赋予直觉化标签：
- F5-TTS → "Stable but Flat"
- CosyVoice 3 → "Paralinguistic-Enhanced"
- MaskGCT → "Prosody-Limited"
- Qwen3-TTS → "Pronunciation-Accurate"
- FireRedTTS-2 → "Balanced"
- IndexTTS2 → "Highly Expressive"

### 6. 消融实验

| 设置 | LCC | SRCC | MSE_norm |
|------|-----|------|----------|
| w/o Negatives | 0.150 | 0.120 | 0.280 |
| w/o Instruction Tuning | 0.320 | 0.302 | 0.118 |
| w/o CoT (rationale) | 0.662 | 0.654 | 0.052 |
| **TTS-PRISM (Full)** | **0.717** | **0.721** | **0.044** |

负样本贡献最大；rationale 生成起逻辑正则化作用。

## 重要发现

1. **Rubric-as-Judge 在语音领域有效**: 将文本域的 Rubric-Based Evaluation 迁移到语音模态，7B 模型即可超越通用大模型
2. **Schema-driven > Free CoT**: 受 scoring criteria 约束的 rationale 比自由 CoT 更稳定（RSC 0.98 vs Qwen3-Omni 0.88）
3. **ASR 预训练偏差难消除**: Pronunciation Accuracy 维度上 Gemini 更强，因为 ASR 预训练优化 error-tolerant mapping，与 TTS 诊断的 defect-discrimination 目标冲突
4. **诊断画像比排名更有价值**: 12 维分数揭示系统的建模优先级差异，而非简单的好/坏排序

## 问题与思考

1. **标注成本与可复制性**: 200K 样本 × 12 次 Gemini API 调用 = 240 万次调用，成本不低；且 Gemini 版本迭代可能影响标注一致性
2. **仅中文**: 当前仅覆盖普通话，跨语言泛化性未验证
3. **Rubric 的主观性**: 虽然有量化标准，但 "Score 4 vs Score 3" 的边界在人类评估中本身存在分歧
4. **训练数据生成代码未开源**: 仓库仅开源推理代码和模型权重，标注管线不可复现
5. **与 Prometheus 的对比缺失**: 未与 Prometheus 系列（同为 Rubric-driven Judge）做对比，可能因模态不同

## 相关页面

- [Rubric-Based 评测](../concepts/rubric-based-evaluation.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [Prometheus](../sources/prometheus.md)
- [Prometheus 2](../sources/prometheus-2.md)
- [小米](../entities/xiaomi.md)
