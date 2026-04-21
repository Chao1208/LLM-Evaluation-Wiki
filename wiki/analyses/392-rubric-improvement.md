---
title: "392 Rubrics 可用性提升分析"
type: analysis
created: 2026-04-09
updated: 2026-04-09
tags: [392, rubric, 改进方案, 原子化, 模板化, 校准]
sources: [raw/benchmarks/392toolkit/策略库.py, raw/report/Rubric-Based推理数据调研报告.md, raw/report/Rubric-Forge：基于 Rubric 的 LLM 自动评分系统.md]
---

# 392 Rubrics 可用性提升分析

> 问题："为了提高 392 的 rubrics 可用性，应该从哪些方面进行提升？"
> 分析日期：2026-04-09

## 背景

[392 语音助手评测集](../benchmarks/392-voice-assistant.md)包含 392 条 query 的完整 rubric 策略库，采用 strategy1（负向）+ strategy2（正向）的双策略结构，三值评分（-1/0/1）。通过与 wiki 中积累的业界最佳实践（[PRBench](../benchmarks/prbench.md)、[AdvancedIF](../benchmarks/advancedif.md)、[BigLaw Bench](../benchmarks/biglaw-bench.md)、[HealthBench](../benchmarks/healthbench.md)、[Rubric-Forge](../entities/rubric-forge.md)）系统对照，识别出五个核心改进方向。

## 方向一：原子化拆分（P0 优先级）

### 现状问题

392 的 strategy 单条动辄数百字，内含多个子条件（用 `\n2.` `\n3.` 隔开），严重违反了 [Rubric 编写五大原则](../concepts/rubric-based-evaluation.md)中的**原子性（Atomic）**原则。例如 q8 的 strategy1 里一口气塞了 4 个检查点：

1. 科目名不能出英语以外的
2. 时间分配不合理判 -1
3. 时效性幻觉判 -1
4. 场景幻觉判 -1

LLM Judge 拿到这一整坨文字，很难对每个子条件做独立判定。一旦半对半错，三值评分（-1/0/1）就没法打。

### 改进方案

把每条 strategy 拆成独立的原子 criteria，每条只检查一件事：

```
# 改造前：q8.strategy1（一大段）
"若智能体回复中直接针对英语以外的某一具体科目给出复习建议...
 若回复中给出的建议在时间分配上不合理...
 若智能体回复提及需要强时效信息...
 若回复中提及用户未明确说明的场景..."

# 改造后：q8 拆成 4 条独立 criteria
criteria_8a: "回复中是否直接针对英语以外的具体科目给出复习建议？" → 是则 fail
criteria_8b: "回复中给出的建议在时间分配或步骤安排上是否有一眼可见的不合理？" → 是则 fail
criteria_8c: "无 RAG 时回复是否提及天气、日期等时效性信息？" → 是则 fail
criteria_8d: "回复是否提及用户未提过的场景/活动/身份？" → 是则 fail
```

### 预期收益

- [Rubric-Forge](../sources/rubric-forge.md) 的经验：拆分后的平均 4.4 条 criteria/query 就能达到 89.4% 一致率
- [PRBench](../benchmarks/prbench.md) 的验收标准：专家 IRA ≥ 90% 的前提就是原子性
- LLM Judge 对短小、单一判断的执行准确率远高于长文本多条件

### 操作建议

1. 先从 q1-q30（情绪安抚 + 备考辅导）做试点拆分
2. 每条 query 预计拆成 3-6 条原子 criteria
3. 复用高频模式做模板化（见方向二）

## 方向二：模板化高频 criteria（P0 优先级）

### 现状问题

大量 criteria 在 392 条 query 间反复出现，只是措辞略有不同：

| 高频检查点 | 出现频率 | 当前问题 |
|-----------|---------|---------|
| 中英文混杂检测 | ~90%+ 的 query | 每条都重复一大段相同描述 |
| 时效性幻觉（无 RAG 编造天气/日期） | ~40%+ | 每条都重复整段 |
| 场景幻觉（提及用户未说的信息） | ~50%+ | 措辞略有差异但核心相同 |
| 专升本科目不能具体化 | q2-q30+ | 大段重复 |
| 功能越界（建议发图片/视频） | ~10%+ | 重复描述 |
| 共情表达要求 | ~60%+ | strategy2 中反复出现 |

### 改进方案

建立**全局 criteria 模板库 + 局部 criteria** 的二层结构：

```python
# 全局模板（所有 query 共享）
GLOBAL_CRITERIA = {
    "no_code_mixing": "回复中是否出现中英文混杂？（品牌名/型号名除外）→ 是则 fail",
    "no_temporal_hallucination": "无 RAG 且用户未提供时间/地点时，回复是否编造天气/日期/事件等时效信息？→ 是则 fail",
    "no_context_hallucination": "回复是否提及用户从未在当前对话中表达或暗示的具体信息？→ 是则 fail",
    "empathy_required": "回复是否对用户表达的负面情绪进行了理解或共情？→ 否则 fail",
}

# 局部 criteria（仅适用于特定 query）
q8_local = {
    "no_specific_subject": "回复是否直接针对英语以外的具体科目给出复习建议？→ 是则 fail",
    "plan_feasibility": "回复中给出的建议在时间分配上是否明显不合理？→ 是则 fail",
}

# 组合
q8_criteria = GLOBAL_CRITERIA | q8_local
```

### 预期收益

