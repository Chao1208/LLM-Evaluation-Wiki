---
title: "FBI FRD-501: Verification and Blind Verification（FBI 实验室摩擦脊纹核验与盲核验规程）"
type: source
created: 2026-08-12
updated: 2026-08-12
tags: [盲验证, 核验流程, 确认偏差, 质量控制, 标注噪声, 司法鉴定]
sources: [fbi-frd501-verification-and-blind-verification.pdf]
---

## 概述

这是 FBI 实验室摩擦脊纹（friction ridge，指纹 / 掌纹）鉴定业务的**内部作业规程文件**，不是研究论文，因此本页用「流程定义」替代通常的「方法」章节。文件规定了两种复核形态：verification（核验，复核者知道初检人的评估结论）与 **blind verification（盲核验，复核者信息受限且不知道初检人的评估结论）**，并明确后者的目的是**减少 confirmation bias、限制 contextual bias**。对评测工作的参考价值在于：它是一套已经落地运行多年、把"第二人复核"拆成知情 / 不知情两档并规定各自适用条件、材料交付方式、分歧解决路径的成熟制度设计。

**文件信息**: FBI Laboratory 内部文件, 文件号 FRD-501-11（Verification and Blind Verification）, Revision 11, Issue Date 2024-10-15, 签发人 Laboratory Director, 状态 Current, 共 8 页

## 关键发现

1. **两种核验的分界就在于"复核者知道什么"**。Verification 定义为"由另一名检验人对摩擦脊纹执行 Analysis, Comparison, Evaluation（ACE）"；blind verification 定义为"由另一名检验人执行的一种 verification，该检验人**只有有限的案件信息**，且**不知道初检人的评估决定**"（第 1 节）。
2. **盲核验的资格是负面清单**：不得由满足以下任一条件的检验人执行——曾就待核验的纹线提供咨询、知晓评估结论、掌握详细案件信息（第 2.2-C 节）。普通 verification 的排除条件只有一条：不得由曾就该纹线提供咨询的检验人执行（第 2.1-B 节）。
3. **强制性层级不同**：所有 identification（同一认定）**必须**做 verification，其他评估结论可以做；而 blind verification **必须**在"只有单一评估结论"的案件中执行（第 7、7.1 节列举的情形除外）。此外，当一份将被报告为 identification 的纹线曾经历技术分歧解决时也必须做盲核验，除非 (1) 已启用 Scientific Review Board，或 (2) 是"已知不确定（known inconclusive）"结论在一次 verification 后被改为 identification（第 2.2 节）。
4. **盲核验替代普通核验，且必须在出结果之前完成**：需要时，verification 或 blind verification 必须在向委托方（contributor）提供结果**之前**完成；如已做盲核验，则不再需要单独的 verification（第 3 节）。
5. **由 coordinator 做中介、按轮转选人、并主动防止材料本身造成引导**。coordinator 角色由 supervisor 担任（紧急案件等情形下管理层可书面授权非管理岗承担部分行政职能）；Unit Chief 负责保证（盲）核验人按 rotational basis 轮换选取；第 5.B.2 节特别要求初检人 / coordinator **避免提供可能引导盲核验人的有意采集纹线**——举例：只提供左手食指的多份采集记录而不提供其他手指，会引导盲核验人去比对左手食指。
6. **盲核验人在收到 coordinator 通知前不得与人讨论该纹线**（第 5.E 节）；盲核验人若需咨询其他检验人，由 coordinator 挑选**事先不知道结论**的检验人，且咨询须记入 FBI Laboratory 案卷。
7. **分歧解决会改变记录性质，并防止"循环盲核验"**：若盲核验进入分歧解决流程且盲核验人原始评估结论发生改变，该次比对从 blind verification **降级记为 verification**；若分歧解决后盲核验人把原结论改为 identification，则需要**追加一次盲核验**（除非启用了 Scientific Review Board 或 Technical Leader 依 8.2-C 判定不需要）。**连续两次盲核验未成功后，该检验须由 Technical Leader 复审**，判断已采取的质量措施是否与风险相称，复审结论记入案卷（第 8.2 节）。修订历史显示 8.2-B / 8.2-C 正是在 Revision 10 中为"解决循环盲核验（circular blind verification）"而修改的。

