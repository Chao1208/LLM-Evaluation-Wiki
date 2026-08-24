---
title: "符号可解性与定性矩阵理论（sign-solvability）"
type: concept
created: 2026-08-22
updated: 2026-08-22
tags: [定性矩阵理论, 符号模式, sign-solvability, 比较静态, 排序稳健性, 对偶锥, 术语]
sources: [lee-1998-sign-consistency-and-solvability-of-constrained-linear-systems.pdf, viappiani-2020-robust-winner-determination-in-positional-scoring-rules-with-uncertain-weights.pdf]
---

# 符号可解性与定性矩阵理论（sign-solvability）

## 概述

**定性矩阵理论（qualitative matrix theory）** 研究这样一类问题：当一个矩阵的元素只知道**符号**（正 / 零 / 负）、
**数值幅度完全未知**时，矩阵或线性系统的哪些性质仍然被唯一决定。它的核心概念是 **sign-solvability（符号可解性）**：
线性方程组 `Ax = b` 的**解向量的符号模式**能否只凭 `A` 与 `b` 的符号模式定出来。

这一支源自经济学（Samuelson 1947《Foundations of Economic Analysis》），
用于回答「只知道模型结构中各效应的方向，能不能断定均衡量变动的方向」这一比较静态问题，
后来在线性代数、组合数学与理论计算机科学中独立发展，1995 年由 Brualdi & Shader 的专著系统化。

## 基本定义（逐字来源：Lee & Shader 1998, ELA 4:1–18）

- **符号模式（sign pattern）**：把实矩阵每个元素换成其符号得到的 (0,1,−1)-矩阵。
- **定性类（qualitative class）** `Q(B)`：*所有*符号模式等于 `B` 的实矩阵构成的集合（p.2 原文
  “consisting of **all** matrices with sign pattern B”）。
  ⇒ **该理论对幅度不作任何有界性假设**，元素可以任意大、任意小。
- **sign-consistent**：对 `Q(A)×Q(b)` 中每一对 `(Ã, b̃)`，系统 `Ãx = b̃` 都可解。
- **sign-solvable**（p.4）：系统 sign-consistent，且集合
  `{x̃ : ∃ Ã∈Q(A), b̃∈Q(b), Ãx̃ = b̃}` 中所有解**具有同一符号模式**。
- **L-matrix**：符号模式*要求*（requires）行线性无关；**SNS-matrix**：方阵 L-matrix（sign-nonsingular）。
- **balanceable**：*允许*（allows）一个无零元的左零向量。
- 术语约定：符号模式 **requires** 某性质 ＝ `Q(A)` 中每个矩阵都有该性质；**allows** ＝ 至少一个有。

## 概念家族一览

| 名称 | 出处 | 判定对象 |
|---|---|---|
| sign-solvable | Samuelson 1947；Brualdi & Shader 1995（Zbl 0833.15002） | 解向量各分量符号是否全被定住 |
| partial sign-solvable / 分量 sign-determined | Hwang & Park, Bull. Korean Math. Soc.（Zbl 1112.15008） | 至少一个分量符号被定住 |
| conditionally sign-solvable；CS- / CS\*-matrices | Brualdi, Chavey & Shader 1993, Math. Comput. Modelling 17(1):141–148（Zbl 0768.90051）；Xu & Shao（Zbl 1055.15038） | 可解性**与**解的符号模式同时被定住 |
| sign inconsistent | Shao, LAA（Zbl 0932.15002） | 定性类中每个系统都**不可解** |
| sign-consistency（带 `x ⪈ 0` 约束）＋定性 Farkas 引理 | Lee & Shader 1998, ELA 4:1–18, Lemma 3.1 (p.8) | 是否**存在非负解** |
| sign-central（及 strict / tight / nearly 变体） | Ando & Brualdi 1994, LAA 208/209:283–295（Zbl 0818.15017）；Brualdi & Dahl 2015 SIMAX（Zbl 1327.05045） | 原点是否落在列向量凸包内（Gordan 型） |
| asymptotic sign-solvability | Cayton, Herring & Holder 2006, Math. Methods OR（Zbl 1114.90109） | 未获全文，判定对象待补 |
| unambiguous entry（符号模式算术） | Eschenbach, Hall & Johnson 1997, LAA 260:95–118（Zbl 0881.05089） | 两个符号模式**乘积中某一项**的符号是否无歧义（无正负相消） |

