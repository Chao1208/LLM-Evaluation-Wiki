---
title: "AdvancedIF 官方仓库"
type: source
created: 2026-04-09
updated: 2026-04-09
tags: [AdvancedIF, Meta, 指令遵循, rubric, 仓库]
sources: [raw/benchmarks/AdvancedIF/README.md, raw/benchmarks/AdvancedIF/README_zh.md]
---

# AdvancedIF 官方仓库

## 概述

Meta / Llama 团队开源的 AdvancedIF 评测工具仓库（论文：https://arxiv.org/abs/2511.10507）。提供完整的 Rubric-Based 指令遵循评测流水线，包括 CLI 工具、批量处理器、两种专用 LLM Judge 及示例数据。许可证为 CC-BY-NC。

## 关键发现

### 1. 三种任务类型

| 任务 | benchmark_name | 说明 | 对应 Judge |
|------|---------------|------|-----------|
| 系统指令遵循 | `if_system_steerability_oss` | 检查回复是否遵循 system prompt | SystemSteerIFRubricsJudge |
| 多轮上下文传递 | `if_carried_context_oss` | 评估多轮对话中携带上下文的指令遵循 | IFRubricsJudge |
| 复杂单轮指令 | `if_complex_if_oss` | 评估复杂的单轮指令遵循 | IFRubricsJudge |

### 2. 三级评测指标

1. **成功率（Success Rate）**：成功处理的行的百分比（无错误）
2. **整体通过率（Overall Pass Rate）**：所有 rubric 均通过的样本百分比——论文中报告的核心指标
3. **微观 Rubric 通过率（Micro-Level Rubric Pass Rate）**：所有样本中通过的单条 rubric 百分比

### 3. 数据格式规范

输入必填字段：
- `conversation_history`：对话历史（不含最终 assistant 回复）
- `response`：被评测的模型回复（最终 assistant 轮次）
- `prompt_metadata`：包含 rubrics 列表的元数据
- `benchmark_name`（可选）：任务标识符

### 4. 评测工具链

- **默认 Judge 模型**：o3-mini-2025-01-31（OpenAI）
- **并发批处理**：支持最大 10-20 并发 API 请求，具备逐行容错能力
- **输出格式**：保留所有原始输入字段 + 嵌套 `judge_result` 对象（含 rubrics_check、rubric_level_pass_rate 等）
- **支持格式**：JSONL 和 CSV

### 5. 代码架构

```
AdvancedIF/
├── judge.py       # BaseRubricsJudge / IFRubricsJudge / SystemSteerIFRubricsJudge
├── processor.py   # DataProcessor（批处理 + 容错）
├── cli.py         # CLI 入口
└── examples/      # 示例数据
```

## 重要数据

- 默认最大补全 token 数：32,768
- 支持 task-based 过滤，可单独运行某一类任务
- 使用 Python logging 模块，支持 DEBUG 到 CRITICAL 五级日志

## 问题与思考

1. 仅支持 OpenAI API 作为 Judge 后端，未来是否会支持开源模型作为 Judge？
2. 示例数据集非常大（109K+ tokens），说明 rubric 评测的 prompt 可能较长
3. CC-BY-NC 许可意味着不能直接商用

## 相关页面

- [AdvancedIF](../benchmarks/advancedif.md)
- [Meta](../entities/meta.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