- 消除 80% 的文本冗余，缩短 Judge 输入长度
- 全局 criteria 可以被统一测试和校准
- 修改一处全局生效，维护成本大幅降低

## 方向三：标准化评分量表（P1 优先级）

### 现状问题

三值评分（-1/0/1）中，0 的含义不统一：
- 有些 query 里 0 = "部分满足"（如 q12: "仅仅表明理解但没有提出方法，判 0"）
- 有些 query 里 0 = "语言不对"（如 q31: "全程中文没有英文，判 0"）
- 有些 query 里 0 = "跑题了"（如 q34: "直接认定用户不喜欢做菜，转移话题，判 0"）

同一个分值在不同 query 间含义不可比，也让 LLM Judge 难以形成一致的评判标准。

### 改进方案

三种可选范式：

| 方案 | 参考 | 适用场景 | 改动量 |
|------|------|---------|-------|
| **A. 全部拆成 pass/fail** | [AdvancedIF](../benchmarks/advancedif.md)、[Rubric-Forge](../entities/rubric-forge.md) | 多条原子 criteria 各自 pass/fail，不需要中间态 | 大 |
| **B. 加权打分** | [PRBench](../benchmarks/prbench.md) | 每条 criteria 有权重（+1~+10, -1~-10），最终加权求和 clip 到 [0,1] | 大 |
| **C. 保留三值但统一定义** | 当前方案优化 | -1=有红线错误；0=无错误但不够好；1=好 | 小 |

**推荐方案 A**：如果做了方向一的原子化拆分，pass/fail 是最自然的选择。每条原子 criteria 只判 pass/fail，聚合层处理权重。这也是 Rubric-Forge 已验证有效的方案（pass/fail + 加权求和 + veto 机制，一致率 89.4%）。

## 方向四：建立校准闭环（P1 优先级）

### 现状问题

`十批数据整理（修正评分版）.xlsx` 的存在说明前期评分有系统性问题。对照 [P0-P5 六阶段方法论](../concepts/rubric-based-evaluation.md)，392 缺失了 **P4（独立验证）** 和 **P5（LLM Judge 校准）** 这两个质量关卡。

### 改进方案

```
Step 1: 建立 Ground Truth 标定集
  - 从 392 条 query 中抽取 50-80 条代表性 query
  - 每条 query 配 3-5 条不同质量的 response
  - 由 2+ 人独立标注，计算 IRA

Step 2: 校准 LLM Judge
  - 用 Step 1 的标定集跑 Judge
  - 计算 Judge-Expert IRA
  - 目标：≥ 80%（PRBench 标准）

Step 3: 迭代修复
  - 对 IRA 低的 query 进行 bad case 分析
  - 修改 criteria 文本直到 IRA 达标
  - Rubric-Forge 经验：首轮 bad case 修复效果最显著（v2→v3 +6.2pp）
```

### 预期收益

- [Rubric-Forge](../sources/rubric-forge.md) 实践证明：10+ 轮迭代能从 79.4% 提升到 89.4%，且首轮修复效果最大
- 可以复用 Rubric-Forge 的校准 pipeline，不需要从零搭建

## 方向五：提取可复用的评测能力标签（P2 优先级）

### 现状问题

392 目前只有 query 级别的 strategy，无法回答"模型在哪个能力维度上表现好/差"这类问题。

### 改进方案

为每条 criteria 打上能力标签（和 [Rubric-Forge](../sources/rubric-forge.md) 的维度体系对齐）：

| 维度 | 说明 | 392 中的典型 criteria |
|------|------|---------------------|
| `instruction_following` | 指令遵循 | 语言切换、翻译准确性 |
| `hallucination` | 幻觉检测 | 时效性幻觉、场景幻觉、虚构用户信息 |
| `emotion` | 情感共情 | 理解情绪、安抚质量 |
| `reasoning` | 推理质量 | 建议可行性、时间分配合理性 |
| `safety` | 安全红线 | 功能越界、不当建议 |
| `style` | 风格一致性 | 中英文混杂、persona 一致性 |

有了标签后可做维度级模型对比分析：

```
模型 A: emotion 92% | hallucination 78% | instruction_following 85%
模型 B: emotion 88% | hallucination 91% | instruction_following 90%
→ 结论：模型 A 共情更好，模型 B 幻觉控制更好
```

## 优先级总结

| 优先级 | 方向 | 预期 ROI | 理由 |
|--------|------|---------|------|
| 🔴 P0 | 原子化拆分 | 最高 | 所有后续改进的基础 |
| 🔴 P0 | 模板化 | 最高 | 和拆分同步做，消除冗余 |
| 🟡 P1 | 统一评分量表 | 高 | 拆分后自然转向 pass/fail |
| 🟡 P1 | 校准闭环 | 高 | 拆分+统一后跑一轮校准，拿到 IRA 基线 |
| 🟢 P2 | 能力标签 | 中 | 锦上添花，但可解释性价值大 |

**落地建议**：先拿 q1-q30（备考辅导场景）做 pilot，完成"拆分→模板化→统一评分→校准"全流程，验证可行后再推广到全部 392 条。

## 相关页面

- [392 语音助手评测集](../benchmarks/392-voice-assistant.md)
- [392 资料详情](../sources/392toolkit.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [Rubric-Forge](../entities/rubric-forge.md)
- [Rubric-Forge 报告](../sources/rubric-forge.md)
- [PRBench](../benchmarks/prbench.md)
- [AdvancedIF](../benchmarks/advancedif.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