⚠️ 检索确认：`sign-semi-solvable`、`one-sided sign-solvability` 在 zbMATH 全库**无任何记录**（2026-08-22 查）
⇒ 这一支**没有**「符号只在单一方向被蕴含」的专有术语；相应的单侧概念只涉及**可解性 / 可行性**，不涉及某个线性泛函的不等号方向。

## 结构与复杂度

- Brualdi & Shader 1995（Cambridge Tracts in Mathematics 116, ix+298 页）章节页码：
  Sign-solvability 1–17｜L-matrices 18–34｜Sign-solvability and digraphs 35–61｜S\*-matrices 62–86｜
  Beyond S\*-matrices 87–105｜SNS-matrices 106–167｜S²NS-matrices 168–202｜
  Extremal properties of L-matrices 203–224｜The inverse sign pattern graph 225–238｜Sign stability 239–258｜
  Related topics 259–288｜Master bibliography 289–294（DOI 前缀 `10.1017/cbo9780511574733`，逐章确证）。
- 识别复杂度：无约束 sign-solvable 系统的识别问题是 **NP-完全**的；而带 `x ⪈ 0` 约束的 sign-consistency 与
  sign-solvability 有**多项式时间**识别算法（Lee & Shader 1998 摘要）。
- 图论刻画：`Sign-solvability and digraphs` 一章把判据翻译成有向图上的条件；`Sign stability` 一章是
  生态学「只凭相互作用符号判系统稳定性」的来源。

## 应用面

书评（zbMATH Zbl 0833.15002，评审人 Giacomo Bonanno）列出的读者群是**经济学、理论计算机、物理、化学、工程**；
另有数学金融方向的动机（strict sign-central）。截至 2026-08-22 的检索**未发现**该支被用于投票、排序或模型评测。

## 相邻但独立的一支：排序中的「对锥内所有权重都成立」

社会选择 / OR 侧有一支形状相似但**互不引用**的结果：位置评分规则（positional scoring rule）的权重未知时，
一个候选者的得分是否**对该权重锥内的所有权重**都不低于另一个候选者，可由**累积名次向量的逐分量比较**判定。

- Stein, Mizzi & Pfaffenberger 1994, EJOR 74(1):78–85（DOI `10.1016/0377-2217(94)90205-4`，未获全文）
  的 Theorem 1(a)(b)：递减权重 ⇒ 一次累积名次向量占优；递减且凸的权重 ⇒ 二次累积名次向量占优。
  分别类比一阶、二阶随机占优。
- Viappiani 2020, Theory and Decision 88(3):323–367（DOI `10.1007/s11238-019-09734-3`，全文已获）
  Proposition 1、2 逐字转述了上述定理，并把 possible / necessary winner 与 minimax regret 接了起来；
  Proposition 6：`x` 是 necessary winner ⟺ 对所有其他候选者 `PMR(x,z;W) < 0`。
- 上游更一般的 dominance 框架：Salo 1995, EJOR（Viappiani 称其结果为该框架的特例，尚未查证）。

数学上这类结果都是**对偶锥**刻画（「对锥中所有线性泛函都成立的不等式」等价于「系数落在对偶锥里」），
其最基础的实例是非负象限自对偶（Boyd & Vandenberghe《Convex Optimization》§2.6.1, Example 2.23）。

## 检索条件（引用本页结论时必须一并引用）

2026-08-22 的检索**无 WebSearch 通道**（配额耗尽），主力为 Crossref 题录（22 条查询）与 **zbMATH Open API**（19 条查询，
可取回专家书评正文）；Brualdi & Shader 1995 专著、Bassett–Maybee–Quirk 1968（Econometrica 36(3):544–563,
DOI `10.2307/1909522`, Zbl 0217.26802）、Maybee–Quirk 1969（SIAM Review 11(1):30–51, DOI `10.1137/1011004`,
Zbl 0186.33503）三份原文**均未获全文**，本页对它们内容的陈述来自 zbMATH 书评与 Lee & Shader 1998 的引用转述。
⇒ 本页任何「未发现 / 无记录」只表示「在上述通道与查询式下没有命中」，不等于该方向为空白。

## 相关页面

- [评测方法论基础](evaluation-methodology-foundations.md)
- [标注噪声与标注流水线质量](annotation-noise-and-pipeline-quality.md)
