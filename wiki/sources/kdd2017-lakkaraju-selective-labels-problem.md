---
title: "The Selective Labels Problem: Evaluating Algorithmic Predictions in the Presence of Unobservables（选择性标注问题：存在不可观测变量时如何评测算法预测）"
type: source
created: 2026-08-12
updated: 2026-08-12
tags: [选择性标注, 不可观测变量, contraction, 人机对比评测, 反事实推断, 标注噪声]
sources: [kdd2017-lakkaraju-selective-labels-problem.pdf]
---

## 概述

本文形式化了 **selective labels（选择性标注）** 问题：在保释、医疗、保险等场景里，人类决策者的"是 / 否"决定同时决定了**哪些样本的结果标签能被观测到**——法官准予保释的被告才能观察到"是否按时到庭 / 是否再犯"，被羁押者的结果永远不可知。因此有标签的样本不是总体的随机样本，直接在这些样本上算 AUC / accuracy 并与人类决策对比是无效的。更棘手的是决策者还使用了算法看不到的 **unobservables**（不可观测的混淆变量，如被告在法庭上的举止），这使标准反事实推断的"无未测混淆"假设失效。作者提出 **contraction** 技术：利用**多个宽严不同的决策者**这一天然异质性，把"最宽松决策者放行的样本集"按算法风险分收缩到与某个更严格决策者相同的 acceptance rate，从而在无需 imputation、无需反事实推断的前提下比较两者的 failure rate。

**论文信息**: KDD 2017（Halifax, NS, Canada, 2017-08-13~17）, Himabindu Lakkaraju (Stanford)、Jon Kleinberg (Cornell)、Jure Leskovec (Stanford)、Jens Ludwig (University of Chicago)、Sendhil Mullainathan (Harvard), 2017, DOI 10.1145/3097983.3098066

> **资料版本说明**：`raw/papers/kdd2017-lakkaraju-selective-labels-problem.pdf` 最初下载错文件（内容为一篇日球层物理论文 arXiv:1702.00399），已于 2026-08-12 替换为 KDD 2017 正式版原文（Lakkaraju, Kleinberg, Leskovec, Ludwig, Mullainathan；DOI 10.1145/3097983.3098066）。本页内容基于正确版本。

## 关键发现

1. **问题的两个技术要件**：(1) 数据来自决策者的"是 / 否"判断，决策者依据特征 $(X, Z)$ 做决定，而算法只能看到 $X$，$Z$ 不可观测；(2) 决策者的判断决定了哪些样本有标签，形成 selective labels。两者叠加才构成本文要解的问题——单独的缺失数据或单独的不可观测混淆都有既有方法，组合起来则没有。
2. **朴素评测会得出"误导性"结论，且错在方向上**：论文给出的机制性例子是——若"被告家属出席庭审"这一特征对法官高度可预测但**未被记录在行政数据里**，那么在有标签数据上训练的算法会"错误但自信地"学到"年轻被告不犯罪"，部署后的错误率远高于预期。
3. **imputation / 反事实推断在此不可用，且会系统性高估模型**：合成实验中 propensity score matching 等 imputation 方法**严重低估**模型的 failure rate，以至于会得出"模型优于法官"的结论，而其真实表现实际上**比法官更差**（法官能看到 $X$ 与 $Z$，预测更准）。contraction 只轻微高估，且其 mean absolute error 在 $\beta_Z = 1$ 时比表现最好的 imputation 方法**小 6.4 倍**，随 $\beta_Z$ 增大差距进一步拉大。
4. **contraction 的核心思路**：取数据中 acceptance rate 最高（最宽松）的决策者 $q$，只在 $q$ 判过的样本上模拟黑盒模型；把 $q$ 放行的样本按模型风险分从高到低"收缩"掉一部分，使模型的 acceptance rate 降到与目标决策者 $j'$ 相同，再在剩余样本上算 failure rate。由于这些样本的结果标签都被 $q$ 观测到了，failure rate 可直接从数据算出，**完全避免对无标签样本做标签插补**。
5. **该方法必须依赖三个前提**（论文明确列出）：(1) **多决策者**——数据中有很多决策者而非一个；(2) **随机分配**——样本"如同被随机分配"给各决策者，使各决策者面对的案件池可比；(3) **宽严异质性**——不同决策者的判定阈值显著不同，从而 acceptance rate 有差异。若被模拟的样本池与被比较的决策者的样本池不同，failure rate 就无法公平对比。
6. **误差有可证明的上界，其大小由"分歧率"决定**：Proposition 4.1 给出误差界，界的紧致程度取决于最宽松决策者的 acceptance rate、模型与最宽松决策者在"拒绝"决定上的一致率、以及最宽松决策者判过的样本数。经验上（Figure 5）：最宽松决策者 acceptance rate 越低误差越大；一致率越高误差越小；最宽松决策者判过的样本越多误差越小。
7. **三个真实数据集上的结论一致**：保释、哮喘治疗、保险审批三个数据集上，contraction 曲线与"仅用有标签样本"曲线都显示模型的 failure rate 在所有 acceptance rate 上低于人类决策者，但"仅用有标签样本"的估计**过于乐观**；三个数据集上模型与最宽松决策者在"拒绝"决定上的一致率分别为 0.891 / 0.932 / 0.718，一致率高说明 contraction 的估计接近真值。
8. **随机分配假设是被检验过的，不是硬性设定**：作者用多重假设检验流程——先在 $t=1$ 的样本上拟合 M1 由 $x_i$ 预测 $y_i$ 并给全部评测样本打出 $\hat{y}$，再拟合 M2 仅用决策者 ID 预测 $\hat{y}$，做 F 检验并看统计量是否落入 95% 置信区间——在三个评测集上原假设（决策者与样本特征无关）均成立。

