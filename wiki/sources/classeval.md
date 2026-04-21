---
title: "ClassEval: A Manually-Crafted Benchmark for Evaluating LLMs on Class-Level Code Generation"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [benchmark, code-generation, class-level, evaluation, ICSE]
sources: [3597503.3639219.pdf]
---

## 概述

ClassEval 是 Du 等人在 ICSE 2024 上发表的代码生成 benchmark，专注于 **class-level（类级别）** 的 Python 代码生成评估。与 HumanEval 等主流 benchmark 聚焦函数级别（function-level）代码生成不同，ClassEval 要求模型生成完整的 Python 类，包含构造函数、多个方法及方法间依赖关系，更贴近真实软件开发场景。

该 benchmark 包含 100 个手工构建的类级别代码生成任务，配有完善的测试用例，是目前评估 LLM 在复杂代码结构生成能力方面的重要基准。

## 关键发现

- **LLM 在类级别生成上显著退化**：所有测试的 LLM 在 class-level 任务上的表现远低于 function-level，差距普遍在 20-40 个百分点
- **方法间依赖是主要瓶颈**：LLM 在处理类内方法间的调用关系、数据共享和状态管理时错误率大幅上升
- **三种生成策略各有优劣**：
  - **Holistic**（整体生成）：一次性生成完整类，简单但错误率高
  - **Incremental**（增量生成）：逐方法生成，利用已生成部分作为上下文
  - **Compositional**（组合生成）：分别生成各方法再组合，灵活但集成困难
- **Incremental 策略总体最优**：在多数模型上，增量生成策略取得最佳 pass@1
- **构造函数生成影响全局**：构造函数生成错误会导致后续所有方法的连锁失败

## 方法论

1. **Benchmark 构建**：
   - 100 个手工设计的 Python 类生成任务
   - 每个任务包含：类描述、方法签名、方法文档、测试用例
   - 覆盖多种类设计模式：继承、组合、状态管理、异常处理等
2. **生成策略**：
   - **Holistic**：输入完整类描述，一次性生成整个类
   - **Incremental**：按方法依赖顺序逐个生成，每次将已生成代码作为上下文
   - **Compositional**：独立生成各方法，最后组合为完整类
3. **评估指标**：class-level pass@1（完整类通过所有测试用例）和 method-level pass@1（单方法通过对应测试）
4. **测试模型**：GPT-4、GPT-3.5、Claude、CodeLlama、WizardCoder、StarCoder 等

## 重要数据

| 模型 | Class-level Pass@1 | Method-level Pass@1 | 策略 |
|------|-------------------|--------------------|----|
| GPT-4 | ~30-40% | ~60-70% | Incremental 最优 |
| GPT-3.5 | ~15-25% | ~45-55% | Incremental 最优 |
| 开源模型 | ~5-20% | ~30-50% | 差异较大 |

| Benchmark 规格 | 数值 |
|----------------|------|
| 任务数量 | 100 |
| 代码语言 | Python |
| 评估粒度 | Class-level + Method-level |
| 发表会议 | ICSE 2024 |

## 局限性

- 仅覆盖 Python 语言，未涉及 Java、C++ 等其他面向对象语言
- 100 个任务的规模相对有限，可能无法全面覆盖所有类设计模式
- 任务难度分布可能不够均匀
- 未考虑跨文件/跨模块的类间依赖场景
- 测试用例的完备性难以保证（可能存在假阳性通过的情况）

## 相关页面

- [Rubric Is All You Need](rubric-is-all-you-need.md) — 代码评估相关工作
- [PRBench](prbench-repo.md) — 另一个代码相关 benchmark
