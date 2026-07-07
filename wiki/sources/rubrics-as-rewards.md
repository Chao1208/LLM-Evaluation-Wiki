---
title: "Rubrics as Rewards (RaR): 超越可验证领域的强化学习"
type: source
created: 2026-07-08
updated: 2026-07-08
tags: [rubric, RL, 奖励模型, RLVR, GRPO, Scale-AI, 医疗, 科学, HealthBench]
sources: [2507.17746-rubrics-as-rewards.pdf]
---

# Rubrics as Rewards (RaR)

## 概述

RaR（Rubrics as Rewards）是 [Scale AI](../entities/scale-ai.md) 提出的 on-policy 强化学习框架，把**结构化 checklist 式 rubric 直接当作奖励信号**，将 [RLVR](../concepts/rlhf.md)（可验证奖励强化学习）从数学/代码等可验证任务，扩展到医学、科学等**不可验证的真实世界推理领域**。每个 prompt 配一组实例特定的 rubric criteria，由 LLM judge 逐条检查后聚合成标量奖励，驱动 GRPO 策略优化。最佳变体在 [HealthBench](healthbench.md) 上相对提升达 **31%**，GPQA-Diamond 上提升 **7%**。

**论文信息**: arXiv:2507.17746v2, 2025 年 10 月（v1 为 2025 年 7 月）。作者 Anisha Gunjal、Anthony Wang 等，Scale AI。

## 关键发现

1. **RLVR 是 RaR 的特例**（Remark 1）：当 rubric 只有 1 条、权重为 1、criterion 退化为 `match(y, ŷ)` 时，RaR 即还原为 RLVR。RaR 把单一标量奖励泛化为多维、prompt 特定的监督信号。
2. **隐式聚合（RaR-Implicit）最强**：把全部 criteria + 类别权重交给 LLM judge 产出单一整体 Likert 分（归一化到 [0,1]），无需手工调权，稳定优于 Direct-Likert（HealthBench +31% 相对）和 Reference-Likert。
3. **实例特定 rubric 是关键**：RaR-Predefined（对所有 prompt 用通用 rubric）表现最差（HealthBench 仅 12.5），远逊于实例特定的 RaR-Explicit（29.7）/ RaR-Implicit（31.2）。
4. **参考答案锚定至关重要**：带参考答案生成的合成 rubric 显著优于无参考版本；合成 rubric（35.9）甚至略超人工 rubric（34.8）。
5. **权重和 pitfall 影响很小**：去掉类别标签或 pitfall（负向）criteria 影响甚微——作者推测**合成负向 criteria 难以生成好**，需要人类直觉/领域专长来预判失败模式。

## 方法论

### 形式化

每个 prompt `x` 配 `k` 条 rubric 项 `{(w_j, c_j)}`，`w_j` 为权重，`c_j` 为二值 criterion。两种奖励聚合：

- **显式聚合（Explicit）**：归一化加权和 `r = Σ w_j·c_j / Σ w_j`，每条由 LLM judge 独立评判。
- **隐式聚合（Implicit）**：全部 criteria + 类别权重交给 judge 产出单一整体分。

### Rubric 生成四原则

1. **专家指导锚定**（以参考答案作专家代理）
2. **全面覆盖**（事实、连贯、完整、风格、安全；含负向 pitfall 项）
3. **重要性分级**（Essential / Important / Optional / Pitfall，权重 1.0 / 0.7 / 0.3 / 0.9）
4. **自包含评判**（每条可独立判定）

由强 LLM（o3-mini、GPT-4o）以参考答案为条件生成，每 prompt 7–20 条。

### 训练配置（GRPO）

- 基座策略：**Qwen2.5-7B**；judge：**gpt-4o-mini**
- 每 prompt 采样 k=16 rollout，温度 1.0，上下文 3584
- 有效 batch 96，学习率 5e-6，300 步，8× H100
- Scale AI 内部后训练平台 **RLXF**

## 重要数据

| 数据集 | 规模 | 平均 rubric/题 | rubric 生成模型 |
|--------|------|----------------|-----------------|
| RaR-Medicine | 20,166 | 7.5 | GPT-4o |
| RaR-Science | 20,625 | 7.5 | o3-mini |

**HealthBench 总分（gpt-4o-mini judge）**：

| 方法 | 得分 |
|------|------|
| Qwen2.5-7B（基座） | 7.7 |
| Qwen2.5-7B-Instruct | 22.7 |
| Direct-Likert | 25.5 |
| Reference-Likert | 28.9 |
| RaR-Predefined | 12.5 |
| RaR-Explicit | 29.7 |
| **RaR-Implicit** | **31.2** |

GPQA-Diamond（10 次运行均值）：RaR-Implicit 37.6% vs Direct-Likert 34.8%。

## 对我们研究的启示

- **评测 rubric ↔ 训练 rubric 的统一**：RaR 与 [Rubicon](rl-rubric-anchors.md) 一样把评测标准直接当 RL 奖励，印证了 [RLHF 页](../concepts/rlhf.md)中"评分标准 = 奖励信号"的闭环趋势。HealRub 若要把评测 rubric 复用于训练，RaR 的隐式聚合是低成本起点。
- **负向 criteria 的矛盾**：RaR 发现合成 pitfall 项作用有限，[RubricHub](rubrichub.md) 进一步发现加惩罚项**反而降低** RL 性能，[Reflect-and-Revise](reflect-and-revise.md) 引用的 Furuhashi 则说删项能提升——三方均指向"负向/惩罚项在 LLM 场景与人类直觉不同"，这与本 Wiki [Rubric-Based 方法论](../concepts/rubric-based-evaluation.md)中评测场景推荐 7:3~8:2 正负比存在张力，值得单独深挖。
- **参考答案是合成 rubric 质量的命门**：无参考的纯合成 rubric 在高风险领域不够用——对 HealRub 的医疗 rubric 自动生成有直接警示。

## 问题与思考

- 实验仅限医学、科学两个可控领域，对话/工具调用/agentic 任务未验证。
- 仅探索隐式/显式两种聚合，学习式连续权重、课程式动态加权留待未来。
- 用现成 LLM 作 judge 保证可复现，专用生成式奖励模型可能带来更多增益。

## 相关页面

- [Scale AI](../entities/scale-ai.md)
- [RLHF](../concepts/rlhf.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [HealthBench](healthbench.md)
- [OpenRubrics](openrubrics.md)
- [RubricHub](rubrichub.md)
- [Reflect-and-Revise](reflect-and-revise.md)
- [RL with Rubric Anchors (Rubicon)](rl-rubric-anchors.md)
- [DR Tulu](dr-tulu-repo.md)
- [PRBench](../benchmarks/prbench.md)