## 方法

**形式化**：数据集 $\mathcal{D} = \{(x_i, j_i, t_i, y_i)\}$ 含 $N$ 条观测。决策者 $j_i$ 可见 $x_i$ 与不可观测特征 $z_i$，做出 $t_i = 1$（是，如准予保释）或 $t_i = 0$（否）。结果标签

$$y_i = \begin{cases} 0 \text{ 或 } 1, & t_i = 1 \\ \text{not observed (NA)}, & \text{否则} \end{cases}$$

即只有被"放行"的样本才有标签（Figure 1）。$z_i$ 同时影响人类决策与结果，且与记录在数据中的特征独立。黑盒模型 $\mathcal{B}$ 把特征映射为风险分 $s \in [0,1]$。目标：给定观测数据 $\mathcal{D}$ 与模型 $\mathcal{B}$，在 selective labeling 与 unobservables 同时存在的条件下评测 $\mathcal{B}$ 并与人类决策对比。

**两个指标**：
- **failure rate**：决策所导致的不良结果数 / 该决策者判过的样本总数。论文例子：法官审 100 份保释申请，放行 70 人，其中 20 人犯罪，则 failure rate = 0.2。
- **acceptance rate**：被判为"是"（$t=1$）的样本数 / 该决策者判过的样本总数。上例中若法官放行 70%，acceptance rate = 0.7。

论文特别注明标注的**单边性**带来一个退化解：任何决策者都能靠把 acceptance rate 压到 0 来保证 failure rate 为 0，因此把 failure rate 降到 0 本身不是目标——必须看 **failure rate vs. acceptance rate 的权衡曲线**。把模型的曲线称为 *machine evaluation curve*，把 acceptance rate 相近的决策者分箱、视为单一假想决策者后画出的曲线称为 *human evaluation curve*，两条曲线的高低即人机对比结论。

**contraction 算法（Algorithm 1）**：输入观测数据 $\mathcal{D}$、模型风险分 $\mathcal{S}$、目标 acceptance rate $r$。
1. 取 $\mathcal{D}$ 中 acceptance rate 最高的决策者 $q$；$\mathcal{D}_q = \{(x,j,t,y) \in \mathcal{D} \mid j = q\}$。
2. $\mathcal{R}_q = \{(x,j,t,y) \in \mathcal{D}_q \mid t = 1\}$，即 $q$ 判过且有结果标签的样本。
3. 按模型置信分 $\mathcal{S}$ 降序排序 $\mathcal{R}_q$ 得 $\mathcal{R}_q^{sort}$（模型认为高风险的排在最前）。
4. 从 $\mathcal{R}_q^{sort}$ 顶部移除 $\lceil (1.0 - r)|\mathcal{D}_q| \rceil - [\,|\mathcal{D}_q| - |\mathcal{R}_q|\,]$ 条观测，剩下的记为 $\mathcal{R}_\mathcal{B}$（即模型判为 $t=1$ 的集合）。
5. 返回 $u = \frac{1}{|\mathcal{D}_q|}\sum_{l=1}^{|\mathcal{R}_\mathcal{B}|} \mathbb{1}(y_l = 0)$，即在 $\mathcal{R}_\mathcal{B}$ 上出现不良结果的比例（按 Algorithm 1 的记法 failure 对应 $y = 0$）。

