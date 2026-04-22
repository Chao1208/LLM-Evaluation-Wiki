---
title: "C-Eval: 综合中文基础模型评测套件 (GitHub 仓库)"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [benchmark, 中文评测, 多选题, NeurIPS, 基础模型, C-Eval]
sources: [raw/github/ceval/README.md]
---

## 概述

C-Eval 是一个面向基础模型的综合性中文评测套件，由上海交通大学（SJTU-LIT）等机构开发。该项目包含 **13,948 道多选题**，覆盖 **52 个学科**，设有 **4 个难度级别**，旨在帮助开发者追踪模型进展并分析其在中文场景下的核心优势与不足。

- 论文：[C-Eval: A Multi-Level Multi-Discipline Chinese Evaluation Suite for Foundation Models](https://arxiv.org/abs/2305.08322)
- 发表于 **NeurIPS 2023**
- 数据集托管于 [Hugging Face](https://huggingface.co/datasets/ceval/ceval-exam)
- 官网：[cevalbenchmark.com](https://cevalbenchmark.com/)
- 许可证：代码 MIT，数据 CC BY-NC-SA 4.0
- 2025年7月已向社区释出完整测试集

## 关键特性

1. **学科覆盖全面**：52 个学科横跨 STEM、社会科学、人文、其他四大类别，模拟中国各级考试（高考、考研、职业资格等）
2. **多难度层次**：从基础到高级，涵盖高中、大学、专业等不同难度
3. **C-Eval Hard 子集**：从 8 个高难度数学、物理、化学学科中抽取，涉及复杂 LaTeX 公式和非平凡推理，专门考察高级推理能力
4. **标准化数据拆分**：每个学科包含 dev（5 道含解析的 few-shot 示例）、val（超参调优）、test（模型评测）三个子集
5. **集成 lm-evaluation-harness**：可通过 EleutherAI 的评测框架一键运行（任务名格式 `Ceval-valid-{subject}`）
6. **双评测范式**：支持 answer-only 和 chain-of-thought 两种 prompt 模板

## 评测方法论

- **Zero-shot 和 Five-shot**：提供两种 few-shot 设置，zero-shot 对指令微调模型表现更好
- **答案提取方式**：
  - 正则匹配直接提取 A/B/C/D 答案 token（适用于 few-shot）
  - 概率约束解码：计算 A/B/C/D 的生成概率取最大值（适用于 zero-shot 未经指令微调的模型）
  - 概率方法不适用于 chain-of-thought 场景
- **中文 prompt**：`以下是中国关于{科目}考试的单项选择题，请选出其中的正确答案。`

## 支持的模型/基准

初始发布的评测覆盖以下模型：

| 模型 | Zero-shot 均分 | Five-shot 均分 |
|------|:-:|:-:|
| GPT-4 | 66.4 | 68.7 |
| ChatGPT | 51.0 | 54.4 |
| Claude-v1.3 | 50.5 | 54.2 |
| Bloomz-mt-176B | 44.3 | 39.0 |
| GLM-130B | 44.0 | 40.3 |
| ChatGLM-6B | 38.9 | 34.5 |
| LLaMA-65B | 34.7 | 38.8 |

**C-Eval Hard 成绩**（最高 GPT-4: zero-shot 53.3, five-shot 54.9），凸显当前模型在复杂中文推理任务上仍有较大提升空间。

## 技术架构

- 数据格式：CSV，每条包含 id、question、A/B/C/D 选项、answer、explanation 字段
- 学科映射：`subject_mapping.json` 提供学科 handler 到（英文名、中文名、大类）的映射
- 支持 Hugging Face `datasets` 库直接加载
- 可通过 lm-evaluation-harness 框架集成评测，支持 HuggingFace Hub 上的模型

## 与评测相关的启示

1. **中文评测基准的重要参考**：C-Eval 是中文 LLM 评测领域最有影响力的 benchmark 之一，与 [MMLU](../sources/mmlu.md) 形成中英对照
2. **Rubric 设计角度**：C-Eval 采用客观多选题形式，评分完全确定（无需 [LLM-as-Judge](../concepts/llm-as-judge.md)），适合大规模自动化评测
3. **Few-shot 对指令微调模型的反效果**：zero-shot 在指令微调模型上优于 five-shot，说明 prompt 策略需因模型而异
4. **C-Eval Hard 的价值**：揭示了当前模型在中文高级推理（数学、物理、化学）上的瓶颈，为模型改进提供方向
5. **概率解码 vs 生成解码**：两种评测方式可能产生不同结果，评测报告中需明确说明采用何种方法
6. **与其他中文评测互补**：C-Eval 侧重知识型考试评测，与 [CLUE](../sources/clue-repo.md)（NLU 任务）、[SuperCLUE](../sources/superclue-repo.md)（综合能力）形成互补

## 相关页面

- [MMLU](../sources/mmlu.md) -- 英文多任务语言理解基准，C-Eval 的英文对标
- [AGIEval](../sources/agieval.md) -- 另一个基于真实考试的评测基准（中英双语）
- [LLM-as-Judge](../concepts/llm-as-judge.md) -- LLM 自动评审方法论
- [Chain-of-Thought Prompting](../sources/chain-of-thought.md) -- C-Eval 支持的 CoT 评测模式
- [GPT-4 Technical Report](../sources/gpt4-technical-report.md) -- C-Eval 排行榜上表现最佳的模型
- [CLUE benchmark](../sources/clue-repo.md) -- 中文语言理解评测基准（NLU 任务）
- [SuperCLUE](../sources/superclue-repo.md) -- 中文通用大模型综合评测
