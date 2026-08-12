---
title: "标注噪声与标注流水线质量"
type: concept
created: 2026-08-10
updated: 2026-08-10
tags: [标注噪声, 标注流水线, 众包, 可识别性, 核验偏差, 选择性标注, 弱监督, 标签聚合, PPI, 主动测试, LLM-as-Judge]
sources: [dawid-skene-1979-observer-error-rates-em.pdf, hcomp2013-baba-kashima-statistical-quality-estimation-crowdsourcing.pdf, lu-joseph-2010-bayesian-verification-reference-standard-bias.pdf, dendukuri-joseph-2001-bayesian-conditional-dependence-multiple-diagnostic-tests.pdf, pvldb2015-argonaut-macrotask-crowdsourcing-complex-data-processing.pdf, aij2013-turkontrol-pomdp-control-crowdsourcing-workflows.pdf, jmlr2016-spectral-methods-meet-em-optimal-crowdsourcing.pdf, aistats2012-kim-bayesian-classifier-combination.pdf, icml2019-li-exploiting-worker-correlation-label-aggregation.pdf, ijcai2021-gemalmaz-yin-confirmation-bias-label-aggregation.pdf, 1605.07723-data-programming-creating-large-training-sets-quickly.pdf, 1711.10160-snorkel-weak-supervision.pdf, 2607.15455-design-based-supervised-learning-noisy-human-labels.pdf, 2606.15031-partial-identification-from-llm-prompts.pdf, 2605.29800-nine-judges-two-effective-votes.pdf, 2210.06812-crowdlab-consensus-labels-quality-scores.pdf, 2103.05331-active-testing-sample-efficient-model-evaluation.pdf, 2408.15204-confidence-driven-inference-llm-annotations.pdf]
---

# 标注噪声与标注流水线质量

## 定义

本页覆盖一个与「评测基准怎么设计」互补的问题：**评测与训练所依赖的人工标签本身是有噪声的，而产生这些标签的流水线有结构**。当标签由「标注 → 抽检质检 → 裁定」这样的多阶段流程产出、且没有任何一层可信真值（gold label）时，我们能否从观测数据中恢复真实的模型能力、标注者可靠性与数据质量？这个问题横跨七个原本互不相通的社区：众包系统、诊断医学统计、计量经济学、弱监督、标签噪声学习、样本高效评测、以及新兴的 LLM-as-Judge 可信度研究。

核心概念记号（本页统一）：潜真值 $Y$、第一阶段标注 $A$、第二阶段质检结论 $Q$、抽检指示 $R$、辅助/LLM 通道 $Z$、样本特征 $x$。

## 为什么这对 LLM 评测重要

1. **评测分数的上界由标签质量决定**。[HealthBench](../benchmarks/healthbench.md) 的元评测显示 grader-医师一致性≈医师间一致性（55–75%）——这个数字既是 judge 可信度的证据，也是标签噪声的天花板：没有任何评测能比它的参考标签更准。
2. **多 judge 投票的收益被误差相关性吃掉**。「多加几个 LLM judge 就更可靠」这一直觉在实测中不成立（见簇 5）。
3. **抽检不是随机的**。真实流水线按可疑度抽检，这使朴素的「抽检通过率」不是无偏质量估计——这一点在众包系统侧 2015 年就已被写明（Argonaut §4.4）。
4. **无 gold 时可识别性有硬上界**。误差任意相关时，prevalence 的识别集可以退化为 $[0,1]$（簇 3）；识别力只能来自外部校准或流程干预，不能来自「更聪明的模型」。

## 主题簇总览

第一批（2026-08-10 摄入，84 篇）建立「问题有多难」的理论骨架；第二批（2026-08-12 摄入，71 篇）转向「用什么流程干预能买回识别力」的跨领域实证先例。

| 簇 | 主题 | 核心问题 | 论文数 |
|---|---|---|---|
| 1 | 标签聚合与观测者误差模型 | 无 gold 下如何联合估计真值与标注者可靠性 | 12 |
| 2 | 两阶段流水线与众包系统 | 「标注→质检」串行结构如何形式化与控制 | 15+5 |
| 3 | 核验偏差 / 选择性标注 / 可识别性 | 抽检非随机 + 参考标准不完美时能识别什么 | 17+5 |
| 4 | 弱监督与标注函数依赖 | 程序化噪声源之间的依赖结构如何建模 | 8 |
| 5 | LLM-as-Judge 的误差相关性与可信推断 | LLM 通道是独立证据还是相关重复 | 10 |
| 6 | 样本高效评测（PPI / active testing） | 有限 gold 预算下如何做有效统计推断 | 11 |
| 7 | 标签噪声基准与标注协议 | 可控噪声注入与真实噪声数据集 | 11 |
| **8** | **盲验证与依赖验证（跨领域先例）** | 第二审核者该不该看到第一位的结论 | **16** |
| **9** | **锚定效应与评审者相互影响** | 锚定的效应量到底有多大、方向是否一致 | **9** |
| **10** | **抽检设计 / 验收抽样 / 序贯审计** | 检验本身有误差时如何设计抽样方案 | **13** |
| **11** | **干预设计买识别力（因果实验设计）** | 最小成本的干预集合如何选 | **4** |
| **12** | **部分识别与自适应采集数据的推断** | 点识别拿不到时能给出什么界 | **12** |

