---
title: "SuperCLUE: 中文通用大模型综合性基准 (GitHub 仓库)"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [benchmark, 中文评测, 大模型, SuperCLUE, Agent, 安全, 多轮对话]
sources: [raw/github/SuperCLUE/README.md]
---

## 概述

SuperCLUE 是由 CLUEbenchmark 团队开发的中文通用大模型综合性评测基准，是 [CLUE](../sources/clue-repo.md)/[ChineseGLUE](../sources/chineseglue-repo.md) 的大模型时代升级版。该基准从传统 NLU 任务评测转向大模型综合能力评测，覆盖语言理解与生成、专业技能与知识、AI Agent 智能体和安全性四大能力象限，细化为 **12 项基础能力**。

- 技术报告：[SuperCLUE: A Comprehensive Chinese Large Language Model Benchmark](https://arxiv.org/abs/2307.15020)
- 官网：[www.SuperClueAI.com](https://www.superclueai.com/)
- 最新动态：2025 年度中文大模型基准测评报告已发布
- 子基准：
  - **SuperCLUE-Agent** -- AI 智能体中文原生任务评估
  - **SuperCLUE-Safety** -- 中文大模型多轮对抗安全基准

## 关键特性

1. **四大能力象限**：
   - 语言理解与生成（语义理解、对话、生成创作）
   - 专业技能与知识（计算、逻辑推理、代码、知识百科）
   - AI Agent 智能体（工具使用、任务规划）
   - 安全性（传统安全、对抗安全）

2. **双评测体系**：
   - **SuperCLUE-OPEN**：多轮开放问题（主观题），使用 GPT-4 Turbo 作为裁判模型
   - **SuperCLUE-OPT**：三大能力客观题（基础能力、中文特性、学术专业能力）
   - 总分 = OPEN + OPT 综合

3. **十大基础能力评估**：计算、逻辑推理、代码、知识百科、语言理解、生成创作、对话、角色扮演、工具使用、安全

4. **多轮对话评测**：核心特色之一，评测模型在多轮交互中的一致性和上下文保持能力

5. **月度更新榜单**：定期纳入新模型，持续追踪中文大模型发展趋势

6. **AI Agent 评估**：重点评估工具使用和任务规划两个关键能力

## 支持的模型/基准

2023年12月总排行榜（部分）：

| 排名 | 模型 | 机构 | 总分 | OPEN | OPT |
|:----:|------|------|:----:|:----:|:---:|
| - | GPT-4 Turbo | OpenAI | 90.63 | 90.89 | 90.03 |
| - | GPT-4(网页) | OpenAI | 83.92 | 80.76 | 91.28 |
| 1 | 文心一言4.0 | 百度 | 79.02 | 75.00 | 88.38 |
| 2 | 通义千问2.0 | 阿里巴巴 | 76.54 | 71.78 | 87.64 |
| 3 | AndesGPT | OPPO | 75.04 | 70.01 | 86.76 |
| 4 | 智谱清言 | 清华&智谱 | 74.11 | 69.91 | 83.92 |
| 5 | Moonshot | 月之暗面 | 71.92 | 67.25 | 82.81 |
| - | Claude2 | Anthropic | 67.43 | 65.14 | 72.77 |
| - | GPT-3.5 Turbo | OpenAI | 61.44 | 55.63 | 74.98 |

开源模型排行榜：

| 排名 | 模型 | 总分 |
|:----:|------|:----:|
| 1 | Qwen-72B-Chat | 69.69 |
| 2 | Yi-34B-Chat | 68.46 |
| 3 | Qwen-14B-Chat | 61.27 |
| 4 | Baichuan2-13B-Chat | 61.12 |
| 5 | ChatGLM3-6B | 49.50 |

## 技术架构

- **评测方法**：
  - OPEN 主观题：采用 GPT-4 Turbo 作为超级裁判模型（LLM-as-Judge），多轮对话形式
  - OPT 客观题：标准答案自动评分
- **评测规模**：OPEN 部分约 4,265 道题（持续扩展）
- **评测形式**：支持 API 调用、网页访问、本地模型三种接入方式
- **安全评测**：SuperCLUE-Safety 采用多轮对抗策略评估模型安全边界

## 与评测相关的启示

1. **LLM-as-Judge 的实践**：SuperCLUE-OPEN 使用 GPT-4 Turbo 做开放题裁判，是 [LLM-as-Judge](../concepts/llm-as-judge.md) 方法论的大规模中文实践
2. **主客观结合**：OPEN（主观）+ OPT（客观）双体系设计，平衡了评测的全面性和可靠性
3. **Agent 能力纳入评测**：反映了 LLM 评测从纯语言能力向实际应用能力（工具使用、任务规划）的扩展趋势
4. **安全性独立评测**：将安全作为独立能力维度，体现了对 AI 安全的重视
5. **中文大模型生态全景**：涵盖国内外主流大模型（百度、阿里、字节、月之暗面、OpenAI 等），提供了中文大模型发展的横向对比
6. **开源 vs 闭源差距**：榜单显示开源模型（Qwen-72B 约 69 分）与闭源模型（GPT-4 Turbo 约 91 分）仍有显著差距
7. **月度榜单的价值**：定期更新使评测保持时效性，避免一次性 benchmark 的快速过时问题
8. **评测演进路径**：从 ChineseGLUE（2019，NLU）→ CLUE（2020，NLU+）→ SuperCLUE（2023，LLM 综合），展示了中文评测随技术发展的演进

## 相关页面

- [CLUE benchmark](../sources/clue-repo.md) -- SuperCLUE 的前身（NLU 评测）
- [ChineseGLUE](../sources/chineseglue-repo.md) -- 最早期经典版本
- [C-Eval](../sources/ceval-repo.md) -- 中文知识考试型评测基准
- [MT-Bench & Chatbot Arena](../sources/mt-bench-chatbot-arena.md) -- 英文 LLM 多轮评测基准，SuperCLUE-OPEN 的参考对象
- [LLM-as-Judge](../concepts/llm-as-judge.md) -- SuperCLUE-OPEN 采用的评测范式
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md) -- 细粒度评测标准设计
- [GPT-4 Technical Report](../sources/gpt4-technical-report.md) -- SuperCLUE 排行榜第一名模型
