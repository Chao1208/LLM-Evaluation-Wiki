---
title: "Horowitz & Manski 1992：数据有误差时的识别与稳健性"
type: source
created: 2026-08-22
updated: 2026-08-22
tags: [部分识别, 污染抽样, 腐败抽样, 稳健估计, 锐界, 识别崩溃点, 计量经济学]
sources: [horowitz-manski-1992-identification-and-robustness-in-the-presence-of-errors-in-data.pdf]
---

# Horowitz & Manski 1992：数据有误差时的识别与稳健性

## 基本信息

| 项 | 值 |
|---|---|
| 标题 | Identification and Robustness in the Presence of Errors in Data |
| 作者 | Joel L. Horowitz、Charles F. Manski |
| 形式 | University of Wisconsin **SSRI 工作论文**（扫描件 + OCR，36 页 PDF，印刷 33 页） |
| 正式发表版 | **Identification and Robustness with Contaminated and Corrupted Data**，*Econometrica* 63(2):281–302, 1995-03，DOI `10.2307/2951627`（题录经 Crossref 确证；全文未获，命题编号与工作论文的对应关系**未核**） |
| 参考文献 | 仅 6 条：Donoho & Huber 1983、Hampel et al. 1986、Huber 1964、Huber 1981、Maddala 1983、U.S. Census 1991 |
| 页码换算 | **印刷页码 = PDF 页序 − 2**（PDF 第 3 页 = 印刷第 1 页） |

## 问题设定

存在随机三元组 `(y₀, y₁, z) ∈ Y×Y×{0,1}`，分布为 P。**只能观测到** `y = y₀(1−z) + y₁z`，且 **z 永不可观测**。关心的是 y₁ 的边缘分布 P₁，或某个泛函 `τ(P₁)`。

记号（印刷第 4 页）：

- `Q` = 可观测 y 的分布（**被数据识别**）
- `P_ij = P_i(y_i | z = j)`，`p = P(z = 0)` = 数据出错的边缘概率
- 分解式：`P₁ = (1−p)P₁₁ + pP₁₀`，`Q = (1−p)P₁₁ + pP₀₀`

**核心对照（印刷第 4 页）**：稳健估计把未知的 P₁ 固定、让 Q 遍历所有可能（**事前 ex ante**）；识别分析把 Q 固定（因为它被数据识别）、让 P₁ 遍历所有与分解式相容的分布（**事后 ex post**）。

## 两个抽样模型

| 模型 | 定义 | 对应的稳健估计文献 |
|---|---|---|
| **contaminated sampling（污染抽样）** | 数据出错与总体实现**独立**，即 `P₁ = P₁₁`。结论用 P₁₁ 与 τ(P₁₁) 表述 | bounded-influence estimation、influence function |
| **corrupted sampling（腐败抽样）** | 不假设独立，允许**任意选中的一部分数据被任意腐败**。结论用 P₁ 与 τ(P₁) 表述 | high-breakdown estimation；Hampel et al. 1986 的有限样本 breakdown point、Donoho-Huber 1983 的 ε-replacement 定义都是它的有限样本版本 |

另一处点明：corrupted sampling 也出现在"无样本分离的转换回归（switching regressions without sample separation）"文献里（Maddala 1983）。

**唯一的先验信息假设**：已知一个上界 λ 使 `p ≤ λ < 1`（印刷第 6 页）。作者论证稳健估计其实总隐含地假设了这样一个上界——影响函数只在误差概率"接近零"时有意义，breakdown point 只在知道 p 落在它上方还是下方时有意义。

## 主要结果

### 命题 1（印刷第 7 页）——抽象的识别集

```
(4)  P₁₁ ∈ Ψ₁₁(p) = Ψ ∩ {(Q − pψ₀₀)/(1 − p) : ψ₀₀ ∈ Ψ}
(5)  P₁  ∈ Ψ₁(p)  = {(1 − p)ψ₁₁ + pψ₁₀ : (ψ₁₁, ψ₁₀) ∈ Ψ₁₁(p) × Ψ}
```

- **在没有进一步信息的前提下**，这些限制是 tight 的。⚠️ 注意 `ψ₁₀` 取遍**全部**分布空间 Ψ，即腐败方向完全不受约束——tightness 的成立依赖这一点。
- (B) `Ψ₁₁(p) ⊂ Ψ₁(p)`：污染抽样的界严格更紧（推论 1.1）。
- (C) 识别集随 p 单调扩张。
- (D) 只知 `p ≤ λ` 时 `P₁₁ ∈ Ψ₁₁(λ)`、`P₁ ∈ Ψ₁(λ)`，且 tight。
- 附带：`P₀₀ ∈ Ψ₀₀(λ) = Ψ₁₁(1−λ)`，`P₀ ∈ Ψ₀(λ) = Ψ₁(1−λ)`。

