---
title: "RLHF（基于人类反馈的强化学习）"
type: concept
created: 2026-04-22
updated: 2026-04-22
tags: [RLHF, 强化学习, 对齐, 奖励模型, PPO, DPO, RLVR]
sources: []
---

# RLHF（基于人类反馈的强化学习）

## 定义

RLHF（Reinforcement Learning from Human Feedback）是一种通过人类偏好信号训练语言模型的对齐（alignment）技术。其核心思想是：先从人类标注者的偏好比较中学习一个奖励模型（Reward Model），再以该奖励模型作为奖励信号，通过强化学习优化语言模型的生成策略，使模型输出更符合人类期望。

RLHF 是当前大模型后训练（post-training）流程的核心环节，也是 ChatGPT、GPT-4 等产品级模型背后的关键技术。该方法弥补了传统监督学习（SFT）在捕捉人类偏好方面的不足——SFT 只能模仿标注数据中的行为，而 RLHF 能够在更广泛的输出空间中学习"什么是好的回答"。

## 经典三阶段流程

RLHF 的标准范式由 [InstructGPT](../sources/instructgpt.md)（OpenAI, 2022）奠定，包含三个阶段：

### 阶段一：监督微调（SFT）

使用人工编写的高质量示范数据对预训练模型进行监督微调，为后续 RL 训练提供良好的初始策略。

- InstructGPT 使用约 **13,000 条**标注者编写的示范数据
- [Llama 2](../sources/llama2.md)（Meta, 2023）仅用 **27,540 条**高质量标注就达到高水平，验证了"Quality Is All You Need"——少而精优于多而杂

### 阶段二：奖励模型训练（RM）

标注者对模型生成的多个输出进行排序或成对比较，训练一个奖励模型来预测人类偏好。

- InstructGPT 训练了一个 **6B 参数**的奖励模型，使用约 **33,000 条**比较数据
- Llama 2 使用了更精细的偏好标注体系：二元偏好比较 + 四级偏好程度标签（significantly better / slightly better / negligibly better / unsure），并训练了两个独立的奖励模型分别优化**有用性**（Helpfulness）和**安全性**（Safety）
- [RewardBench](../sources/rewardbench.md)（Lambert et al., NAACL 2025）发现不同奖励模型之间性能差距超过 **20 个百分点**，说明奖励模型质量对 RLHF 成败至关重要

### 阶段三：强化学习优化（PPO）

以奖励模型的输出作为奖励信号，通过 Proximal Policy Optimization（PPO）算法优化语言模型策略。

- InstructGPT 的核心成果：仅 **1.3B** 参数的 InstructGPT 在人类偏好评测中优于 **175B** 参数的 GPT-3，证明对齐技术能以极小的参数代价带来巨大的用户体验提升（来源：[InstructGPT](../sources/instructgpt.md)）
- Llama 2 在 PPO 之前先使用 **Rejection Sampling（拒绝采样）**——从 K 个候选中选最优，再用 PPO 精调（来源：[Llama 2](../sources/llama2.md)）
- Llama 2 还提出了 **Ghost Attention（GAtt）** 技术解决多轮对话中系统提示被"遗忘"的问题

## 演进方向

经典 RLHF 面临 PPO 训练不稳定、奖励模型偏差（reward hacking）、标注成本高等挑战，推动了多种改进方向的出现。

### DPO：去掉奖励模型

**Direct Preference Optimization**（DPO）跳过了显式的奖励模型训练阶段，直接从偏好数据中优化语言模型策略。[Tulu 3](../sources/2411-recent-eval.md)（Ai2, 2024）采用了 SFT → DPO → RLVR 的三阶段后训练流程，其中 DPO 使用 on-policy 偏好数据进行优化，在多个 benchmark 上超越同级别闭源模型（来源：[Tulu 3](../sources/2411-recent-eval.md)）。

### RLVR：可验证奖励

**Reinforcement Learning with Verifiable Rewards**（RLVR）用程序化可验证的奖励信号（如数学答案正确性、指令遵循的约束满足度）替代人类标注，避免了奖励模型的偏差问题。Tulu 3 在 GSM、MATH 和 IFEval 三个领域验证了 RLVR 的有效性。但 RLVR 的局限在于仅适用于有明确可验证答案的任务，对开放式生成任务的适用性有限（来源：[Tulu 3](../sources/2411-recent-eval.md)）。

### GRPO：高效策略优化

**Group Relative Policy Optimization**（GRPO）是替代传统 PPO 的高效 RL 算法。[DR Tulu](../sources/dr-tulu-repo.md)（Ai2, 2025）使用 GRPO + Evolving Rubrics 训练深度研究 agent，8B 模型匹配商业级 Deep Research 系统的表现。

### Rubric-Driven RL：结构化奖励信号

