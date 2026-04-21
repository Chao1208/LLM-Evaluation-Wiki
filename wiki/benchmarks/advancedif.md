---
title: "AdvancedIF"
type: benchmark
created: 2026-04-09
updated: 2026-04-09
tags: [benchmark, rubric, 指令遵循, Meta, RIFL]
sources: [raw/report/Rubric-Based推理数据调研报告.md, raw/benchmarks/AdvancedIF/README.md]
---

# AdvancedIF

## 基本信息

| 项目 | 详情 |
|------|------|
| 创建机构 | [Meta](../entities/meta.md) / Llama 团队 |
| 领域 | 通用指令遵循 |
| 规模 | 1,600+ prompts |
| 方法论 | [Rubric-Based 评测](../concepts/rubric-based-evaluation.md) |
| 论文 | https://arxiv.org/abs/2511.10507 |
| GitHub | https://github.com/facebookresearch/AdvancedIF |
| 许可证 | CC-BY-NC |

## 评测维度

### 三种任务类型

| 任务 | benchmark_name | 说明 | 对应 Judge |
|------|---------------|------|-----------|
| 系统指令遵循 | `if_system_steerability_oss` | 检查回复是否遵循 system prompt | SystemSteerIFRubricsJudge |
| 多轮上下文传递 | `if_carried_context_oss` | 评估多轮对话中携带上下文的指令遵循 | IFRubricsJudge |
| 复杂单轮指令 | `if_complex_if_oss` | 评估复杂的单轮指令遵循 | IFRubricsJudge |

（来源：[仓库文档](../sources/advancedif-repo.md)）

## 方法论

### 评测指标（三级）

1. **成功率（Success Rate）**：成功处理的行的百分比
2. **整体通过率（Overall Pass Rate）**：所有 rubric 均通过的样本百分比——**论文中报告的核心指标**，公式：`(所有 rubric 均通过的样本数) / (总样本数)`
3. **微观 Rubric 通过率（Micro-Level Rubric Pass Rate）**：所有样本中通过的单条 rubric 百分比，公式：`(通过的 rubric 总数) / (评估的 rubric 总数)`

### 评测工具链

- **默认 Judge 模型**：o3-mini-2025-01-31（[OpenAI](../entities/openai.md)）
- **两种专用 Judge**：IFRubricsJudge（用户指令）和 SystemSteerIFRubricsJudge（系统指令）
- **批处理**：支持最大 10-20 并发 API 请求，逐行容错
- **输出**：保留原始输入字段 + 嵌套 `judge_result` 对象（含 rubrics_check、rubric_level_pass_rate）
- **数据格式**：JSONL / CSV，必填字段为 `conversation_history` + `response` + `prompt_metadata`(rubrics)

### 规模化扩展方案（RIFL）

AdvancedIF 提出了用少量专家样本训练 Rubric Generator 的规模化方案：

1. 收集 ~1,600 条专家编写的 Prompt + Rubric 对
2. 微调 LLM 作为 Rubric Generator
3. 在 held-out 专家数据上评估，通过语义匹配计算 Precision/Recall
4. 达标后（F1 从 0.639 提升至 0.790）投入批量生成
5. 专家对合成 Rubric 进行抽样验证和修正

### Reward Hacking 发现

当 Rubric 用于 RL 训练时，RIFL 发现模型出现 reward hacking：在回答末尾生成"all instructions are followed"来欺骗 Verifier。

对策：
1. 微调专用 Rubric Verifier（而非直接用 off-the-shelf LLM）
2. 增加反 hacking criteria，如"模型是否提供了干净的回答，没有异常的冗余自评内容？"

## 局限性与争议

- 规模化生成的 Rubric 质量是否能达到专家手写水平仍需验证
- Reward hacking 问题说明 Rubric 用于训练时需要额外防护
- 仅支持 OpenAI API 作为 Judge 后端，未来是否会支持开源模型作为 Judge 尚不明确
- CC-BY-NC 许可意味着不能直接商用

## 相关页面

- [Meta](../entities/meta.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
- [仓库文档](../sources/advancedif-repo.md)