> 下文每簇给出「已确认结论」与该簇的论文索引表。**索引表中未单独建 source 页的条目，其描述基于标题与所属研究页的引用语境，未逐篇通读全文**——引用前请核实原文。已建 source 页的条目以链接标出。


### 簇 1：标签聚合与观测者误差模型

**已确认结论**：这条线的奠基工作是 [Dawid-Skene (1979)](../sources/dawid-skene-1979-observer-error-rates-em.md)——用 EM 从多个并行标注者的结果中同时估计潜真值与每人的混淆矩阵。它依赖**给定真值后标注者条件独立**这一假设，而原文第 2 节已自我批评存在 intervening variables、条件独立只是「为了能估出错误率而目前必需」。

放松条件独立是一条完整活跃的支线，但既有依赖建模几乎全部是**无向的**（共享潜因子或对称协方差），而非有向的（一个通道看得到另一个通道的输出）。

| 论文 | 年份 | 会议/期刊 | 依赖建模方式 |
|---|---|---|---|
| [Dawid & Skene, Observer Error-Rates via EM](../sources/dawid-skene-1979-observer-error-rates-em.md) | 1979 | JRSS-C | 条件独立（奠基） |
| Bayesian Classifier Combination (DBCC) | 2012 | AISTATS | 共享潜结构，无向 |
| Unsupervised Ensemble Learning with Dependent Classifiers | 2015 | arXiv 1510.05830 | 潜因子分解，无向 |
| Deep Learning for Unsupervised Ensemble Learning | 2016 | arXiv 1602.02285 | 神经网络建模依赖 |
| Spectral Methods meet EM: Provably Optimal Crowdsourcing | 2016 | JMLR | 条件独立下最优 |
| Crowdsourcing via Pairwise Co-occurrences | 2019 | arXiv 1909.12325 | 成对共现张量分解 |
| Exploiting Worker Correlation (EBCC) | 2019 | ICML | 子类型建模，无向；批评参数量随人数平方增长 |
| Confirmation Bias in Label Aggregation | 2021 | IJCAI | 确认偏差对聚合的影响 |
| [CrowdLab: Consensus Labels + Quality Scores](../sources/2210.06812-crowdlab-consensus-labels-quality-scores.md) | 2022 | arXiv 2210.06812 | 结合训练好的分类器与多噪声标注者 |
| Dependence-Aware Label Aggregation via Ising Models | 2026 | arXiv 2601.22336 | Ising 对称耦合，无向 |
| CARE: Confounder-Aware Aggregation | 2026 | arXiv 2603.00039 | 共享混淆变量，无向；已占「无 gold 可识别性」话语 |
| Allman-Matias-Rhodes, Identifiability of Latent Structure | 2008 | arXiv 0809.5032 | Kruskal 三线性分解的可识别性基础 |

### 簇 2：两阶段流水线与众包系统

**已确认结论**：真实工业标注流水线的「创建 → 审阅」两阶段结构，在众包系统社区早已被形式化。[Baba & Kashima (2013)](../sources/hcomp2013-baba-kashima-statistical-quality-estimation-crowdsourcing.md) 把它建成无监督潜变量模型并联合估计两阶段可靠性，基线直接对 Dawid-Skene。Argonaut (PVLDB 2015) §4.4 更进一步写明：**按可疑度选出的复核样本偏向高错误分、不能用于无偏估计**，并给出实践解法（复核预算里留一层随机抽检）。

这意味着「首次形式化两阶段流水线」不是可主张的新颖性，而「留不留随机抽检层带来多少识别力差异」才是尚未被定量回答的问题。

| 论文 | 年份 | 会议/期刊 | 关键点 |
|---|---|---|---|
| [Baba & Kashima, Statistical Quality Estimation](../sources/hcomp2013-baba-kashima-statistical-quality-estimation-crowdsourcing.md) | 2013 | HCOMP（摘要版）/ KDD | creation+review 两阶段无监督潜变量模型 |
| Argonaut: Macrotask Crowdsourcing | 2015 | PVLDB | 两层 review hierarchy + TaskGrader；§4.4 抽检偏差与随机层解法 |
| TurKontrol: POMDP-based Workflow Control | 2013 | AIJ | 迭代改进-投票工作流，显式建模 review 决策 |
| Anchoring and Agreement in Syntactic Annotations | 2016 | EMNLP 1605.04481 | 预标注造成锚定，一致率虚高 |
| Analyzing Dataset Annotation Quality Management in the Wild | 2024 | Computational Linguistics 2024.cl-3.1 | 真实标注质量管理调研 |
| Generalizable Error Modeling for Human Data Annotation | 2024 | ACM JDIQ 2310.05286 | 工业级搜索标注错误建模 |
| On Efficient and Statistical Quality Estimation for Annotation | 2024 | arXiv 2405.11919 | 抽检量的统计效率 |
| Statistical Methods for Auditing Quality of Manual Content Reviews | 2023 | arXiv 2306.07466 | 审计方法学；提议 A/B 设计 |
| Position: Early-Stage QA Is More Cost-Effective | 2026 | arXiv 2605.15714 | 立场论文：早期质检优于后期验证 |
| Who Annotates in NLP? Annotation Reporting Audit | 2026 | arXiv 2606.02255 | 标注者信息报告规范审计 |
| From Ground Truth to Measurement | 2026 | arXiv 2604.07591 | 人类标注的统计测量框架 |
| Adjudicator: KG-Informed Council of LLM Agents | 2025 | arXiv 2512.13704 | LLM 裁定层 |
| ANLI: Adversarial NLI | 2019 | arXiv 1910.14599 | writer-validator 两阶段 benchmark 构建 |
| Bayesian Model for Multi-Stage Censoring | 2025 | arXiv 2511.11684 | 多阶段删失的贝叶斯建模 |
| Analyzing Dataset Annotation Quality Management (preprint) | 2023 | arXiv 2307.08153 | 同上 CL 版的预印本 |