### 命题 2（印刷第 9 页）——识别崩溃点 = 稳健 breakdown point 的极限形式

Y 是有限维实空间且泛函满足一个连续性条件时，`λ₁ = lim_{n→∞} λ₁ₙ`（HRRS 有限样本 breakdown point）几乎必然成立。

**关键差异**：识别崩溃点在**观测分布 Q** 上求值，稳健 breakdown point 在**未知的目标分布 P₁₁** 上求值——这正是事后 vs 事前之别。

### 推论 1.2 与式 (12)（印刷第 10 页）——概率的 tight 界与四个 regime

```
(10) P₁₁(A) ∈ Ψ₁₁(A;λ) = [0,1] ∩ [(Q(A) − λ)/(1 − λ), Q(A)/(1 − λ)]
(11) P₁(A)  ∈ Ψ₁(A;λ)  = [0,1] ∩ [Q(A) − λ, Q(A) + λ]
```
两者都逐字标注 tight。等价的分段表示：

```
(12a)  1 − λ ≤ Q(A) ≤ λ        ⇒  Ψ₁₁(A;λ) = Ψ₁(A;λ) = [0,1]
(12b)  Q(A) ≤ min(1 − λ, λ)    ⇒  Ψ₁₁(A;λ) = [0, Q(A)/(1 − λ)]
                                  Ψ₁(A;λ)  = [0, Q(A) + λ]
(12c)  Q(A) ≥ max(1 − λ, λ)    ⇒  Ψ₁₁(A;λ) = [(Q(A) − λ)/(1 − λ), 1]
                                  Ψ₁(A;λ)  = [Q(A) − λ, 1]
(12d)  λ ≤ Q(A) ≤ 1 − λ        ⇒  Ψ₁₁(A;λ) = [(Q(A) − λ)/(1 − λ), Q(A)/(1 − λ)]
                                  Ψ₁(A;λ)  = [Q(A) − λ, Q(A) + λ]
```

**(12b)/(12c) 是单边失效的形状**：一侧端点被钉死在 0（或 1），另一侧仍有信息。

### ⚠️ "trivial" 与 "identification breakdown" 是两个不同门槛（印刷第 11 页）

原文一句话同时给出两者，容易混读：

> It can be seen from (12) that the bounds on P₁₁(A) and P₁(A) are **trivial if 1 − λ ≤ Q(A) ≤ λ** but are informative otherwise. Moreover, the **identification breakdown point** of both P₁₁(A) and P₁(A) is **min[Q(A), 1 − Q(A)]**.

| 概念 | 几何含义 | 条件 |
|---|---|---|
| **trivial** | 识别区间退化成整个 [0,1]，**两个**端点都触界 | `Q ≤ λ` **且** `Q ≥ 1−λ` ⟺ `1−λ ≤ Q ≤ λ` ⟺ `λ ≥ max(Q, 1−Q)`（故要求 λ ≥ 1/2） |
| **identification breakdown** | **至少一个**端点被推到值域边界 | `Q ≤ λ` **或** `Q ≥ 1−λ` ⟺ `λ ≥ min(Q, 1−Q)` |

差别来自 breakdown 的定义（印刷第 8 页）："the largest fraction of erroneous data that τ(·) can tolerate without being driven to **either** boundary of its range"——*either* 即单边触界就算崩溃。

### 观测分布 Q 永在识别集内（印刷第 11 页）

`Q(A) ∈ Ψ₁₁(A;λ) ⊂ Ψ₁(A;λ)` 对一切 A 成立 ⇒ `Q ∈ Ψ₁₁(λ) ∩ Ψ₁(λ)`。推论：

> This means that if the only available information is a bound on p, **one cannot reject the hypothesis that P₁ = P₁₁ = Q**.

同时 (10) 蕴含 P₁₁ 被 Q 支配（Q 零测集也是 P₁₁ 零测集），(11) 蕴含 `sup_A |P₁(A) − Q(A)| ≤ λ`（式 13）。

### ⚠️ 逐坐标锐区间的乘积（"箱"）**不是**识别集（印刷第 11 页）

> Every distribution ψ₁₁ ∈ Ψ₁₁(λ) satisfies ψ₁₁(A) ∈ Ψ₁₁(A;λ), A ∈ Ω. **However, not every function φ: Ω → ℝ¹ satisfying φ(A) ∈ Ψ₁₁(A;λ), A ∈ Ω, is a probability distribution.**

