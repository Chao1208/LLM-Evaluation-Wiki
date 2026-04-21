---
title: "392 语音助手评测集"
type: source
created: 2026-04-09
updated: 2026-04-09
tags: [392, 语音助手, rubric, 百度, 内部实践, 评测数据]
sources: [raw/benchmarks/392toolkit/策略库.py, raw/benchmarks/392toolkit/4_black_box_392.csv, raw/benchmarks/392toolkit/6_white_box_392.csv]
---

# 392 语音助手评测集

## 概述

百度内部的语音助手类评测集合，编号"392"，包含 392 条 query 的完整 rubric 策略库以及多批次、多模型的黑盒/白盒评测数据。这是一个面向**情感陪伴型语音助手**的 Rubric-Based 评测实践，评测场景涵盖情绪安抚、备考辅导、跨语言切换、工作压力疏导等多轮对话场景。

## 关键发现

### 1. 数据规模

| 文件 | 类型 | 规模 |
|------|------|------|
| `策略库.py` | Rubric 策略定义 | 392 条 query，每条含 strategy1（负向/扣分标准）+ strategy2（正向/得分标准） |
| `4_black_box_392.csv` | 黑盒评测数据 | ~57,800 行 |
| `6_white_box_392.csv` | 白盒评测数据 | ~90,700 行 |
| `392_q_rubric_all.xlsx` | Rubric 汇总表 | — |
| `black_392_vanswer.xlsx` | 黑盒结果验证 | — |
| `十批数据整理（修正评分版）.xlsx` | 十批数据修正评分 | — |
| `超黑盒/` | 超黑盒评测数据 | 含 persona 维度、有/无 ground truth 两种版本 |

### 2. Rubric 设计特点（策略库分析）

**双策略结构（strategy1 + strategy2）**：

- **strategy1（负向标准）**：定义扣分条件（判 -1），关注：
  - **中英文混杂**：语音助手场景下的核心红线，几乎每条 query 都检查
  - **幻觉问题**：回复中提及用户未说过的具体信息（如虚构用户经历、学习计划等）
  - **时效性幻觉**：无 RAG 时编造天气、日期等时效信息
  - **逻辑错误**：如阅卷老师说成监考老师、时间尺度错配
  - **功能越界**：建议用户发送图片/视频，但交互渠道仅支持语音
  - **事实错误**：如"讨厌的考试"几个字数说错

- **strategy2（正向标准）**：定义得分条件（判 1），关注：
  - **共情表达**：对用户情绪的理解和回应
  - **可行建议**：提供符合用户实际情况的建议
  - **场景适配**：建议需匹配用户的时间、身份等约束
  - **语言一致性**：用户用英文则全程英文回复

**三值评分体系**：-1（扣分）/ 0（中性/部分满足）/ 1（满分）

### 3. 评测场景覆盖

从策略库 q1-q392 的内容可识别出以下核心对话场景：

| 场景 | Query 范围（示例） | 关键评测点 |
|------|-------------------|-----------|
| 情绪安抚 + 备考辅导 | q1-q30 | 专升本焦虑、手抖困扰、拖延自责、朋友不理解 |
| 跨语言交互 | q31-q41 | 中英文切换、全英文回复、翻译能力 |
| 工作压力疏导 | q42-q51+ | 上班焦虑、领导不公、工资不满、想摆烂 |

### 4. 多模型多配置评测

评测数据包含多种模型/配置：

| 模型/配置 | 类型 | Persona |
|----------|------|---------|
| mimo | 模型变体 | — |
| nothink | 无思考模式 | — |
| think | 思考模式 | — |
| v26 | 版本迭代 | — |
| 小度 | persona | 小度（别称小度想想），乐观开朗、热情洋溢 |
| 肖然 | persona | — |
| 叶悠悠 | persona | — |
| 岳云鹏 | persona | — |

### 5. 黑盒 vs 白盒 vs 超黑盒

| 评测模式 | 特征 | 数据 |
|---------|------|------|
| 黑盒（black box） | 不提供 ground truth | `4_black_box_392.csv`（~57.8K 行） |
| 白盒（white box） | 提供 ground truth | `6_white_box_392.csv`（~90.7K 行） |
| 超黑盒 | 增加 persona 维度，分 with/without ground truth | `超黑盒/` 目录 |

### 6. 数据字段结构

**黑盒 CSV**：编号、批次、session_id、turn_id、query、response、history、rag、persona

**白盒 CSV**：w_or_b、批次、session_id、turn_id、query、response、persona、history、rag、ground_truth

## 重要洞察

1. **中英文混杂是语音助手场景的第一红线**——策略库中几乎每条 query 的 strategy1 都包含中英文混杂检测，反映语音场景用户对语言一致性的高敏感度
2. **幻觉检测高度具体化**——不是泛泛的"是否有幻觉"，而是针对每条 query 的具体上下文定义哪些信息算幻觉（如"用户没说过晚自习，回复却提到晚自习"）
3. **三值评分（-1/0/1）** 是对 [Rubric-Forge](../entities/rubric-forge.md) 的 pass/fail 二值评分和 [PRBench](../benchmarks/prbench.md) 的加权评分的一种折中方案
4. **Persona 维度**的引入说明语音助手评测需要考虑角色一致性，这在学术 benchmark 中较少见
5. **超黑盒概念**——在黑盒基础上叠加 persona 维度，增加了评测复杂度

## 问题与思考

1. 392 条 rubric 全部手写，规模化成本高——是否可以用 RIFL（[AdvancedIF](../benchmarks/advancedif.md)）类似方法半自动生成？
2. strategy1/strategy2 的文本很长（单条可达数百字），LLM Judge 的理解和执行质量如何保证？
3. 三值评分中 0 的判定标准不够统一——有些是"部分满足"，有些是"未涉及"
4. 多批次数据是否有系统性的质量变化趋势？十批数据修正评分暗示前期评分存在问题

## 相关页面

- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [Rubric-Forge](../entities/rubric-forge.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
