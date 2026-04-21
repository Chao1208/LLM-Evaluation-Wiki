---
title: "BigLaw Bench 官方仓库"
type: source
created: 2026-04-09
updated: 2026-04-09
tags: [BigLaw Bench, Harvey AI, 法律, rubric, 仓库]
sources: [raw/benchmarks/biglaw-bench/README.md, raw/benchmarks/biglaw-bench/blb-core/README.md, raw/benchmarks/biglaw-bench/blb-retrieval/README.md, raw/benchmarks/biglaw-bench/blb-workflows/README.md]
---

# BigLaw Bench 官方仓库

## 概述

Harvey AI 法律研究团队开源的 BigLaw Bench 评测框架仓库。BigLaw Bench 是一个综合性法律 LLM 评测框架，聚焦于模拟律师实际计费工作的任务，旨在准确衡量 AI 模型在专业法律场景中的实用价值。

## 关键发现

### 1. 三层评测架构

| 层级 | 名称 | 说明 | 数据类型 |
|------|------|------|---------|
| Core | 核心任务 | 基准法律问题解决，16 种子任务类型 | CSV（prompt + rubric） |
| Workflows | 工作流 | 复合型法律任务，评估 Agent 系统 | CSV + JSON schema + PDF 合同 |
| Retrieval | 检索 | 评测检索系统质量 | CSV + PDF 文档（合同/邮件） |

### 2. 核心任务分类（16 种）

**交易类（9 种）**：公司战略与咨询、文件起草、法律研究、尽职调查、风险评估与合规、谈判策略、交易管理、交易结构设计、监管与咨询

**诉讼类（7 种）**：诉讼文件分析、案件管理、文件起草、判例法研究、庭审记录分析、文件审查与分析、庭审准备与口头辩论

### 3. Rubric 评分方法论

评分维度：
- **回答质量（Answer Quality）**：完整性、准确性、适当性
- **来源可靠性（Source Reliability）**：引用的可验证性和正确性

评分机制：
- **正分**：满足任务要求的加分
- **负分**：错误或幻觉的扣分
- **最终得分** = 模型完成了律师级工作成果的百分比

### 4. Workflows — SPA 交易要点提取

评测 LLM Agent 从股权购买协议（SPA）中提取标准交易要点的能力。提供完整的 JSON Schema，涵盖 19 个核心字段：

- 基本信息：effective_date、purchaser、seller、company（含 name/entity_type/jurisdiction）
- 交易条款：price（含 base_purchase_price/price_per_share/number_of_shares）、closing_deliverables、purchase_price_adjustments
- 资本结构：capitalization（shares_outstanding/pro_forma_percentage）
- 赔偿条款：indemnification（buyer/seller 各有 survival/liability_cap/basket）
- 履约条件：conditions_to_sellers_obligations、conditions_to_buyers_obligations
- 法律条款：governing_law、dispute_resolution_jurisdiction、termination_rights、transfer_taxes、fees_and_expenses、notices

### 5. Retrieval — 两类检索任务

- **合同文档检索**：数百页文档，含交叉引用和定义术语，支持 Merger Agreements 和 SPA
- **电子发现邮件检索**：短文档但高数量，复杂关系（邮件线程）+ 丰富元数据（发件人/收件人/附件），数据来源为美国国务院 FOIA 公开邮件

### 6. 核心 Rubric 示例

从 core-samples.csv 可见真实的 rubric 结构：
- 每个任务配有一段 prompt + 关联文档 + 详细 rubric
- Rubric 分为 "Affirmative Points"（正分）和扣分项
- 任务涉及真实法律文书（法院令、合并协议等）

## 重要数据

- 团队规模：18 人（Harvey 法律研究团队）
- 公开数据为样本子集，完整数据集需联系 Harvey 获取
- 将持续更新追加新数据集

## 问题与思考

1. 正/负分评分机制很有价值——BigLaw Bench 和 HealthBench 都采用了这种模式，这是 Rubric-Based 评测的一个重要实践模式
2. SPA 提取的 JSON Schema 为结构化信息提取评测提供了很好的范式
3. 检索任务的纳入说明 BigLaw Bench 的覆盖面远超传统的 QA benchmark
4. 公开数据有限，制约了独立复现

## 相关页面

- [BigLaw Bench](../benchmarks/biglaw-bench.md)
- [Harvey AI](../entities/harvey-ai.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [PRBench](../benchmarks/prbench.md) — 同为法律领域 benchmark