## 流程定义

**核验材料（第 4.A / 5.A 节）**：初检人提供检验人的评估结论、待（盲）核验纹线的**未标记（unmarked）图像**、其得出 ACE 结论所依据的其他图像（盲核验一侧文件明确写为 unmarked image(s)，例如同一纹线用不同显现介质显影的图像）、以及初检人用于支撑评估结论的全部有意采集（intentionally recorded）纹线。materials 的交换由 coordinator 指定并调度；coordinator 或初检人可被要求补充关于纹线（如证据类型）或案件（如自动检索信息）的额外信息，或协调数字图像的传递。

**执行（第 4.C / 5.C 节）**：核验人先按第 2 节确认自身资格，再依 *Examining Friction Ridge Prints*（FRD-500）执行并记录 ACE。可检索 Next Generation Identification System；若自动检索未产生 identification，最终决定必须基于与所提供记录的**人工比对**。可通过 coordinator（盲核验）或初检人（普通核验）索取原始及数字处理后图像的数字副本。

**记录（第 4.D / 5.D 节）**：在提供的未标记图像上做全部适当标注，包括依 FRD-500 记录得出结论所用信息、达成的分析与评估结论（若判定不适于比对，必须记录"（盲）核验已完成且结论为 not suitable for comparison"）、被比对个体的姓名或唯一编号（如 Universal Control Number）、注明这是 verification 还是 blind verification、（盲）核验人签名与日期或日期区间；并在案卷中标明提供了哪些有意采集纹线（如原件在保管链上的移交、或数字/实物副本的加密签注）。无分歧时案卷记录三项：所达成结论、哪些纹线被（盲）核验、（盲）核验人身份。

**收尾差异**：普通核验完成后由核验人把材料退回初检人（第 4.E 节）；盲核验完成后由盲核验人通知 coordinator 并把材料交回指定位置，**由 coordinator 复核盲核验人的结论并指示把全部记录退还初检人**（第 5.E 节）——即初检人被隔离在流程之外。

**不需要盲核验的情形（第 7 节，可做但非强制）**：身份不明的死者；标准或非标准的有意采集纹线；NGI 比对（含 Unsolved Latent Match cascade 检验，仅限 exclusion 与 inconclusive 结论）；被报告的 latent-to-latent identification 只需 verification（latent-to-latent 的其他结论既不做 verification 也不做 blind verification）；被认定个体在相关联的事件 / 案件 / 送检中已被认定并核验过的 identification（此时若不做盲核验，则新的 identification 需要一次 verification）；对已与案卷中另一纹线认定为同一人的个体所作的单一 inconclusive 结论。

**Unknown Biometric Identity Tracker（第 7.1 节）**：Tracker 收集由 Unsolved Latent File 自动检索产生的跨事件 latent-to-latent identification，这些关联在最初建立时都已核验。当 Tracker 中一组相互关联的 latent 纹线与某个已知个体建立同一认定时，所需质量检查为二者之一：(a) 已知记录与该组中**单一** latent 纹线之间做一次 **blind verification**；或 (b) 已知记录与该组中**两枚** latent 纹线之间做 verification，前提是这两枚 latent 来自**两个不同的事件编号**。满足其一后不再需要追加（盲）核验。

**多个个体的单一 exclusion / inconclusive（第 6 节）**：盲核验针对其中一名或多名已知个体执行，个体数量由 coordinator 依案件信息或与初检人讨论后裁定，或由专业管理层规定。

**分歧解决入口（第 8 节）**：出现结论差异（纹线类型、是否适于比对、任何评估决定）时，援引 *FBI Laboratory Operations Manual*（LAB-200）与 *Disagreements and Scientific Review Boards in Technical Casework*（FRD-502）。若只是对"claimed 纹线类型"（指纹 / 掌纹 / 压痕）有分歧而评估结论一致，双方须讨论并就单一分析结论达成一致，且双方须就商定的类型完成当时可做的全部与有意采集记录的比对；对盲核验而言，追加的比对记为额外的 verification / examination，**原结论仍记为 blind verification**（第 8.1 节）。

## 重要数据

