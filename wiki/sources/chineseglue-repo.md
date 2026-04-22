---
title: "ChineseGLUE: 中文语言理解测评基准经典版 (GitHub 仓库)"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [benchmark, 中文评测, NLU, 预训练模型, ChineseGLUE]
sources: [raw/github/ChineseGLUE/README.md]
---

## 概述

ChineseGLUE 是中文语言理解测评基准的经典版本（v0 版），由 CLUEbenchmark 社区于 2019 年底发起，借鉴英文 GLUE 项目，为中文 NLP 构建了一套包含数据集、基线模型、语料库和排行榜的评测基础设施。2019 年 11 月后，该项目已迁移至新版 [CLUE](../sources/clue-repo.md)（更系统全面、更好的技术支持），但经典版以实用任务为主继续保留更新。

- 官网：[www.CLUEbenchmarks.com](https://www.CLUEbenchmarks.com)
- 新版地址：[github.com/CLUEbenchmark/CLUE](https://github.com/CLUEbenchmark/CLUE)
- 数据集整体下载：[chineseGLUEdatasets.v0.0.1.zip](https://storage.googleapis.com/chineseglue/chineseGLUEdatasets.v0.0.1.zip)
- 时间线：2019-10 至 2019-12 为 beta 版，2020 年为正式版，2021 年发展为 super 版本

## 关键特性

1. **13 个评测任务**（v0 初版），覆盖分类、句子对匹配、阅读理解、命名实体识别等：
   - **TNEWS** -- 今日头条短文本分类（266K 训练样本，15 类）
   - **LCQMC** -- 口语化语义相似度匹配（238K 训练样本）
   - **XNLI** -- 跨语言自然语言推理（392K 训练样本）
   - **INEWS** -- 互联网新闻情感分析（5.3K 训练样本）
   - **DRCD** -- 繁体中文抽取式阅读理解（8K 段落，26K 问题）
   - **CMRC2018** -- 简体中文抽取式阅读理解（2.4K 段落，10K 问题）
   - **BQ** -- 智能客服问句匹配（100K 训练样本）
   - **MSRANER** -- 命名实体识别（46K 训练样本）
   - **THUCNEWS** -- 长文本分类（33K 训练样本，14 类）
   - **iFLYTEK** -- APP 描述长文本分类（12K 训练样本，119 类）
   - **CHID** -- 成语完形填空（84K 训练样本）
   - **CMNLI** -- 中文自然语言推理（391K 训练样本）
2. **9 大基线模型**：BERT-base、BERT-wwm-ext、ERNIE-base、RoBERTa-large、XLNet-mid、ALBERT-xlarge/tiny、RoBERTa-wwm-ext、RoBERTa-wwm-large
3. **一键运行脚本**：自动下载数据和模型，支持 TensorFlow/PyTorch/Keras/PaddlePaddle
4. **14G 预训练语料库**：新闻(8G)、社区互动(3G)、维基百科(1.1G)、评论(2.3G)
5. **公开排行榜和提交系统**

## 支持的模型/基准

v0 版分类任务排行榜（测试集 Score，9 任务均分）：

| 模型 | 参数量 | Score |
|------|:------:|:-----:|
| RoBERTa-wwm-large | 330M | **85.13** |
| RoBERTa-large | 334M | 85.08 |
| BERT-wwm-ext | 108M | 84.89 |
| BERT-base | 108M | 84.57 |
| ERNIE-base | 108M | 84.63 |
| ALBERT-xlarge | 59M | 84.08 |
| XLNet-mid | 209M | 81.07 |
| ALBERT-tiny | 1.8M | 78.22 |

阅读理解任务（Score，3 任务均分）：

| 模型 | Score |
|------|:-----:|
| RoBERTa-wwm-large | **84.22** |
| RoBERTa-large | 83.32 |
| RoBERTa-wwm-ext | 81.88 |
| BERT-base | 79.08 |

## 技术架构

- **数据格式**：JSON，各任务使用 `_!_` 分隔符或标准 JSON 结构
- **评测指标**：Accuracy（分类）、F1/EM（阅读理解）、F1（NER）
- **基线代码**：`chineseGLUE/baselines/models/{model}/run_classifier_{task}.sh`
- **提交系统**：通过官网上传预测 JSON 文件，自动评分

## 与评测相关的启示

1. **中文 NLU 评测的先驱**：ChineseGLUE 是中国最早的系统化中文 NLU 基准之一，为后续 [CLUE](../sources/clue-repo.md) 和 [SuperCLUE](../sources/superclue-repo.md) 奠定了基础
2. **任务多样性设计**：覆盖语义相似、文本分类、自然语言推理、阅读理解、NER 等多种任务类型，体现了 benchmark 设计中任务覆盖面的重要性
3. **中文语言的独特挑战**：
   - 象形文字特性和无分隔符分词影响下游任务
   - 公开可用的中文数据集远少于英文
   - 许多 SOTA 模型无官方中文版本
4. **从传统 NLU 到大模型评测的过渡**：ChineseGLUE 时代关注预训练语言模型（BERT 系列），随着 GPT/LLM 兴起，同一团队发展出针对大模型的 SuperCLUE
5. **社区驱动模式**：开放成员制、邮件申请语料等社区协作模式，对中文 NLP 生态建设有参考价值
6. **Whole Word Masking 的效果**：RoBERTa-wwm 系列在几乎所有任务上优于标准版本，说明中文分词对预训练质量的关键影响

## 相关页面

- [CLUE benchmark](../sources/clue-repo.md) -- ChineseGLUE 的正式升级版（v1 版）
- [SuperCLUE](../sources/superclue-repo.md) -- 面向大模型的综合评测基准（同一团队）
- [C-Eval](../sources/ceval-repo.md) -- 中文知识考试型评测基准
- [MMLU](../sources/mmlu.md) -- 英文多任务语言理解基准
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md) -- 细粒度评测标准设计
