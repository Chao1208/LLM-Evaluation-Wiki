---
title: "中文评测基准"
type: concept
created: 2026-04-22
updated: 2026-04-22
tags: [中文评测, C-Eval, CLUE, SuperCLUE, AGIEval, benchmark]
sources: []
---

## 概述

中文评测基准是专门针对中文语言模型能力设计的评测体系。由于中文在语言结构、文化背景和应用场景上的独特性，通用英文 benchmark 无法充分评估模型的中文能力，因此需要专门的中文评测基准。

中文评测面临的核心挑战包括：

- **语言特性**：中文为象形文字，无空格分隔词边界，分词质量直接影响下游任务表现；Whole Word Masking 等预训练策略对中文效果显著
- **文化知识**：中国历史、地理、法律、成语等文化特有知识需要专门的评测维度（如 [C-Eval](../sources/ceval-repo.md) 的考试题、[CLUE](../sources/clue-repo.md) 的成语完形填空 CHID 任务）
- **合规与安全**：中文大模型需符合本地法规和安全要求，[SuperCLUE](../sources/superclue-repo.md) 专门设立了安全评测子基准（SuperCLUE-Safety）
- **数据稀缺**：公开可用的高质量中文评测数据集远少于英文，构建中文 benchmark 面临更大的数据采集挑战
- **中英能力差距**：[AGIEval](../sources/agieval.md) 的研究显示，多数模型在中文考试上的准确率比对应英文考试低 10-20 个百分点，反映了训练数据的语言偏向

## 发展脉络

中文评测基准的发展与 NLP 技术演进紧密同步，经历了从传统 NLU 到大模型综合评测的转变：

| 阶段 | 时间 | 代表基准 | 技术背景 |
|------|------|----------|----------|
| 起步期 | 2019 | [ChineseGLUE](../sources/chineseglue-repo.md) | BERT 时代，借鉴英文 GLUE，13 个 NLU 任务 |
| 成熟期 | 2020 | [CLUE](../sources/clue-repo.md) | 升级为 9 个核心任务 + 诊断集 + 14G 语料库，COLING 2020 |
| LLM 转型 | 2023 | [C-Eval](../sources/ceval-repo.md) | GPT/LLM 兴起，52 学科知识考试型评测，NeurIPS 2023 |
| 综合评测 | 2023 | [SuperCLUE](../sources/superclue-repo.md) | 四大象限综合评测，涵盖 Agent、安全、多轮对话 |

关键演进趋势：

1. **任务形式**：从固定格式 NLU 任务（分类、匹配、NER）转向开放式生成与多轮对话
2. **评测对象**：从预训练语言模型（BERT 系列，百 M 参数）扩展到大型语言模型（GPT-4 级别）
3. **评测维度**：从单一语言理解能力扩展到知识、推理、Agent 能力、安全性等多维度
4. **评测方法**：从纯客观题自动评分引入 LLM-as-Judge 主观评测（SuperCLUE-OPEN 使用 GPT-4 Turbo 作为裁判）

## 基准对比

| 基准名称 | 机构 | 发布时间 | 数据规模 | 任务类型 | 评测方式 | 核心特色 |
|----------|------|----------|----------|----------|----------|----------|
| [ChineseGLUE](../sources/chineseglue-repo.md) | CLUEbenchmark 社区 | 2019 | 13 个任务 | 分类、匹配、阅读理解、NER | 客观题自动评分 | 中文 NLU 先驱，借鉴英文 GLUE |
| [CLUE](../sources/clue-repo.md) | CLUEbenchmark 社区 | 2020 | 9 个核心任务 + 诊断集 | 分类(6) + 阅读理解(3) | 客观题自动评分 | 难样本筛选、14G 语料库、COLING 2020 |
| [C-Eval](../sources/ceval-repo.md) | 上海交通大学等 | 2023 | 13,948 题 / 52 学科 | 多选题知识考试 | 客观题（概率解码 / 正则匹配） | 4 级难度、C-Eval Hard 子集、NeurIPS 2023 |
| [SuperCLUE](../sources/superclue-repo.md) | CLUEbenchmark 团队 | 2023 | 4,265+ 题（持续扩展） | 开放题 + 客观题 | LLM-as-Judge + 自动评分 | 四大象限、Agent 评测、安全评测、月度更新 |
| [AGIEval](../sources/agieval.md) | Zhong et al. | 2023 | 20 种考试 / 数千题 | 选择题、填空题、简答题 | Exact Match + 人工评估 | 中英双语、真实考试题目、NAACL 2024 |

