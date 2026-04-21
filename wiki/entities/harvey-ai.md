---
title: "Harvey AI"
type: entity
entity_type: organization
created: 2026-04-09
updated: 2026-04-09
tags: [机构, 法律AI, 评测]
sources: [raw/report/Rubric-Based推理数据调研报告.md, raw/benchmarks/biglaw-bench/README.md]
---

# Harvey AI

## 基本信息

专注于法律领域的 AI 公司。

## 评测贡献

- 开发了 [BigLaw Bench](../benchmarks/biglaw-bench.md)：面向大型律所的法律领域 Rubric-Based 评测基准
- 三层架构设计：Core（16 种法律任务）+ Workflows（SPA 交易要点提取，评估 Agent 系统）+ Retrieval（合同 + 电子发现邮件检索）
- 评分方法：正分（Affirmative Points）+ 负分（幻觉扣分），最终得分 = 律师级工作完成度百分比
- 团队规模 18 人，以法律研究专家为主
- 提供公开样本数据，完整数据集需联系获取

## 核心团队

Julio Pereyra, Elizabeth Lebens, Matthew Guillod, Laura Toulme, Cameron MacGregor, David Murdter, Karl de la Roche, Emilie McConnachie, Jeremy Pushkin, Rina Kim, Aaron Chan, Jenny Pan, Boling Yang, Nan Wu, Niko Grupen, Lauren Oh, Aatish Nayak, Gabriel Pereyra

## 相关页面

- [BigLaw Bench](../benchmarks/biglaw-bench.md)
- [调研报告](../sources/rubric-based-reasoning-data-survey.md)
- [BigLaw Bench 仓库文档](../sources/biglaw-bench-repo.md)