将 [Rubric-Based 评测方法论](rubric-based-evaluation.md) 中的评分标准作为强化学习的奖励信号，是 RLHF 演进中最值得关注的方向之一。

**Rubicon 框架**（蚂蚁集团/浙江大学, 2025）：[RL with Rubric Anchors](../sources/rl-rubric-anchors.md)（[新闻报道](../sources/rubicon-news.md)）提出了系统化的 Rubric 驱动 RL 框架：
- 评分系统 = K 个维度 x (标准描述 c_k, 评分等级 T_k, 权重 w_k)
- 高级奖励聚合策略：**否决机制（Veto）**、饱和度感知聚合、成对交互建模、目标性奖励塑造
- 核心成果：仅 **5,000+ 样本** 让 30B 模型在人文社科开放式基准上击败 **671B** DeepSeek-V3
- 有效缓解 reward hacking 风险，增强奖励信号的可解释性

**Evolving Rubrics**（DR Tulu, 2025）：[DR Tulu](../sources/dr-tulu-repo.md) 提出评分标准不是静态的，而应随训练进程**动态演化**——初期使用较宽松标准，随模型能力提升逐步引入更严格、更细粒度的标准，类似 curriculum learning。

Rubric-Driven RL 的核心价值在于：将 RLVR 从"严格可验证任务"扩展到"开放式任务"，用结构化的评分标准替代简单的标量奖励，同时天然提供可解释性。

## 已知局限与挑战

| 挑战 | 描述 | 来源 |
|------|------|------|
| Reward Hacking | 模型学会利用奖励模型漏洞获取高分，而非真正提升质量 | [InstructGPT](../sources/instructgpt.md)、[Rubicon](../sources/rubicon-news.md) |
| 代理目标问题 | 对齐的是标注者偏好而非"真正的人类价值观" | [InstructGPT](../sources/instructgpt.md) |
| PPO 训练不稳定 | 需要精心调参，训练过程容易发散 | [InstructGPT](../sources/instructgpt.md) |
| 标注成本高 | 高质量人类偏好数据采集成本大 | 通用挑战 |
| 安全-有用性张力 | 安全性与有用性存在 trade-off，需独立优化 | [Llama 2](../sources/llama2.md) |
| "对齐税" | RLHF 可能在部分学术 NLP 基准上造成轻微性能下降 | [InstructGPT](../sources/instructgpt.md) |
| 跷跷板效应 | 不同任务联合 RL 训练时性能相互干扰 | [Rubicon](../sources/rubicon-news.md) |

## 与评测的关系

RLHF 与 LLM 评测之间存在深度的双向关系：

1. **评测驱动 RLHF 优化**：评测结果指导 RLHF 训练方向。Tulu 3 明确提出"评估驱动开发"——使用 development eval 指导每一步设计决策（来源：[Tulu 3](../sources/2411-recent-eval.md)）

2. **奖励模型需要评测**：[RewardBench](../sources/rewardbench.md) 证明了评测奖励模型本身的必要性——不同奖励模型差距显著，安全类任务是普遍薄弱环节，推理类任务区分度最高

3. **评分标准 = 奖励信号**：[Rubric-Based 评测方法论](rubric-based-evaluation.md)中的 criteria 正在被直接用作 RL 训练的奖励信号（Rubicon、DR Tulu），评测标准与训练奖励形成统一闭环

4. **[LLM-as-Judge](llm-as-judge.md) 作为奖励代理**：RewardBench 发现生成式模型（如 GPT-4）作为评判器的表现与专门训练的奖励模型相当甚至更优，模糊了"评测"与"奖励模型"的边界

5. **评测暴露对齐弱点**：RLHF 后的模型在安全性、真实性等维度上的表现需要专门的 benchmark 来度量，如 TruthfulQA、RealToxicityPrompts 等

## 相关页面

- [InstructGPT](../sources/instructgpt.md) — RLHF 里程碑工作
- [Llama 2](../sources/llama2.md) — Rejection Sampling + PPO 实践
- [Tulu 3](../sources/2411-recent-eval.md) — SFT/DPO/RLVR 后训练流程
- [DR Tulu](../sources/dr-tulu-repo.md) — GRPO + Evolving Rubrics
- [RL with Rubric Anchors](../sources/rl-rubric-anchors.md) — Rubric 驱动 RL 论文
- [Rubicon 框架报道](../sources/rubicon-news.md) — Rubric 奖励机制新闻解读
- [RewardBench](../sources/rewardbench.md) — 奖励模型评测基准
- [Rubric-Based 评测方法论](rubric-based-evaluation.md) — Rubric 在评测与 RL 中的方法论
- [LLM-as-Judge](llm-as-judge.md) — LLM 作为评判器/奖励代理
- [OpenAI](../entities/openai.md)
- [Meta](../entities/meta.md)