### 簇 3：核验偏差 / 选择性标注 / 无 gold 可识别性

**已确认结论**：这是全局最关键也最拥挤的一簇。三条硬结论：

1. **MAR vs MNAR 是定义性区分**。抽检若只依赖已观测的 $(A, x)$，机制是 **MAR**（随机缺失），Begg-Greenes / IPW 配贝叶斯 LCA 已足够；只有当抽检者动用了 $(A,x)$ 之外关于真值的信息，才是真 MNAR。把「抽检依赖标注结果」直接叫 MNAR 是错的。
2. **自由度充分性论证不成立**。Jones et al. (Biometrics 2009，引 Goodman 1974) 明确否掉「自由度 ≥ 参数个数 ⇒ 可识别」。参数维数大于可观测维数可以证明不可识别，反向不成立。
3. **误差任意相关时存在硬上界**。[Partial Identification from LLM Prompts](../sources/2606.15031-partial-identification-from-llm-prompts.md) 证明误差任意相关时 prevalence 识别集为 $[0,1]$——识别力只能来自外部校准，不能来自模型能力。

另一条关键结论来自 [PA-DSL](../sources/2607.15455-design-based-supervised-learning-noisy-human-labels.md)：设计基（design-based）推断的有效性要求**抽检与裁定概率由前瞻性设计已知**，它明确排除抽检规则未记录的回顾式流水线。这划出了「回顾式数据能做什么」与「必须前瞻性采集什么」的边界。

| 论文 | 年份 | 会议/期刊 | 关键假设 |
|---|---|---|---|
| Lu, Joseph et al., Verification + Reference Standard Bias | 2010 | Statistics in Medicine | 同时处理核验偏差与参考标准不完美 |
| Dendukuri & Joseph, Conditional Dependence Between Tests | 2001 | Biostatistics | 条件协方差 $\{cov_p, cov_n\}$ 参数化 |
| [PA-DSL: Design-Based SL with Noisy Human Labels](../sources/2607.15455-design-based-supervised-learning-noisy-human-labels.md) | 2026 | arXiv 2607.15455 | 自动标注/审计/裁定三层；抽检概率须由设计已知 |
| [Partial Identification from LLM Prompts](../sources/2606.15031-partial-identification-from-llm-prompts.md) | 2026 | arXiv 2606.15031 | 任意相关时识别集为 $[0,1]$ |
| Design-Based SL with Imperfect Surrogates (DSL) | 2023 | arXiv 2306.04746 | 代理标签 + 已知概率 gold 子样本 |
| Nonignorable Missing Data with a Shadow Variable | 2015 | arXiv 1509.02556 | shadow variable 识别 MNAR，要求精确条件独立 |
| VUS Estimation under Nonignorable Verification Bias (IV) | 2019 | arXiv 1906.08735 | 非可忽略核验 + 工具变量 |
| From Biased Selective Labels to Pseudo-Labels (EM) | 2024 | arXiv 2406.18865 | 选择性标注下的 EM 伪标签 |
| Learning with Selectively Labeled Data, Multiple Decision-makers | 2023 | arXiv 2306.07566 | 决策者异质性作识别源 |
| Robust Design under Unobserved Confounding | 2022 | arXiv 2212.09844 | 未观测混淆下部分识别 / 敏感性分析 |
| Robust Classifier under MNAR Sample Selection Bias | 2023 | arXiv 2305.15641 | MNAR 选择偏差下的鲁棒分类 |
| Inference for High-Dimensional Selective Labeling Models | 2024 | arXiv 2410.18381 | 高维选择性标注推断 |
| Model-Agnostic Selective Labeling with Provable Guarantees | 2025 | arXiv 2510.14581 | 模型无关的选择性标注保证 |
| Correcting Partial Verification Bias（R 教程） | 2025 | arXiv 2509.12217 | 部分核验偏差校正实操 |
| DAGs for Common Biases in Diagnostic Test Accuracy | 2026 | arXiv 2601.12167 | 用 DAG 刻画诊断准确性的常见偏差 |
| Evaluating Predictions under Shift and Selection Bias | 2026 | arXiv 2606.14506 | 分布漂移 + 选择偏差下的评测 |
| Model Evaluation in the Dark: Missing Labels | 2025 | arXiv 2504.18385 | 标签缺失下的模型评测 |

### 簇 4：弱监督与标注函数依赖

**已确认结论**：弱监督社区早已建模噪声源之间的**有向依赖**——[Data Programming](../sources/1605.07723-data-programming-creating-large-training-sets-quickly.md) 定义了标注函数间的 fixing / reinforcing 依赖类型。这说明「有向依赖建模」本身不新。但这条线的方法（三元组法、张量分解）几乎都要求**精确条件独立**：任意 $\varepsilon$ 违背一般就导致不一致，且偏差不自动是 $O(\varepsilon)$——所以「弱相关通道」不是一个可用的数学假设。

