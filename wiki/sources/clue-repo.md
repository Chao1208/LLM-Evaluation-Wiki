---
title: "CLUE: 中文语言理解评测基准 (GitHub 仓库)"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [benchmark, 中文评测, NLU, 预训练模型, CLUE, COLING]
sources: [raw/github/CLUE/README.md]
---

## 概述

CLUE（Chinese Language Understanding Evaluation）是中文语言理解评测基准，由 CLUE benchmark 社区开发和维护。该项目借鉴英文 GLUE/SuperGLUE 的理念，为中文 NLP 构建了一套包含代表性数据集、基准预训练模型、语料库和排行榜的完整评测体系。

- 论文：[CLUE: A Chinese Language Understanding Evaluation Benchmark](https://arxiv.org/abs/2004.05986)
- 发表于 **COLING 2020**
- 官网：[www.CLUEbenchmarks.com](https://www.CLUEbenchmarks.com)
- 工具包：[PyCLUE](https://github.com/CLUEbenchmark/PyCLUE) -- 支持 10 个任务、9 大模型、自定义任务
- 运行环境：TensorFlow 1.12 / CUDA 9.0 / cuDNN 7.0
- 同一团队后续发展为 [SuperCLUE](../sources/superclue-repo.md)，面向大模型综合评测

## 关键特性

1. **9 个核心评测任务**：覆盖分类（6 个）和阅读理解（3 个）两大类
2. **分类任务（v1 正式版）**：
   - **AFQMC** -- 蚂蚁金融语义相似度（34K 训练样本）
   - **TNEWS'** -- 今日头条短文本分类（15 类新闻，53K 训练样本）
   - **IFLYTEK'** -- 长文本分类（119 类 APP 描述，12K 训练样本）
   - **CMNLI/OCNLI** -- 自然语言推理（OCNLI 为原生中文版，替代翻译版 CMNLI）
   - **CLUEWSC2020** -- Winograd 代词消歧中文版（1,244 训练样本）
   - **CSL** -- 论文关键词识别（20K 训练样本）
3. **阅读理解任务**：
   - **CMRC2018** -- 简体中文抽取式阅读理解
   - **CHID** -- 成语完形填空（84K 训练样本）
   - **C3** -- 多选中文阅读理解（对话+混合文本）
4. **诊断集**：评估模型在 9 种中文语言现象上的表现
5. **CLUECorpus2020 语料库**：14G 预训练语料，含新闻(8G)、社区互动(3G)、维基百科(1.1G)、评论(2.3G)
6. **难样本筛选方法**：采用 k 折交叉验证过滤简单样本，提高数据集区分度

## 支持的模型/基准

v1 版分类任务排行榜（测试集平均 Score）：

| 模型 | 参数量 | 分类 Score | 阅读理解 Score |
|------|:------:|:----------:|:-------------:|
| RoBERTa-wwm-large | 330M | **72.83** | **79.05** |
| RoBERTa-large | 334M | 71.70 | 76.85 |
| ALBERT-xxlarge | 235M | 71.04 | 77.19 |
| RoBERTa-wwm-ext | 108M | 70.10 | 75.11 |
| BERT-wwm-ext | 108M | 68.77 | 75.12 |
| BERT-base | 108M | 68.77 | 72.71 |
| ALBERT-tiny | 4M | 62.61 | 49.05 |

**关键发现**：RoBERTa-wwm-large-ext 在几乎所有任务上均为最优；大参数量模型优势明显但 ALBERT-xxlarge 以更少参数实现接近效果。

## 技术架构

- **一键运行脚本**：`bash run_classifier_xxx.sh` 自动下载模型和数据，支持 GPU/TPU
- **多框架支持**：TensorFlow、PyTorch、Keras、PaddlePaddle
- **提交评测**：通过官网提交预测文件，自动获取测试集评分
- **数据格式**：JSON/CSV，与 SQuAD 格式兼容（阅读理解任务）
- **PyCLUE 工具包**：`pip install PyCLUE`，一行代码运行任意任务+模型组合

## 与评测相关的启示

1. **中文 NLU 基础设施**：CLUE 填补了中文 NLU 缺乏标准基准的空白，类比英文 GLUE/SuperGLUE 的角色
2. **预训练模型中文适配评估**：许多 SOTA 模型无官方中文版本，CLUE 提供了标准化评测平台
3. **难样本筛选方法论**：k 折交叉验证筛选法可迁移到其他评测集构建中，提高 benchmark 区分度（参考 [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md) 中对评测集质量的要求）
4. **从 NLU 到 LLM**：CLUE 侧重传统 NLU 任务（分类、推理、阅读理解），后续被同团队的 [SuperCLUE](../sources/superclue-repo.md) 扩展为面向大模型的综合评测
5. **中文语言特性**：象形文字、无分隔符分词等中文特性对模型评测有独特影响，需要专门的中文 benchmark
6. **语料库价值**：14G CLUECorpus2020 为中文预训练模型开发提供了重要的公共资源

## 相关页面

- [ChineseGLUE](../sources/chineseglue-repo.md) -- CLUE 的前身项目（经典版）
- [SuperCLUE](../sources/superclue-repo.md) -- CLUE 团队面向大模型的综合评测
- [C-Eval](../sources/ceval-repo.md) -- 另一个主流中文评测基准（知识考试型）
- [MMLU](../sources/mmlu.md) -- 英文多任务语言理解基准
- [LLM-as-Judge](../concepts/llm-as-judge.md) -- LLM 自动评审方法论
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md) -- 细粒度评测标准设计
