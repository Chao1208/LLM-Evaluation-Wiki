---
title: "Evaluation and Mitigation of the Limitations of Large Language Models in Clinical Decision-Making"
type: source
created: 2026-04-21
updated: 2026-04-21
tags: [medical, clinical-decision, LLM-evaluation, Nature-Medicine, MIMIC]
sources: [s41591-024-03097-1.pdf]
---

## 概述

Hager 等人在 Nature Medicine（2024）上发表的研究，系统性评估了大语言模型在临床决策中的表现和局限性。研究构建了 **MIMIC-CDM** 数据集，包含 2,400 个基于真实患者数据的临床案例（涉及 4 种腹部病理），对多个 LLM 进行了全面的临床决策能力评估。

核心发现是：LLM 在诊断准确率上显著低于临床医生（P < 0.001），揭示了当前 LLM 在高风险医学场景中的根本性不足。

## 关键发现

- **LLM 诊断显著差于临床医生**：所有测试的 LLM 在诊断准确率上均显著低于临床医生，差异具有统计显著性（P < 0.001）
- **平均准确率差距悬殊**：LLM 平均准确率约 45-55%，而医生约 73-89%
- **多维度能力缺陷**：LLM 在诊断准确性、临床指南遵循、指令遵循、实验室检查结果解读、鲁棒性等方面均表现不佳
- **开源医学模型未显著优于通用模型**：专门针对医学领域微调的 Clinical Camel 和 Meditron 并未显著优于通用模型
- **鲁棒性堪忧**：对输入的微小变化（如症状描述的重排、同义词替换）会导致诊断结果大幅波动

## 方法论

1. **MIMIC-CDM 数据集构建**：
   - 基于 MIMIC 真实患者数据
   - 2,400 个临床案例
   - 4 种腹部病理：阑尾炎、胆囊炎、胰腺炎、憩室炎
   - 每个案例包含：病史、症状、实验室检查、影像信息
2. **测试模型**：
   - 通用模型：Llama 2 Chat
   - 对话模型：OASST
   - 指令微调模型：WizardLM
   - 医学专用模型：Clinical Camel、Meditron
3. **评估维度**：
   - **诊断准确性**（Diagnostic Accuracy）：正确诊断率
   - **指南遵循**（Guideline Adherence）：是否遵循临床指南推荐的诊疗流程
   - **指令遵循**（Instruction Following）：是否按指定格式输出
   - **实验室结果解读**（Lab Test Interpretation）：对检验指标的正确解读
   - **鲁棒性**（Robustness）：对输入扰动的稳定性
4. **对照组**：执业临床医生在同一案例集上的诊断表现

## 重要数据

| 模型 | 诊断准确率 | 指南遵循率 |
|------|-----------|-----------|
| Llama 2 Chat | ~45-50% | 较低 |
| OASST | ~45-50% | 较低 |
| WizardLM | ~50-55% | 中等 |
| Clinical Camel | ~48-53% | 中等 |
| Meditron | ~50-55% | 中等 |
| **临床医生** | **~73-89%** | **高** |

| 数据集规格 | 数值 |
|-----------|------|
| 临床案例数 | 2,400 |
| 病理类型 | 4 种腹部病理 |
| 数据来源 | MIMIC（真实患者数据） |
| 统计显著性 | P < 0.001（LLM vs 医生） |

## 局限性

- 仅覆盖 4 种腹部病理，无法代表全部临床场景
- 未测试 GPT-4 等最新闭源模型（发表时可能受 API 限制）
- 临床案例基于文本描述，未涉及影像判读等多模态能力
- 医生对照组的评估条件（如时间限制、信息获取方式）可能与 LLM 不完全一致
- MIMIC 数据集主要来源于美国医院，存在地域和人群偏差
- 未探索 prompt engineering 或 few-shot 等优化策略对 LLM 表现的提升空间

## 相关页面

- [ChatGPT 医学准确性评估](chatgpt-medical-accuracy.md) — 另一项医学 LLM 评估研究
- [LLM-as-Judge 概念](../concepts/llm-as-judge.md)
- [HealthBench](../benchmarks/healthbench.md) — 医学领域 LLM 评测基准