| 论文 | 年份 | 会议/期刊 | 关键点 |
|---|---|---|---|
| [Data Programming](../sources/1605.07723-data-programming-creating-large-training-sets-quickly.md) | 2016 | NeurIPS 1605.07723 | 标注函数 fixing / reinforcing 有向依赖 |
| [Snorkel](../sources/1711.10160-snorkel-weak-supervision.md) | 2017 | arXiv 1711.10160 / VLDB | 弱监督系统，生成式标签模型 |
| Multi-Task Weak Supervision (MeTaL) | 2018 | arXiv 1810.02840 | 多任务弱监督层次结构 |
| Learning Dependency Structures for Weak Supervision | 2019 | arXiv 1903.05844 | 自动学习标注函数依赖结构 |
| FlyingSquid: Fast and Three-rious | 2020 | arXiv 2002.11955 | 三元组法，需条件独立三元组 |
| Dependency Structure Misspecification in Weak Supervision | 2021 | arXiv 2106.10302 | 依赖结构误设的后果 |
| Language Models in the Loop | 2022 | arXiv 2205.02318 | prompt 即标注函数 |
| LLMs for Structure Learning in Prompted Weak Supervision | 2024 | arXiv 2402.01867 | LLM 学依赖结构 |

### 簇 5：LLM-as-Judge 的误差相关性与可信推断

**已确认结论**：这是本页对 [LLM-as-Judge](llm-as-judge.md) 最直接的补充——**「多加 judge = 更多独立证据」在实测中不成立**。[Nine Judges, Two Effective Votes](../sources/2605.29800-nine-judges-two-effective-votes.md) 实测 judge 间平均相关后，9 个 judge 的 Kish 有效样本量只相当于约 2 票。Great Models Think Alike 用 CAPA 度量给出同向证据：模型越强、彼此误差越像，这直接削弱「用强模型监督强模型」的 AI oversight 前提。

推论：**近乎无限的 LLM token 买到的是高度相关的重复，不是有效通道数的增长**。LLM 通道的条件独立性/外生性是待检验的经验命题，而不能作为假设直接使用。

另一条重要结论来自 rating indeterminacy 研究：当任务本身允许多个合法答案时，$Y^*$ 不是良定义的单一潜真值——此时任何「可识别性定理」只是在刻画一个误设模型的伪真参数。这是 caption 质量、开放式回答等主观任务的根本困难。

| 论文 | 年份 | 会议/期刊 | 关键点 |
|---|---|---|---|
| [Nine Judges, Two Effective Votes](../sources/2605.29800-nine-judges-two-effective-votes.md) | 2026 | arXiv 2605.29800 | 相关误差使 judge 面板有效票数远低于名义数 |
| Great Models Think Alike | 2025 | arXiv 2502.04313 | CAPA 度量模型间误差相似度，削弱 AI oversight |
| Validating LLM-as-a-Judge under Rating Indeterminacy | 2025 | arXiv 2503.05965 | $Y^*$ 非良定义时定理只刻画伪真参数 |
| Bridge Human-LLM Judgments | 2025 | arXiv 2508.12792 | 人类与 LLM 判断的桥接 |
| Efficient Inference with a Noisy LLM Judge | 2026 | arXiv 2601.05420 | 噪声 judge 下的高效推断 |
| Best Arm Identification with LLM Judges + Limited Human Audit | 2026 | arXiv 2601.21471 | LLM judge + 少量人工审计做模型选择 |
| Evaluating LLM under Corrupted Crowdsourcing without Ground Truth | 2025 | arXiv 2506.06991 | 无真值下评测被污染的众包 |
| Confirmation Bias: Challenge to Scalable Oversight | 2025 | arXiv 2507.19486 | 确认偏差对可扩展监督的挑战 |
| LLMs as Instruments for Latent Cognitive Variables | 2026 | arXiv 2604.02403 | 把 LLM 当潜认知变量的测量工具 |
| STABLEVAL: Disagreement-Aware Stable Evaluation | 2026 | arXiv 2605.02122 | 感知分歧的稳定评测 |

### 簇 6：样本高效评测（PPI / active testing）

**已确认结论**：这一簇给出「有限 gold 预算下如何做有效推断」的成熟工具箱，也给出它们的共同前提。[Active Testing](../sources/2103.05331-active-testing-sample-efficient-model-evaluation.md) 用非均匀采样提升标签效率，保持无偏的关键在于**样本必须从一个采样概率 $q(i_m)$ 已知的分布中随机抽取**，再用 LURE 型权重重加权——任何确定性的 top-$k$ 选取都会破坏无偏性。[Confidence-Driven Inference](../sources/2408.15204-confidence-driven-inference-llm-annotations.md) 把同一机制用在人工标注预算上：LLM 的自述置信度只用来决定**抽谁**（$\pi_i \propto \sqrt{\widehat{\text{err}}_i}$），从不决定答案，逆概率加权保证无论 LLM 有多偏都无偏。

这与簇 3 的 PA-DSL 结论合流成一条统一判据：**这一整套工具都需要「已知概率的 gold 层」**。回顾式流水线若没有这样一层，它们全部不可用——这正是为什么「留一层已知概率的随机抽检」在工程上如此关键。

