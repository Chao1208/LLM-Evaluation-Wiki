---
title: "混合比例的单侧可识别性：从 purity 到 mutual irreducibility"
type: concept
created: 2026-08-22
updated: 2026-08-22
tags: [混合模型, 可识别性, 部分识别, 标签噪声, PU-learning, 多重检验, mixture-proportion-estimation]
sources: [genovese-2004-a-stochastic-process-approach-to-false-discovery-control.pdf, blanchard-2010-semi-supervised-novelty-detection.pdf, 1303.1208-classification-with-asymmetric-label-noise-consistency-and-maximal-denoising.pdf, 2306.01253-mixture-proportion-estimation-beyond-irreducibility.pdf, 1811.04820-learning-from-positive-and-unlabeled-data-a-survey.pdf, 1710.01167-decontamination-of-mutual-contamination-models.pdf]
---

# 混合比例的单侧可识别性：从 purity 到 mutual irreducibility

## 问题

观测到一个混合分布 `F = (1 − κ)G + κH`，其中 `H` 已知（或可采样）、`G` 完全未知、`κ ∈ [0, 1]` 无上界约束。
只从 `F` 与 `H` 出发，`κ` 能被定到什么程度？

答案在四个互不常引的学科里被分别写下过，而结论一致：**`κ` 只有一侧可被识别**——可行值构成一个闭区间，
上端点有闭式且可达，下端点（「完全没有污染」）永远不能被数据否证。

## 核心量：maximal proportion

给定 `F`、`H`，定义

```
κ*(F | H) := max{ α ∈ [0, 1] : ∃ 概率分布 G′ 使 F = (1 − α)G′ + αH }
```

即「`H` 在 `F` 中的最大可能占比」。它有密度形式的闭式：

```
κ*(F | H) = inf_{S: H(S)>0} F(S)/H(S) = ess inf_{x ∈ supp(H)} f(x)/h(x)
```

（来源：`raw/papers/1303.1208-classification-with-asymmetric-label-noise-consistency-and-maximal-denoising.pdf`
印刷页 11–13；其中 Proposition 8 由该文归功于 Blanchard-Lee-Scott 2010。⚠️ 该 PDF 尚无对应的 `wiki/sources/` 摘要页。）

**识别集的形状**：若 `F = (1 − κ)G + κH` 成立，则对任意 `δ ∈ [0, κ)`，
`F = (1 − κ + δ)G′ + (κ − δ)H`（其中 `G′ = (1 − κ + δ)^{-1}[(1 − κ)G + δH]`）同样成立。
⇒ 可行集恰为 `[0, κ*(F | H)]`，两端点均可达。

## 恢复点识别的条件族（按学科分列）

| 学科 | 条件的名字 | 形式 | 一手出处 |
|---|---|---|---|
| 多重检验 / FDR | **purity** | `ess inf_t f(t) = 0`（`f` 为备择下 p 值密度） | Genovese & Wasserman，*Annals of Statistics* 32(3):1035–1061, 2004，§3.1（印刷页 1042–1043） |
| 半监督新奇检测 | **proper novelty distribution** | 不存在 `P₁ = (1 − γ)Q + γP₀`（`0 < γ ≤ 1`） | Blanchard, Lee & Scott，*JMLR* 11:2973–3009, 2010，Definition 4（印刷页 2983） |
| 非对称标签噪声 | **irreducible / mutually irreducible** | `κ*(G \| H) = 0`，双向则称 mutually | Scott, Blanchard & Handy，COLT 2013（JMLR W&CP v30:489–511）／arXiv:1303.1208，Definition 4（印刷页 11） |
| PU learning | **anchor set / 非重叠支撑** | 存在 `H` 占满的区域 | 综述见 Bekker & Davis，arXiv:1811.04820 |
| 多混合分布 | **jointly irreducible** | mutual irreducibility 对 `L` 个基分布的推广 | Katz-Samuels, Blanchard & Scott，arXiv:1710.01167，Definition 3 |

这些条件彼此等价或互为推广：Blanchard-Lee-Scott 2010 印刷页 2989 明写
「our notion of proper novelty distribution recovers their notion of **purity**」（指 Genovese-Wasserman 2004 §3）；
Scott 等 2013 的 irreducibility 又是 properness 的重述。

**不加这些条件时**能得到什么，三处一手表述：

