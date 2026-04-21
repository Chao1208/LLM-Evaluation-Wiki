---
title: "BigLaw Bench"
type: benchmark
created: 2026-04-09
updated: 2026-04-09
tags: [benchmark, rubric, 法律, Harvey AI]
sources: [raw/report/Rubric-Based推理数据调研报告.md, raw/benchmarks/biglaw-bench/README.md]
---

# BigLaw Bench

## 基本信息

| 项目 | 详情 |
|------|------|
| 创建机构 | [Harvey AI](../entities/harvey-ai.md) |
| 领域 | 法律 |
| 团队规模 | 18 人（Harvey 法律研究团队） |
| 方法论 | [Rubric-Based 评测](../concepts/rubric-based-evaluation.md) |
| GitHub | https://github.com/harveyai/biglaw-bench |
| 博客 | https://www.harvey.ai/blog/introducing-biglaw-bench |
| 数据 | 公开样本子集，完整数据集需联系 Harvey 获取 |

## 评测维度

### 三层评测架构

| 层级 | 名称 | 说明 | 数据类型 |
|------|------|------|---------|
| Core | 核心任务 | 基准法律问题解决，16 种子任务类型 | CSV（prompt + rubric） |
| Workflows | 工作流 | 复合型法律任务，评估 Agent 系统 | CSV + JSON schema + PDF 合同 |
| Retrieval | 检索 | 评测检索系统质量 | CSV + PDF 文档（合同/邮件） |

（来源：[仓库文档](../sources/biglaw-bench-repo.md)）

### 核心任务分类（16 种）

**交易类（9 种）**：公司战略与咨询、文件起草、法律研究、尽职调查、风险评估与合规、谈判策略、交易管理、交易结构设计、监管与咨询

**诉讼类（7 种）**：诉讼文件分析、案件管理、文件起草、判例法研究、庭审记录分析、文件审查与分析、庭审准备与口头辩论

## 方法论

### Rubric 评分方法

评分维度：
- **回答质量（Answer Quality）**：完整性、准确性、适当性
- **来源可靠性（Source Reliability）**：引用的可验证性和正确性

评分机制：
- **正分（Affirmative Points）**：满足任务要求的加分
- **负分（Negative Points）**：错误或幻觉的扣分
- **最终得分** = 模型完成了律师级工作成果的百分比

### Workflows — SPA 交易要点提取

评测 LLM Agent 从股权购买协议（SPA）中提取标准交易要点的能力。提供完整的 JSON Schema，涵盖 19 个核心字段：

- 基本信息：effective_date、purchaser/seller/company（含 name/entity_type/jurisdiction）
- 交易条款：price（base_purchase_price/price_per_share/number_of_shares）、closing_deliverables、purchase_price_adjustments
- 资本结构：capitalization（shares_outstanding/pro_forma_percentage）
- 赔偿条款：indemnification（buyer/seller 各有 survival/liability_cap/basket）
- 履约条件：conditions_to_sellers_obligations / conditions_to_buyers_obligations
- 法律条款：governing_law、dispute_resolution_jurisdiction、termination_rights、transfer_taxes、fees_and_expenses、notices

### Retrieval — 两类检索任务

- **合同文档检索**：数百页文档，含交叉引用和定义术语，支持 Merger Agreements 和 SPA
- **电子发现邮件检索**：短文档但高数量，复杂关系（邮件线程）+ 丰富元数据（发件人/收件人/附件），数据来源为美国国务院 FOIA 公开邮件

## 局限性与争议

- 公开数据为样本子集，完整数据集需联系 Harvey 获取，独立复现受限
- 相比 [PRBench](prbench.md) 公开信息和数据较少
- 将持续更新追加新数据集

## 相关页面

- [Harvey AI](../entities/harvey-ai.md)
- [PRBench](prbench.md) — 同为法律领域 benchmark
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
- [仓库文档](../sources/biglaw-bench-repo.md)