两个反直觉的实证发现值得单独记下：

- **代理模型的「不同」比「更准」更值钱**（Active Testing）：随机森林代理在评测 ResNet-18 时胜过 ResNet 系代理；5×ResNet-18 集成胜过 1×ResNet-50。且主动学习的采集函数（互信息 / BALD）在评测场景反而**更差**——它们压制 aleatoric 不确定性，而评测恰恰需要它。
- **高一致率不等于下游推断有效**（Confidence-Driven Inference）：在 stance 任务上 LLM-人类一致性是三个设定中最高的（$\kappa = 0.57$），但纯 LLM 的 odds-ratio 估计方向**反了**、覆盖率 0%。这直接反驳「一致率够高就可以用 LLM 替代人工标注」。

| 论文 | 年份 | 会议/期刊 | 关键点 |
|---|---|---|---|
| [Active Testing: Sample-Efficient Model Evaluation](../sources/2103.05331-active-testing-sample-efficient-model-evaluation.md) | 2021 | arXiv 2103.05331 | 非均匀采样 + 重要性加权保无偏 |
| [Confidence-Driven Inference with LLM Annotations](../sources/2408.15204-confidence-driven-inference-llm-annotations.md) | 2024 | arXiv 2408.15204 | 置信度驱动分配人工标注预算 |
| Active Statistical Inference | 2024 | arXiv 2403.03208 | 主动采样下的有效推断 |
| PPI with Inverse Probability Weighting | 2025 | arXiv 2508.10149 | PPI + 倾向加权 |
| Active Multiple PPI | 2026 | arXiv 2605.08429 | 多目标主动 PPI |
| Prediction-Powered Active Testing | 2026 | arXiv 2607.08347 | PPI 与 active testing 结合 |
| Reliable Fairness Auditing with Semi-Supervised Inference | 2025 | arXiv 2505.12181 | 半监督推断做公平性审计 |
| Active Label Cleaning under Resource Constraints | 2021 | arXiv 2109.00574 | 预算约束下主动清洗标签 |
| End-to-End Label Noise Learning without Anchor Points | 2021 | arXiv 2102.02400 | 无 anchor point 的噪声学习 |
| Identifiability of Label Noise Transition Matrix | 2022 | arXiv 2202.02016 | 转移矩阵可识别性条件，界定单通道极限 |
| Identifiability of Noisy Label via Multinomial Mixture | 2023 | arXiv 2301.01405 | 多项混合下的噪声标签可识别性 |

### 簇 7：标签噪声基准与标注协议

**已确认结论**：做半合成验证时最大的陷阱是**自证循环**——把待验证的假设写进数据生成器。这一簇提供两类解药：一是**可控噪声注入基准**（CILN 强调噪声须 instance-dependent 才真实）；二是**provenance 免费真值**——GenVideo/DeMamba 的合成视频真伪标签全部来自 provenance，零人工标注，可无限生成且难度可控。后者意味着在合成内容检测这类任务上，「没有 gold label」有相当一部分是自我设定的限制。

| 论文 | 年份 | 会议/期刊 | 关键点 |
|---|---|---|---|
| CIFAR-N: Learning with Noisy Labels Revisited | 2021 | arXiv 2110.12088 | 真实人工标注噪声数据集 |
| Benchmarking Instance-Dependent Label Noise (CILN) | 2026 | arXiv 2606.14965 | 可控 instance-dependent 噪声注入 |
| AlleNoise: Real-World Label Noise in Text Classification | 2024 | arXiv 2407.10992 | 真实电商标签噪声数据集 |
| DeMamba / GenVideo | 2024 | arXiv 2405.19707 | 标签全来自 provenance，零人工标注 |
| Crowdsourced Detection of Audiovisual Deepfakes | 2026 | arXiv 2605.04797 | 众包检测的人类基线与难度谱 |
| DeeptraceReward | 2025 | arXiv 2509.22646 | 人类感知伪造点标注协议 |
| THumB 1.0: Transparent Human Evaluation for Captioning | 2021 | arXiv 2111.08940 | 分解式人工 caption 评测协议 |
| CapArena | 2025 | arXiv 2503.12329 | 详细 caption 评测 arena |
| DOCCI | 2024 | arXiv 2404.19753 | 高质量 caption 数据集 |
| ImageInWords | 2024 | arXiv 2405.02793 | 超详细图像描述标注 |
| Polos / Polaris | 2024 | arXiv 2402.18091 | 基于人类反馈的多模态 caption 指标 |

### 簇 8：盲验证与依赖验证（跨领域先例）

**已确认结论**：这一簇是第二批语料带来的最大发现——**「第二审核者该不该看到第一位的结论」这个问题，在法庭科学与医学影像筛查两个领域已有数十年的标准、成本研究与大样本实证**。这意味着「随机化质检可见性」不是一个需要从零论证的新设计，而是有跨领域先例可援引的成熟干预。

两个领域的术语值得记住：

- **法庭科学**：区分 *verification*（复核者知道第一位的结论）与 **blind verification**（复核者不知道结论、有时也不知道这是复核）。FBI、SWGFAST、OSAC 都有成文标准，且 Dror-Kukucka 一系工作把它归入 *cognitive bias* 治理。
- **医学影像**：把「非盲第二读者倾向于附和第一读者」称为 **alliterative bias / alliterative error**。[Cooper et al. (European Radiology 2021)](../sources/eurrad2021-cooper-blinding-second-reader-mammography.md) 在英国 CO-OPS 乳腺筛查数据（1,119,191 名女性、43 个中心）上量化了这一效应，并指出非盲会**绕过仲裁环节**、抵消双读的部分收益。