即：把每个可测集 A 上的锐区间乘起来得到的"箱"是识别集的**外界（outer region）**，不是锐识别集。这一区分在部分识别文献里已被定名——见 Molinari, *Microeconometrics with partial identification*（arXiv `2004.11751`，PDF 在 `raw/papers/`，**尚无 wiki 页**）的定理 `OR-2.1`，并归功 Manski (1994, p. 149 及注 2)。

## Y 为实直线时的加强结果（§3）

### 命题 3（印刷第 12–13 页）——分位数的 tight 界

令 `r(γ)` = Q 的 γ-分位数（γ ≤ 0 取 −∞，γ > 1 取 ∞）：

```
(16) q₁₁(α) ∈ [r(α(1 − λ)), r(α(1 − λ) + λ)]
(17) q₁(α)  ∈ [r(α − λ), r(α + λ)]
```

- `q₁₁(α)` 的界**上下都始终有信息**（对一切 α ∈ (0,1)、一切 Q、一切 λ < 1）⇒ 其识别崩溃点**恒为 1**。
- `q₁(α)`：**下界在 λ < α 时有信息，上界在 λ < 1 − α 时有信息** ⇒ 识别崩溃点 = `min(α, 1−α)`。

这是"单边界失效"的显式刻画：λ 越过 α 时下界失效、越过 1−α 时上界失效。

### 命题 4（印刷第 13–14 页）——凡尊重随机占优的泛函都有 tight 界

定义（F 随机占优 G ⟺ `F[−∞,t] ≤ G[−∞,t]` ∀t；τ 尊重随机占优 ⟺ F 占优 G 时 `τ(F) ≥ τ(G)`）。构造两个极端分布：

```
L_λ[−∞,t] = Q[−∞,t]/(1 − λ)          if t < r(1 − λ)
          = 1                         if t ≥ r(1 − λ)

U_λ[−∞,t] = 0                         if t < r(λ)
          = (Q[−∞,t] − λ)/(1 − λ)     if t ≥ r(λ)
```
则
```
(18) τ(P₁₁) ∈ [τ(L_λ), τ(U_λ)]
(19) τ(P₁)  ∈ [τ{(1 − λ)L_λ + λδ₋∞}, τ{(1 − λ)U_λ + λδ∞}]
```
两者都 tight。`δ₋∞` / `δ∞` 是把全部质量放在 −∞ / ∞ 的点质量。

**⇒ corrupted sampling 的锐界由「腐败部分退化成放在最极端一点的点质量」实现。**

**为什么箱在这套坐标上恰好锐**（证明骨架，印刷第 30–31 页）：`L_λ` 被 `Ψ₁₁(λ)` 的每个成员随机占优，`U_λ` 随机占优 `Ψ₁₁(λ)` 的每个成员。也就是说，在"CDF 值"这套**全序**坐标上，箱的两个角函数 `min{Q[−∞,t]/(1−λ), 1}` 与 `max{(Q[−∞,t]−λ)/(1−λ), 0}` **本身就是合法 CDF**，因而角点可达。**条件是"坐标族被全序排好 + 泛函对该序单调"，不是系数符号条件。**

### 推论 4.1（印刷第 14 页）——**线性泛函**的 tight 界

设 `g: Y → ℝ` 有界且**递增**，`K₀ = lim_{t→−∞} g(t)`、`K₁ = lim_{t→∞} g(t)`，`τ(ψ) = ∫g dψ`。则

```
(20) τ(P₁₁) ∈ [∫g dL_λ, ∫g dU_λ]
(21) τ(P₁)  ∈ [(1 − λ)∫g dL_λ + λK₀, (1 − λ)∫g dU_λ + λK₁]
```
两者 tight。附注：若 `∫g dL_λ`、`∫g dU_λ` 固定，g 的值域 `[K₀,K₁]` 不影响 (20) ⇒ **(20) 即使 g 无界也 tight**；(21) 才需要 K₀/K₁ 有限。取 `g(y) = y` 得污染抽样下均值的有限界（式 22）。

**⇒ "分布上的一个线性（仿射）泛函在污染／腐败识别集上的锐界"，1992 年这篇已给出闭式，条件是被积函数单调。**

### 均值悖论的化解（印刷第 15 页）

均值在污染抽样下"不稳健"是众所周知的，但式 (22) 给出有限界，两者不矛盾：

