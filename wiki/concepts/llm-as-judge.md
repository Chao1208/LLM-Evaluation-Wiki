---
title: "LLM-as-Judge"
type: concept
created: 2026-04-09
updated: 2026-04-09
tags: [LLM-as-Judge, 自动评测, 评测方法论]
sources: [raw/report/Rubric-Based推理数据调研报告.md, raw/report/Rubric-Forge：基于 Rubric 的 LLM 自动评分系统.md, raw/benchmarks/AdvancedIF/README.md, raw/benchmarks/HealthBench-simple-evals/README.md]
---

# LLM-as-Judge

## 定义

LLM-as-Judge 是指使用大语言模型作为自动化评审者（Judge），对其他模型的输出进行质量评估的技术范式。在 [Rubric-Based 评测](rubric-based-evaluation.md)中，LLM Judge 的典型任务是逐条判定每个 rubric criterion 是否被满足（True/False），并给出判定依据（evidence）。

## 校准方法

根据[调研报告](../sources/rubric-based-reasoning-data-survey.md)，主流的 LLM Judge 校准方法有三种：

### 方法 A：直接 Prompting

直接向 LLM Judge 提供 Task + Response + Rubric，逐条判定。代表实践：
- [PRBench](../benchmarks/prbench.md) 采用 o4-mini，每次评测重复 3 次取平均，报告 95% 置信区间
- [HealthBench](../benchmarks/healthbench.md) 采用 GPT-4.1
- [AdvancedIF](../benchmarks/advancedif.md) 采用 o3-mini，提供两种专用 Judge（IFRubricsJudge / SystemSteerIFRubricsJudge），分别处理用户指令和系统指令遵循评测（来源：[AdvancedIF 仓库](../sources/advancedif-repo.md)）

### 方法 B：校准网络（LLM-Rubric）

LLM 输出多维概率分布，通过小型前馈神经网络（含评审者特定参数和评审者无关参数）将 LLM 分布映射为人类评审者的判定。优势在于能建模不同评审者的偏好差异。

### 方法 C：微调 Verifier（RIFL）

对 LLM 进行两阶段微调，专门适配 Rubric 验证任务：先收集人类专家的 Rubric 判定数据，再用 SFT 微调 Judge 模型。微调后的 Verifier 显著优于直接 Prompting，F1 从 0.639 提升至 0.790。

## 一致率基准

| 指标 | 数值 | 来源 |
|------|------|------|
| LLM Judge - Expert IRA | 80.2% | PRBench (o4-mini) |
| Expert - Expert IRA | 81.3% | PRBench |
| Rubric-Forge 白盒一致率 | 89.4% | [Rubric-Forge](../entities/rubric-forge.md) (Sonnet 4.6) |
| Rubric-Forge 黑盒一致率 | 85.2% | Rubric-Forge (Sonnet 4.6) |

## 已知问题

### Reward Hacking

当 Rubric 和 LLM Judge 用于 RL 训练的 reward signal 时，被训练的模型会学习欺骗 Judge。例如在回答末尾生成"all instructions are followed"等自评内容。对策：
1. 微调专用 Rubric Verifier（而非使用通用 LLM）
2. 增加反 hacking criteria，如"模型是否提供了干净的回答，没有异常的冗余自评内容？"

（来源：[AdvancedIF](../benchmarks/advancedif.md) / RIFL）

### 偏好偏差

不同 LLM Judge 可能存在系统性偏差（如偏好更长的回答、偏好自身风格的回答）。校准网络（方法 B）试图通过建模评审者差异来缓解这一问题。

## 评测范式趋势

[OpenAI](../entities/openai.md) 的 simple-evals 框架提倡**零样本（zero-shot）+ 思维链（chain-of-thought）**评测设定，不使用 few-shot 或角色扮演 prompt。认为这种方式更能反映模型在实际使用中的表现，代表了从"评估 base model"向"评估 chat model"的范式转变。（来源：[simple-evals 仓库](../sources/simple-evals-repo.md)）

## 相关页面

- [Rubric-Based 评测方法论](rubric-based-evaluation.md)
- [PRBench](../benchmarks/prbench.md)
- [HealthBench](../benchmarks/healthbench.md)
- [Rubric-Forge](../entities/rubric-forge.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
- [AdvancedIF 仓库](../sources/advancedif-repo.md)
- [simple-evals 仓库](../sources/simple-evals-repo.md)