直觉示例（论文两处举例）：法官 $j$ 与 $k$ 各审 1000 名被告，$j$ 的 acceptance rate 90%（放行 900 人），$k$ 为 80%（放行 800 人）；由要件 (2)，$\mathcal{D}_j$ 与 $\mathcal{D}_k$ 在分布上可视为随机划分。把模型作用在 $j$ 放行的 900 人上，用模型推翻其中 100 人的放行决定，得到"法官 $j$ 放行 900 人 + 模型再收缩到 800 人"的**人机混合机制**，其 800 人的 failure rate 就可与 $k$ 放行的 800 人直接比较。另一处例子：目标法官 $j'$ 放行 100 人中的 70 人（acceptance rate 0.7），最宽松决策者 $q$ 放行 90%，则约束模型必须羁押 $q$ 羁押的那 10 人（从而避开缺失标签），再让模型羁押它认为风险最高的另外 20 人，在剩余 70 人上算 failure rate。

**误差界（Proposition 4.1）**：在要件 (1)–(3) 满足时，对任意 $r \le \psi$（$\psi$ 为 $q$ 的 acceptance rate），contraction 给出的 failure rate 估计 $u$ 与真值之差不超过 $\frac{(1.0-a)|\mathcal{D}_q - \mathcal{R}_q|}{|\mathcal{D}_q|}$，其中 $a$ 是 $\mathcal{D}_q - \mathcal{R}_q$ 中模型 $\mathcal{B}$ 与 $q$ **都**判为拒绝（$t=0$）的比例。推导思路：误差主要来自 $\mathcal{B}$ 与 $q$ 在 $\mathcal{D}_q - \mathcal{R}_q$ 上的分歧，最坏情况下这些样本全部失败（上界），最好情况下全部不失败（下界）。

**合成数据实验**：$M = 100$ 名人类决策者，每人判 500 个样本，共 $N = 50\text{k}$ 观测。三个特征变量：$X$（人机都可见）、$Z$（仅人类可见的 unobservable）、$W$（人机都不可见但影响结果），均为独立零均值单位方差高斯。每个决策者的 acceptance rate 从 $[0.1, 0.9]$ 均匀采样并四舍五入到十分位。结果模型 $P(Y=0 \mid X,Z,W) = \frac{1}{1+\exp(-(\beta_X X + \beta_Z Z + \beta_W W))}$，系数分别为 1.0、1.0、0.2；决策模型 $P(T=0 \mid X,Z) = \frac{1}{1+\exp(-(\beta_X X + \beta_Z Z))} + \epsilon$，$\epsilon \sim N(0, 0.1)$，落在分配给该决策者的样本中风险最高的 $(1-r)\times 100\%$ 者取 $t=0$。50k 随机切成两份 25k，一份训练 logistic regression，另一份作评测集。对比四类估计：**True Evaluation**（用全部结果标签算真值，仅合成数据可得）、**Contraction**、**Labeled Outcomes Only**（只用有标签样本，按风险分排序取前 $r$ 比例为放行）、**Imputation**（gradient boosted trees、logistic regression、nearest neighbor matching、propensity score matching、doubly robust estimation）。

**真实数据实验**：三个数据集，均只用有结果标签的样本训练模型，模型对比过 GBT、随机森林、决策树、logistic regression、SVM，正文只报告 GBT（100 棵树）。

## 重要数据

三个真实数据集（第 5.2 节）：