> Identification analysis gives the range of possible values of the mean of P₁₁ subject to the information on Q that is revealed by the sampling process. … **In contrast, robust estimation supposes that Q is not yet known and, holding P₁₁ fixed, obtains the feasible values of the mean of Q for Q ∈ {(1 − p)P₁₁ + pψ : ψ ∈ Ψ, p ≤ λ}. The range of possible values of the mean is unbounded under this setup.**

## 无穷小识别分析（§4）——识别版的 gross-error sensitivity

改写识别集为 `Ψ₁₁(λ) = {Q − [λ/(1−λ)](ψ − Q) : ψ ∈ Ψ₀₀(λ)}`（式 23）、`Ψ₁(λ) = {Q − λ(ψ − ω) : ψ ∈ Ψ₀₀(λ), ω ∈ Ψ}`（式 24），并定义方向导数 `τ′(Q,ψ,ω)`（式 25）。

**命题 5（印刷第 16–17 页）**：在可微性条件下，
```
(27) T₁₁(λ) = {τ(Q) + λτ′(Q,ψ,Q) + o(λ;Q) : ψ ∈ Ψ₀₀(λ)}
(28) τ(Q) + λ·inf_{ψ∈Ψ₀₀(λ)} τ′ + o(λ) ≤ τ(P₁₁) ≤ τ(Q) + λ·sup_{ψ∈Ψ₀₀(λ)} τ′ + o(λ)
(30)(31) 同理给 T₁(λ) 与 τ(P₁)，sup/inf 同时对 ψ ∈ Ψ₀₀(λ)、ω ∈ Ψ 取
```
正文称 (28)(31) 为 "Tight bounds"。

**推论 5.1（印刷第 17–18 页）**：若 `τ′(Q,ψ,ω) = ∫f_Q(y) d(ψ − ω)` 是有界线性泛函，且不失一般性取 `∫f_Q dQ = 0`，令 `B_U = sup_y f_Q(y)`、`B_L = inf_y f_Q(y)`、`B_U* = sup f_Q − inf f_Q`、`B_L* = −B_U*`，则
```
(36) τ(Q) + λB_L + o(λ) ≤ τ(P₁₁) ≤ τ(Q) + λB_U + o(λ)
(39) τ(Q) + λB_L* + o(λ) ≤ τ(P₁) ≤ τ(Q) + λB_U* + o(λ)
```
⚠️ 印刷第 18 页式 (39) 左端印的是 `τ(P₁₁)`，但上文写 "Bounds on τ(P₁) are" ⇒ **原文印刷笔误**，应为 `τ(P₁)`。

⚠️ **推论 5.1 的正文没有写 tight**，而其证明（印刷第 33 页）说 "Ψ₀₀(λ) is a subset of Ψ. Hence the bounds in (28) and (31) **lie within** the bounds that result when Ψ **replaces** Ψ₀₀(λ)" ⇒ (36)(39) 是把 ψ 的取值域放宽后算出的，**一般应视为外界而非锐界**（此判读基于该页 OCR 文本，未看渲染图像）。

**与 gross-error sensitivity 的关系（印刷第 18–19 页）**：稳健估计里 `max[|inf_y f_{P₁₁}(y)|, sup_y f_{P₁₁}(y)]` 叫 τ 在 P₁₁ 处的 gross-error sensitivity（式 40）。对照 (34)(35) 可见 `max(|B_L|, B_U)` 也是一种 gross-error sensitivity，**区别在于导数是在 Q 处沿 −(ψ−Q) 方向求，而不是在 P₁₁ 处沿 (ψ−P₁₁) 方向求**。

## 估计与实证（§5）

所有界都是 Q 的泛函 ⇒ 用经验分布 `Qₙ` 代入即得一致估计（式 41、42），"这些估计在没有关于 Q 的先验信息时是最好可能的"。置信区间问题本文不处理。

实证：1990 年 3 月 CPS 的美国家庭收入分布。4.5% 家庭未受访、受访家庭中 8% 的人项目无应答 ⇒ `p ≤ 12.1%`。以 21 个收入区间的 `Qₙ` 加"区间内均匀"辅助假设：

| 目标量 | 点值/界 |
|---|---|
| `Qₙ[0,30)` | 0.515 |
| `P₁₁[0,30)` 界 | [0.448, 0.586] |
| `P₁[0,30)` 界 | [0.394, 0.636] |
| `q₁₁(.5)`（中位数，千美元） | [25.482, 33.026] |
| `q₁(.5)` | [21.954, 37.273] |

关于对腐败方向的先验信息，只有一处（印刷第 21 页）：