**这一簇最有建模价值的发现：锚定是单向的、条件于第一阶段为阳性。** Cooper 的数据里，当第一读者召回时，第二读者也召回的概率在非盲下为 74.7%、盲化下 69.8%（差 **4.9 个百分点**）；但当第一读者**不**召回时，两组几乎完全相同（2.33% vs 2.32%）。

这对 $P(Q \mid Y, A)$ 的参数化有直接含义：**锚定算子不是对称地把 $Q$ 拉向 $A$，而只在 $A$ 取某一特定值时起作用**。用一个单一的「锚定强度」标量参数去建模会误设这个结构。

| 论文 | 年份 | 领域/来源 | 关键点 |
|---|---|---|---|
| [Cooper et al., Blinding the Second Reader](../sources/eurrad2021-cooper-blinding-second-reader-mammography.md) | 2021 | European Radiology | 乳腺筛查双读盲化的大样本队列研究；alliterative bias |
| Cooper et al., Blinding Second Reader in Breast Screening | 2021 | European Radiology | 同团队相关研究 |
| [FBI, Verification and Blind Verification](../sources/fbi-frd501-verification-and-blind-verification.md) | — | FBI FRD-501 | verification 与 blind verification 的定义与适用条件 |
| SWGFAST, Standard for Application of Blind Verification | 2012 | SWGFAST | 指纹领域盲验证操作标准 |
| OSAC, Research Need: Blind Verification (FATM) | 2025 | OSAC | 盲验证的研究缺口清单 |
| OSAC, Research Need: ACE-V Bias (Friction Ridge) | 2021 | OSAC | ACE-V 流程中的偏差研究缺口 |
| Wortman, Blind Verification: Case Impact and Cost | 2018 | AAFS B95 | 盲验证的案件影响与成本核算 |
| Dependent versus Independent Verification | 1976 | ASA Proceedings | 依赖式与独立式复核的最早对照讨论之一 |
| Cognitive Aspects of Dependent Verification | 2012 | ASA Proceedings | 依赖式复核的认知机制 |
| Dror & Kukucka, Linear Sequential Unmasking–Expanded (LSU-E) | 2021 | Forensic Sci. Int.: Synergy | 信息暴露顺序的系统化控制框架 |
| Solving the Daubert Dilemma through Blind Testing | — | Houston Law Review | 盲测在法律证据可采性上的论证 |
| Dorfman & Valliant, Reanalysis of Ames/FBI Repeatability | 2022 | arXiv 2204 | 对指纹重复性研究的统计再分析 |
| van de Ven, Non-blinded Paired Screen-Positive | 2021 | Statistics in Medicine | 非盲配对筛查阳性设计的统计后果 |
| Ofqual, Review of Double Marking Research | 2014 | Ofqual | 考试双评（含盲评）的证据综述 |
| Dodd & Korn, BICR: Design Element or Unnecessary Expense? | 2008 | J. Clinical Oncology | 盲态独立中心复核（BICR）的必要性之辩 |
| Dello Russo et al., Local Investigators Overestimate ORR vs BICR | 2022 | Frontiers in Pharmacology | 本地研究者相对盲态中心复核系统性高估疗效 |

### 簇 9：锚定效应与评审者相互影响

**已确认结论**：锚定（anchoring / pre-annotation bias）是**方向与效应量都需要自测**的经验问题，不能援引一句「锚定使质检失效」就当定论。

关键是要区分**「偏倚存在」与「准确率下降」这两件不同的事**。[Mikulová et al. (LREC 2022)](../sources/lrec2022-mikulova-pre-annotation-bias.md) 的数据把这一点分离得很干净：预标注后对金标准的准确率**完全没有下降**（UAS 96.5 vs 96.5），速度提升约 1.7 倍；但标注者间一致性 $\kappa$ **上升了**（labeled $\kappa$ 0.99 vs 0.96），而作者自己明确说这个上升正是偏倚的表现——标注者的判断被同一个 parser 输出拉到了一起。

这解释了它与 Berzak et al. (EMNLP 2016) 的表面冲突：**两者其实都观察到了偏倚，只是 Berzak 关注一致率虚高、Mikulová 额外证明它没有转化为准确率损失**。对无 gold 场景的含义更严重——如果偏倚只表现为一致率虚高而不表现为准确率下降，那么在没有金标准的流水线里，这种偏倚**在观测上是不可见的**：你只会看到质检通过率很高、标注者很一致，而这恰恰是偏倚的症状而非质量的证据。

同类机制在多个领域被随机对照实验证实存在：同行评审的 herding（Stelmakh 等的 RCT）、在线评分的 social influence bias（Muchnik et al., *Science* 2013 的随机实验）、作文评分的 sequential assimilation effect。这些是「看到别人的判断会影响自己的判断」在不同场景下的同一现象，可作为效应量的量级参考。