| 数据集 | 全量规模 | 决策者 | 决策内容 | failure 定义 | 评测子集 |
|---|---|---|---|---|---|
| 保释（bail） | 约 9k 名被告，来自一家匿名地区法院 | 18 名法官 | 准予 / 拒绝保释 | 获保释者在保期间犯罪 | 4.8k 名被告，9 名法官 |
| 医疗（哮喘 / 咳喘治疗） | 60k 名咳嗽 / 喘息患者，来自一家在线电子病历公司 | — | 轻度治疗（速效药）vs 强化治疗（长期控制药） | 治疗推荐后 15 天内症状复发 | 28k 名成年患者，39 名医生 |
| 保险 | 约 50k 份保险申请（承保大型企业的保险机构） | 74 名管理者 | 批准 / 拒绝投保申请 | 获批申请给保险方带来亏损 | 38k 份申请，41 名化工领域管理者 |

关键量化结果：

| 指标 | 数值 |
|---|---|
| contraction 相对最佳 imputation 方法的 MAE 优势（$\beta_Z = 1$） | 小 6.4 倍（且随 $\beta_Z$ 增大而扩大） |
| 模型与最宽松决策者在"拒绝"决定上的一致率 | 保释 0.891 / 哮喘治疗 0.932 / 保险 0.718 |
| 合成实验规模 | $M=100$ 决策者 × 每人 500 样本 = $N=50\text{k}$；训练 / 评测各 25k |
| 合成实验系数 | $\beta_X = 1.0$、$\beta_Z = 1.0$、$\beta_W = 0.2$；决策噪声 $\epsilon \sim N(0,0.1)$ |
| 决策者 acceptance rate 采样范围 | $[0.1, 0.9]$ 均匀采样 |
| failure rate 误差上界 | $\dfrac{(1.0-a)\,|\mathcal{D}_q - \mathcal{R}_q|}{|\mathcal{D}_q|}$ |

三条误差趋势（Figure 5）：最宽松决策者 acceptance rate 下降 → 误差上升；模型与最宽松决策者一致率上升 → 误差下降；最宽松决策者判过的样本数上升 → 误差下降。

## 局限与假设

- **随机分配假设是硬前提**：样本必须"如同随机分配"给决策者，否则最宽松决策者的样本池与被比较决策者的样本池不可比，failure rate 对比失去意义。论文用 F 检验验证了这一点在其三个评测集上成立，但这是需要逐场景检验的经验性质，不是普遍成立的。
- **必须存在宽严异质性且最宽松者足够宽松**：contraction 只能估计 $r \le \psi$（最宽松决策者的 acceptance rate）范围内的曲线；$\psi$ 越低可覆盖范围越窄、误差界越松。若所有决策者宽严一致，方法不可用。
- **估计质量取决于模型与最宽松决策者的分歧**：若最宽松决策者拒绝的样本恰好也是模型排名最高风险的，估计等于真值；若完全不重合，估计会偏离真值。这是"依赖一致率"而非"无条件无偏"。
- **随机化实验不可行是该问题的出发点**：作者明确说明，正因为放行嫌疑人 / 撤除治疗这类决策后果严重，为增加评测数据变异性而随机化决策**不切实际且往往不符伦理**，因此不能用随机对照来绕开选择性标注。
- **不能在真实数据上验证真值**：真实数据集上无法计算 true evaluation curve（论文脚注明确指出），因此真实数据部分只能做方法间对比（contraction vs labeled outcomes only），不能给出绝对误差。
- **只处理"单边标注"结构**：标签只在 $t=1$ 一侧被观测；论文的度量与算法都建立在这一结构上。
- **不可观测变量的存在被建模但未被识别**：contraction 绕过了 $Z$ 而不是估计 $Z$，因此能做的是"人机 failure rate 曲线对比"，而不是恢复个体反事实结果。
- **面向评测而非训练**：论文在结论中把"如何用 contraction 改进在选择性标注数据上的模型训练"列为未来工作。

## 相关页面

- [标注噪声与标注流水线质量](../concepts/annotation-noise-and-pipeline-quality.md)
- [评测方法论基础](../concepts/evaluation-methodology-foundations.md)
- [Pervasive Label Errors in Test Sets](./2103.14749-pervasive-label-errors-test-sets.md)
- [FBI FRD-501：核验与盲核验](./fbi-frd501-verification-and-blind-verification.md)