## 评测维度覆盖

各基准在评测维度上形成互补：

| 评测维度 | ChineseGLUE | CLUE | C-Eval | SuperCLUE | AGIEval |
|----------|:-----------:|:----:|:------:|:---------:|:-------:|
| 语言理解（NLU） | ++ | ++ | + | + | + |
| 知识储备 | - | - | ++ | + | ++ |
| 逻辑推理 | + | + | + | + | ++ |
| 数学计算 | - | - | + | + | + |
| 代码能力 | - | - | - | + | - |
| 生成与创作 | - | - | - | ++ | - |
| 多轮对话 | - | - | - | ++ | - |
| Agent / 工具使用 | - | - | - | ++ | - |
| 安全性 | - | - | - | ++ | - |
| 中文文化特性 | + | + | ++ | + | + |

（++ 重点覆盖 / + 部分覆盖 / - 未覆盖）

早期基准（ChineseGLUE、CLUE）聚焦传统 NLU 能力；C-Eval 和 AGIEval 侧重知识与推理；SuperCLUE 最为全面，是目前唯一覆盖 Agent 能力和安全性的中文评测基准。

## 与通用基准的互补

中文评测基准与通用英文基准形成互补关系：

- **[MMLU](../sources/mmlu.md) vs C-Eval**：MMLU 覆盖 57 个英文学科，C-Eval 覆盖 52 个中文学科，两者在学科知识评测上形成中英对照。C-Eval 特别包含中国法律、中医、中国历史等文化特有学科
- **GLUE/SuperGLUE vs CLUE**：CLUE 直接借鉴英文 GLUE 的设计理念，为中文 NLU 构建了对等的评测体系，但包含成语完形填空（CHID）等中文特有任务
- **[AGIEval](../sources/agieval.md) 作为双语桥梁**：AGIEval 同时包含英文考试（SAT、GRE、LSAT、GMAT）和中文考试（高考、公务员考试），是唯一直接对比模型中英文能力差异的 benchmark，研究发现多数模型中文表现低于英文 10-20 个百分点
- **评测平台整合**：[OpenCompass](../sources/opencompass-repo.md) 作为主要的中文评测平台，同时支持中文基准（C-Eval、CLUE 等）和通用基准（MMLU、HumanEval 等），提供统一的评测框架

综合使用中文基准和通用基准，才能全面评估模型的多语言能力和中文场景适用性。

## 相关页面

### 中文评测基准资料
- [C-Eval](../sources/ceval-repo.md) -- 52 学科中文知识考试评测（NeurIPS 2023）
- [CLUE](../sources/clue-repo.md) -- 中文语言理解评测基准（COLING 2020）
- [ChineseGLUE](../sources/chineseglue-repo.md) -- 中文 NLU 评测经典版
- [SuperCLUE](../sources/superclue-repo.md) -- 中文大模型综合评测
- [AGIEval](../sources/agieval.md) -- 中英双语真实考试评测（NAACL 2024）

### 通用基准与平台
- [MMLU](../sources/mmlu.md) -- 英文多任务语言理解基准，C-Eval 的英文对标
- [OpenCompass](../sources/opencompass-repo.md) -- 主要中文评测平台，整合多种中英文基准