> The Bureau of the Census imputation practice is **valid if P₁₀ = P₀₀**, implying that P₁ = Q. **Our concern, however, is with the worst-case situation in which one has no prior information about the relation between P₀₀ and P₁₀.**

即：他们讨论过的那种先验信息是 `P₁₀ = P₀₀`（导致点识别），**没有**讨论"腐败方向已知"这类形状先验。全文正文零次出现 `sign`、`one-sided`、`known direction`（`grep -ic` 实测）。

## 讨论（§6，印刷第 22–23 页）——对稳健估计"只做点估计"的质疑

- 稳健估计所关心的参数在其自身假设下**本来就不被识别**，因此"集中于点估计"缺乏理由；本文表明这些参数可以被界住、界也容易估。
- 稳健估计视角"比必要的更保守"：它在收集数据**之前**看问题、防最坏情形；而有些事前可能的情形在数据到手后**可以被排除**。
- 关于 plug-in 估计量 `τ(Qₙ)`：

> In robust estimation, the functional τ(·) is weakly continuous on Ψ, and the estimator of τ(P₁) typically is τ(Qₙ). In this situation, plim τ(Qₙ) = τ(Q). We observed in Section 2 that **Q is in the space Ψ₁(λ) of feasible values for P₁**. Therefore, **τ(Q) is in the space of feasible values for τ(P₁)**. … Moreover, given an upper bound on p, **estimation of τ(Q) yields no information on τ(P₁) beyond that contained in our tight bounds.**

- 对"报告 `τ(Qₙ)` 就不必表态 p 的大小"这一辩护，作者不接受：没有 p 的上界就无法评估 `|τ(Q) − τ(P₁)|` 这个渐近偏差、甚至无法确定它是否有限。

## 后续文献（把这条线接到验证子样本）

以下题录经 Crossref / OpenAlex 确证，但**全文均未获**（付费墙，Unpaywall `is_oa: false`）：

| 文献 | 出处 | 与本文的关系 |
|---|---|---|
| Dominitz & Sherman, "Sharp bounds under contaminated or corrupted sampling **with verification**, with an application to environmental pollutant data" | *JABES* 9(3):319–338, 2004；DOI `10.1198/108571104x3389` | 在污染／腐败抽样上加入验证信息求锐界 |
| Dominitz & Sherman, "Identification and estimation of bounds on school performance measures: a nonparametric analysis of a **mixture model with verification**" | *J. of Applied Econometrics* 21:1295–1326, 2006；DOI `10.1002/jae.912` | 摘要（经 Crossref 确证）：数据可划分为两集，其一（verified set）更可能来自目标分布；用**验证信息 + 单调性约束**界住学校表现的"分歧区间" |
| Kreider & Pepper, "Disability and Employment: Reevaluating the Evidence in Light of Reporting Errors" | *JASA* 102(478):432–441, 2007 | 同属"HM + （部分）验证"支线 |
| Kreider & Pepper, "Inferring disability status from corrupt data" | *J. of Applied Econometrics* 23(3):329–349, 2008 | 同上 |
| Molinari, "Partial identification of probability distributions with misclassified data" | *J. of Econometrics* 144(1):81–117, 2008；DOI `10.1016/j.jeconom.2007.12.003` | 误分类的量与型的多种假设下的分布锐界 |

Molinari, *Microeconometrics with partial identification*（arXiv `2004.11751`，PDF 在 `raw/papers/`，尚无 wiki 页）对这条支线的逐字概述：

> Dominitz and Sherman (2004, 2005) and Kreider and Pepper (2007, 2008) extend the results of Horowitz and Manski to allow for (partial) verification of the distribution from which the data are drawn. They apply the resulting sharp bounds to learn about school performance when the observed test scores may not be valid for all students.

## 阅读注记

- PDF 是**扫描件 + OCR**（Creator: HP Smart Document Scan Software 3.80；Producer: Adobe Paper Capture Plug-in）。**公式层不可靠**：λ 常被识别成 `l`，式 (12a) 整行在 `pdftotext` 输出中丢失。**读公式必须用 `pdftoppm -r 150 -png` 渲染成图像后看**。
- 本页的全部逐字引文来自 PDF 第 9、12–25 页的渲染图像（印刷第 7、10–23 页），附录（印刷 24–33 页）只读了 OCR 文本。

## 相关页面

- [Partial Identification from LLM Prompts](2606.15031-partial-identification-from-llm-prompts.md)
- [图不完整下的排序部分识别（Crippa & Fedchenko）](2410.18272-partially-identified-rankings-from-pairwise-interactions.md)
- [标注噪声与标注流水线质量](../concepts/annotation-noise-and-pipeline-quality.md)