本文件为规程文本，不含实测统计数据。可引用的结构化事实如下。

| 项目 | verification | blind verification |
|---|---|---|
| 复核者是否知道初检结论 | 知道（初检人提供评估结论） | 不知道（明确列为不得具备的条件） |
| 复核者掌握的案件信息 | 未作限制 | 仅有限案件信息，不得掌握详细案件信息 |
| 强制适用范围 | 全部 identification 必须核验 | 单一评估结论的案件必须盲核验（第 7、7.1 节除外）；经技术分歧解决后将报告为 identification 的纹线（两种例外情形除外） |
| 选人与材料交付 | coordinator 指定核验人并调度材料 | coordinator 指定盲核验人；须避免提供可能引导的有意采集纹线 |
| 收尾 | 核验人把材料退回初检人 | 盲核验人通知 coordinator；coordinator 复核结论并指示退还记录 |
| 结论改变的后果 | — | 分歧解决中原结论改变即降级记为 verification；改为 identification 需追加一次盲核验 |
| 失败上限 | — | 连续两次盲核验未成功 → Technical Leader 复审 |

修订历史（第 9 节）：

| 修订号 | 签发日期 | 主要变更（节选） |
|---|---|---|
| 10 | 2023-08-15 | 2.2 删除 "exception" 一词；3.1 增加 coordinator 授权；4-B / 4-C 把 verifier 列为可选项；4-E 取消 coordinator 复核、改由 verifier 负责退还材料；5-B 增加初检人提供有意采集纹线；5-E 修改记录选项；第 7 节改标题、原第 8 节降为子节、增加 inconclusive 允许项；删除原 9.2；8.2-B 与 8.2-C 修改方法以处理循环盲核验 |
| 11 | 2024-10-15 | 全文把 "known exemplar" 改为 "intentionally recorded prints"；2.2 改为 conclusion；第 4、5 节增加 coordinator 需提供的补充信息；第 6 节增加管理层选项；8.1 澄清 |

## 局限与假设

- **这是单一机构的作业规程，不是效力研究**：文件只规定"应该怎么做"，**不提供**盲核验降低错误率或降低偏差的任何实测数据、错误率、一致率或统计证据。"减少 confirmation bias、限制 contextual bias"是文件陈述的**理由（rationale）**，不是文件内的实证结论。
- **盲性是部分而非完全的**：文件第 5.A 节把"检验人的评估结论"列入初检人须提供的材料清单，同时第 2.2-C 节禁止盲核验人知晓评估结论——两者的相容依赖 coordinator 作为中介来隔离信息。文件本身未逐条说明哪些材料到达盲核验人手中，此处依赖流程执行而非文本约束。
- **大量豁免情形**：第 7 / 7.1 节列出的一长串"不要求盲核验"的场景，意味着实际业务中相当比例的结论只经过知情核验或不经核验（例如 latent-to-latent 的非 identification 结论既不核验也不盲核验）。把该制度类比到评测抽检时，这些豁免边界同样需要显式设计。
- **依赖组织结构**：coordinator（由 supervisor 担任）、Unit Chief（保证轮转）、Technical Leader（失败复审）、Scientific Review Board 等角色是制度成立的前提；缺少这类分层角色时，盲性与分歧解决都无法落地。
- **文件外部依赖**：ACE 的具体执行标准在 FRD-500，分歧与 Scientific Review Board 机制在 FRD-502，实验室通用规则在 LAB-200 —— 本文件不自足，单独阅读无法还原完整流程。
- **未标注发布单位的更细层级**：文件页脚仅显示文件号、页码、签发日期与签发人（Laboratory Director），可确认属 FBI Laboratory 摩擦脊纹业务线（FRD 系列），但文件内未出现具体处 / 科室名称（只出现 Unit Chief 这一角色）。

## 相关页面

- [标注噪声与标注流水线质量](../concepts/annotation-noise-and-pipeline-quality.md)
- [评测方法论基础](../concepts/evaluation-methodology-foundations.md)
- [LLM-as-Judge](../concepts/llm-as-judge.md)
- [Pervasive Label Errors in Test Sets](./2103.14749-pervasive-label-errors-test-sets.md)