| 论文 | 年份 | 会议/期刊 | 关键点 |
|---|---|---|---|
| [Mikulová et al., Pre-annotation Bias](../sources/lrec2022-mikulova-pre-annotation-bias.md) | 2022 | LREC | 准确率未降但 $\kappa$ 上升——偏倚存在却不可见 |
| Fort & Sagot, Influence of Pre-annotation on POS | 2010 | LAW | 预标注对词性标注的影响 |
| Dligach et al., Annotate More Accurately or More? | 2010 | LAW | 标注预算在「更准」与「更多」之间的取舍 |
| Rosbach et al., Stuck on Suggestions（病理） | 2026 | MELBA | AI 建议造成的锚定，医学影像标注场景 |
| Zhao, Sequential Effects in Essay Ratings（assimilation） | 2017 | Frontiers in Psychology | 作文评分的序列同化效应 |
| Muchnik et al., Social Influence Bias | 2013 | Science | 在线评分的随机对照实验，正向反馈自我强化 |
| Stelmakh et al., Herding in Peer Review (RCT) | 2020 | arXiv 2011 | 同行评审讨论中的从众效应 RCT |
| Stelmakh et al., Herding in Peer Review (RCT) | 2023 | PLOS ONE | 上述工作的期刊版 |
| Bokhove, Are Some School Inspectors More Lenient? | 2023 | — | 审核者宽严度异质性的实证 |

### 簇 10：抽检设计 / 验收抽样 / 序贯审计

**已确认结论**：「检验者本身也会出错」这件事在**工业质量控制与审计统计**里有半个世纪的方法学积累，比 ML 侧的讨论早得多。[Severn, Steiner & MacKay (2019)](../sources/meas2019-severn-steiner-mackay-targeted-verification-conditional-sampling.md) 的 targeted verification with conditional sampling 直接对应「按第一阶段结果决定抽检谁」这一设定，Collins (1974) 与 Khan & Duffuaa (2002) 处理带检验误差的单次/双次/重复检验方案，curtailed sampling 一系工作研究提前终止的抽样计划。

这条线的实践价值在于：它给出了**抽检方案的设计语言**（抽样计划、生产者/消费者风险、检验误差参数），而 ML 侧往往只把抽检当作一个既成事实去事后纠偏。

| 论文 | 年份 | 会议/期刊 | 关键点 |
|---|---|---|---|
| [Severn, Steiner & MacKay, Targeted Verification](../sources/meas2019-severn-steiner-mackay-targeted-verification-conditional-sampling.md) | 2019 | Measurement | 条件抽样下的目标核验 |
| Severn, Assessing Binary Measurement Systems（学位论文） | 2017 | U. Waterloo | 上述工作的完整论文版 |
| Danila, Steiner & MacKay, Routine Assessment of Binary Measurement System | — | BISRG RR-0901 | 二值测量系统的常规评估 |
| Collins, Attribute Acceptance Sampling with Inspection Errors | 1974 | Virginia Tech | 带检验误差的计数型验收抽样 |
| Khan & Duffuaa, Inspection Errors in Single/Double/Repeat Plans | 2002 | — | 单次/双次/重复检验方案的误差影响 |
| Shah & Phatak, MLE of Fraction Defective under Curtailed Sampling | 1974 | Technometrics | 截尾多次抽样下不良率的极大似然估计 |
| Kundu, Curtailed Sampling Plans: A Review | 2025 | StatsRef | 截尾抽样计划综述 |
| Barnard, El Emam & Zubrow, Capture-Recapture Reinspection Decision | 2002 | SEI | 用捕获-再捕获决定是否复检 |
| Kato & Nakagawa, Sequential Audit Sampling with Guarantees | 2026 | arXiv 2604.06116 | 带统计保证的序贯审计抽样 |
| SFGA: Statistics-First Gating for Adjudicative Escalation | 2026 | arXiv 2607.18960 | 以统计为先的裁定升级门控 |
| SPC in Decennial Census Industry/Occupation Coding | 1993 | ASA Proceedings | 人口普查编码的统计过程控制实践 |
| Fulcher et al., LQAS with Imperfect Diagnostic Tests（海地） | 2022 | BMC Public Health | 检验不完美时的批质量抽样验收 |
| Kazienga et al., Two-Stage LQAS with Imperfect Diagnostics | 2022 | PLOS NTD | 两阶段 LQAS 设计 |

### 簇 11：干预设计买识别力（因果实验设计）

**已确认结论**：「哪些干预能恢复识别力、最小成本的干预集合怎么选」在因果发现社区是一个成熟的形式化问题。Eberhardt et al. (UAI 2005) 给出 $N-1$ 个实验足以确定因果结构的经典结果，Akbari et al. (ICML 2022) 处理最小成本干预设计，Maringgele & Etesami (2026) 处理部分因果效应识别下的最优实验。

这条线为「随机化质检可见性」提供了理论外壳：它不是一个 ad-hoc 的工程 trick，而是**干预设计换识别力**这一范式在标注流水线上的实例。

| 论文 | 年份 | 会议 | 关键点 |
|---|---|---|---|
| Eberhardt, Glymour & Scheines, $N-1$ Experiments Suffice | 2005 | UAI | 确定因果结构所需实验数的上界 |
| Eberhardt, Almost Optimal Intervention Sets | 2008 | UAI | 因果发现的近优干预集 |
| Akbari et al., Minimum-Cost Intervention Design | 2022 | ICML | 因果效应识别的最小成本干预设计 |
| Maringgele & Etesami, Optimal Experiments for Partial Causal Identification | 2026 | arXiv 2605.06993 | 部分识别下的最优实验设计 |