- Genovese & Wasserman 2004（印刷页 1043）：`0 ≤ a₀ ≤ a̲ ≤ a ≤ 1`，「an identifiable lower bound on `a` is `a₀`」；
  Proposition 3.1 给出 `ζ = 1 − inf_t F′(t)`，并指出「for any `b ∈ [ζ, 1]`」都可写成合法分解。
  印刷页 1051 进一步：「In the nonidentifiable case, **no data-based method can distinguish** `a̲` and `a`」。
- Blanchard, Lee & Scott 2010（印刷页 2984）：「**A lower bound is effectively the best we can hope for** π
  if `P₁` is not assumed to be proper」。
- Zhu 等，*Mixture Proportion Estimation Beyond Irreducibility*，ICML 2023（PMLR 202）／arXiv:2306.01253（印刷页 2）：
  `κ(F|H) = κ* + (1 − κ*)κ(G|H)`，故 irreducibility 失效时既有算法给出的是 `κ(F|H) > κ*`，**渐近有偏**。

## 双向 vs 单向：两种噪声模型

**双向（asymmetric label noise）**：`P̃₀ = (1 − π₀)P₀ + π₀P₁`，`P̃₁ = (1 − π₁)P₁ + π₁P₀`，
`π₀ ≠ π₁` 但两方向都存在，并额外要求 **总噪声 `π₀ + π₁ < 1`**（Scott 等 2013 的条件 (A)）。
此时两个比例的**联合**可行域不是乘积箱：Scott 等 2013 Theorem 12(3)（印刷页 17）给出它是
正象限与两个半平面 `π₀ + π₁π̃₀* ≤ π̃₀*`、`π₁ + π₀π̃₁* ≤ π̃₁*` 的交，即一个**闭四边形**；
mutually irreducible 解是「两个约束同时紧」的那个顶点，也是 `π₀ + π₁` 与 `‖P₀ − P₁‖_TV` 的唯一最大化者（Theorem 12(4)）。

**单向（one-sided label noise）**：只有一个方向的翻转，即 `ρ` 之一恒为 0。
这与 PU learning 是同一件事——Bekker & Davis 综述（arXiv:1811.04820 印刷页 36）逐字：
「A common interpretation of PU learning is that it is the specific type of label noise, called
**one-sided label noise**, where the positive examples can be incorrectly labeled as negative」，
并明确 SCAR 机制下两类的错标概率分别是 `1 − c` 与 **0**。
该提法在学习理论侧至少可追到 Goldberg & Goldman，COLT '94（DOI `10.1145/180139.181131`，未获全文）
与 H. U. Simon，*Annals of Mathematics and AI*, 2012（DOI `10.1007/s10472-012-9325-7`，未获全文）。

## 术语对照（同一件事的四套说法）

| 概念 | FDR 侧 | 新奇检测侧 | MPE / 标签噪声侧 | 部分识别（计量）侧 |
|---|---|---|---|---|
| 已知成分 | 均匀分布 `U` | nominal `P₀` | `H` | 已知的误分类结构 |
| 待定比例 | `a`（备择占比） | `π`（novelty 占比） | `κ` | 污染率 `λ` |
| 可行集 | `O_F` | — | — | identification region |
| 上端闭式 | `1 − inf_t F′(t)` | `π*`（式 (7) 的 min） | `κ*` = `ess inf f/h` | 闭式随假设而变 |
| 恢复识别的条件 | purity | properness | irreducibility | 验证子样本 / 单调性 / 已知上界 |

⚠️ 术语不通导致引用稀疏：`identified set` 与 `mixture proportion` 这两个词在 arXiv 上
（标题＋摘要级检索）**共现为 0**，说明 ML 侧与计量侧基本互不引用。

## 退化情形：已知成分是点质量时

当 `H` 退化为某点上的点质量、且样本空间只有两点时，上述所有恢复识别的条件都退化：
`G` 是「irreducible w.r.t. `δ_x`」⟺ `G` 在 `x` 上质量为 0。
⇒ 此时 purity / properness / irreducibility 只在一个极端点上成立，anchor set 假设无从谈起，
`[0, κ*]` 无法被这一族假设窄化。

## 相关页面

- [标注噪声与标注流水线质量](annotation-noise-and-pipeline-quality.md)
- [符号可解性与定性矩阵理论](sign-solvability-qualitative-matrix-theory.md)
- [评测方法论基础](evaluation-methodology-foundations.md)