### 簇 12：部分识别与自适应采集数据的推断

**已确认结论**：两条互补的线。**部分识别**：点识别拿不到时给出 sharp bounds——Manski & Molinari 的 COVID 感染率/抗体检测预测值系列是这一范式的教科书级示范（检验不完美 + 抽样非随机时能给出什么界）。**自适应采集数据的推断**：当数据的采集过程依赖已观测结果时，朴素估计有系统性偏差——Nie-Tian-Taylor-Zou 证明自适应采集会引入**负偏差**，Andrews-Kitagawa-McCloskey 的 "inference on winners" 处理「先挑赢家再报告其效应」的偏差。

后者对评测有直接含义：**「先按分数挑出最好的模型，再报告它的分数」本身就是有偏的**——这正是排行榜和模型选择的标准做法。

| 论文 | 年份 | 会议/期刊 | 关键点 |
|---|---|---|---|
| Manski & Molinari, COVID Infection Rate as Inference Problem | 2020 | NBER | 检验不完美 + 非随机抽样下的 sharp bounds |
| Manski, Bounding Predictive Values of Antibody Tests | 2020 | NBER w27226 | 抗体检验预测值的界 |
| Weak Supervision Performance Evaluation via Partial Identification | 2023 | arXiv 2312.04601 | 弱监督性能评估的部分识别 |
| Obradović, Imperfect Reference & Partial Identification | 2022 | arXiv 2204 | 参考标准不完美下的部分识别 |
| Gerard, Rokkanen & Rothe, Bounds under Manipulated Running Variable | 2016 | NBER | 断点回归被操纵时的界 |
| McCrary, Manipulation of the Running Variable（密度检验） | 2007 | NBER T0334 | 检测操纵的密度不连续检验 |
| Tian & Taylor, Selective Inference with Randomized Response | 2015 | arXiv 1507.06739 | 随机化响应下的选择性推断 |
| Zhang, Janson & Murphy, M-estimators with Adaptively Collected Data | 2021 | arXiv 2104.14074 | 自适应采集数据的 M 估计 |
| Nie, Tian, Taylor & Zou, Adaptively Collected Data Negative Bias | 2018 | AISTATS | 自适应采集引入负偏差 |
| Andrews, Kitagawa & McCloskey, Inference on Winners | 2019 | NBER w25456 | 「挑出赢家再报告」的偏差校正 |
| Cherian & Candès, Statistical Inference for Fairness Auditing | 2024 | JMLR | 公平性审计的多重检验推断 |
| [Lakkaraju et al., The Selective Labels Problem](../sources/kdd2017-lakkaraju-selective-labels-problem.md) | 2017 | KDD | 决策决定哪些结果可见；用决策者宽严度差异做识别 |

### 簇 2 / 簇 3 的第二批补充

第二批语料对前两簇的直接补强，其中三篇解决了第一批调研遗留的关键未知量：

| 论文 | 年份 | 归属簇 | 关键点 |
|---|---|---|---|
| Tai & Zhou, Simultaneous Adjustment（首页预览） | 2025 | 3 | 已核实其 Assumption 2 **要求** index test 与参考标准条件独立 |
| Sato & Miyazawa, Quality Estimation for Partially Subjective Classification | 2020 | 2 | 已确证含 review 阶段；reviewer 看 artifact 且被刻意盲化 |
| Hudak et al., Verification Bias with Conditional Reference Standards | 2026 | 3 | check-the-negatives 抽检 + 条件相关参数 |
| Ringham et al., Biased Sensitivity of Second Test in Series | 2010 | 3 | 串行第二检验的敏感度偏差 |
| Review of Methods for Diagnostic Tests in Sequence | 2024 | 3 | 序贯诊断检验方法综述 |
| Drivers of Bias in Expert Panel Reference Standards | 2025 | 3 | 专家小组作参考标准时的偏差来源 |
| [Pervasive Label Errors in Test Sets](../sources/2103.14749-pervasive-label-errors-test-sets.md) | 2021 | 2 | 十个常用测试集的标签错误率与排名翻转 |
| Breck et al., Data Validation for Machine Learning | 2019 | 2 | MLSys；工业级数据校验系统 |
| Xia et al., Unbiased Multi-label Crowdsourced Annotations | 2024 | 2 | ICML；多标签众包的无偏聚合 |
| Position: Responsible Application-Driven (RAD) AI Research | 2025 | 2 | arXiv 2505.04104；应用驱动研究的规范立场 |


## 相关页面

- [LLM-as-Judge](llm-as-judge.md) — judge 一致率基准与校准方法，簇 5 直接建立在其上
- [评测方法论基础](evaluation-methodology-foundations.md) — IRA/Kappa 与统计方法论基础
- [Rubric-Based 评测方法论](rubric-based-evaluation.md) — 标注 schema 设计（$K \geq 3$ 类别的过度识别约束与 rubric 粒度相关）
- [多模态大模型评测](multimodal-evaluation.md) — caption 与合成视频评测背景
- [评测感知与评测作弊](evaluation-awareness-and-gaming.md) — 盲化思路的另一应用面；bunching 检验与簇 12 的密度检验同源
- [HealthBench](../benchmarks/healthbench.md) — grader-医师一致性≈医师间一致性的对照数据点
