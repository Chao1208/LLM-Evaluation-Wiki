# 研究选题报告：无训练 LLM-as-Judge — 可迁移的评测规范设计

> 日期: 2026-04-23
> 状态: 第三版（核心创新点确立：评测规范的迁移学习）

---

## 1. 问题定义

### 1.1 行业痛点

评测团队的典型工作循环：

```
第 1 周: Test Set v1 → 人工精磨 Rubric → 高一致率 ✓  (大量人力，多轮迭代)
第 3 周: Test Set v2 → 重新精磨 Rubric → 高一致率 ✓  (几乎同等人力)
第 5 周: Test Set v3 → 又重新精磨 ...                 (无限循环)
```

**核心矛盾**: 评测规范没变（同样的质量标准、同样的评分逻辑），只是具体 case 变了，但 rubric 精磨的工作量几乎不减少。现有的 rubric 是 **case-bound** 的——绑定在具体 case 上，而不是绑定在可复用的评测逻辑上。

### 1.2 核心问题

> 如何将评测规范的精磨工作转化为**可迁移的资产**——在一份数据上投入精力打磨的评测经验，能够零/低成本地迁移到新的测试集上，保持同等评测质量？

### 1.3 量化目标

- 在 Split A 上精磨 Specification 达到 α ≥ 0.80（α = LLM Judge 与人工共识的 case 级一致性，详见 §5.5）
- 迁移到 Split B 后（仅用 1 批次人工数据校准），α 下降 ≤ 0.03
- 在 4 个独立数据集上均验证此效果
- 在多个推理模型（GPT / GLM / Qwen）上均稳定

### 1.4 约束条件

- 不训练/微调 Judge 模型（纯 specification-driven）
- 方法需跨数据集通用（不绑定具体领域）
- 迁移过程需自动化（人工仅参与 review，不参与 rubric 编写）

---

## 2. 核心创新：评测规范的迁移学习

### 2.1 类比：跨语种语音识别迁移

```
跨语种 ASR:
  中文 10K 小时训练 → [共享声学表征层] + [中文输出层]
  迁移: 保留共享层 → 换日语输出层 → 几百小时微调 → ≈ 几千小时效果

评测规范迁移:
  Split A 多轮精磨 → [共享评测逻辑层] + [A 的 case-specific rubric]
  迁移: 保留共享层 → 换 B 的 case 层 → 1 批次人工校准 → ≈ 多轮精磨效果
```

### 2.2 两层架构

```
┌─────────────────────────────────────────────────────────┐
│  共享评测逻辑层 (Shared Evaluation Logic)                   │  ← 迁移时直接复用
│  - 评测维度定义（如：解决方案/追问能力/分诊/风格）                  │
│  - 每个维度的判定决策树                                       │
│  - 质量锚点（什么算好/什么算差的通用 anchor）                    │
│  - 错误模式库（从 Split A 不一致中归纳的典型错误）                │
│  - 评分逻辑（正/负分规则、权重、聚合方式）                        │
│  - 已验证的 prompt 模板                                     │
└──────────────────────────┬──────────────────────────────┘
                           │ 自动实例化 + 少量校准
                           ▼
┌─────────────────────────────────────────────────────────┐
│  Case-Specific 实例层 (Case-Specific Rubrics)             │  ← 自动生成
│  - 针对具体 query 的具体 criteria                            │
│  - 该 case 的领域特定判定锚点                                 │
│  - 该 case 的边界条件和特殊注意事项                             │
└─────────────────────────────────────────────────────────┘
```

| 层 | ASR 类比 | 评测规范类比 | 迁移方式 |
|---|---------|----------|---------|
| **共享层** | 声学特征提取 (hidden layers) | 维度、判定逻辑、锚点、错误模式库 | 直接复用 |
| **实例层** | 语言特定音素映射 (output layer) | case-specific criteria 和锚点 | 少量数据重新生成 |

### 2.3 为什么现有方法不解决这个问题

| 方法 | 为什么不够 |
|------|----------|
| AdaRubric | 每次从任务描述重新生成维度，不复用之前打磨的经验 |
| RubricRAG | 检索相似 rubric，但没有"规范→rubric"的生成逻辑 |
| LH-Bench | 专家 rubric 是静态的，不能自动适配新 case |
| DeCE | 分解框架固定（precision/recall），不从数据中学习评测逻辑 |
| 人工编写 | 就是痛点本身 |

### 2.4 我们的核心主张

> **"Specification Once, Evaluate Anywhere"** — 将人工精磨评测规范的一次性投入，转化为可迁移、可复用的评测资产。通过两层架构分离共享评测逻辑与 case-specific rubric，实现评测知识的高效迁移。

---

## 3. 算法设计

### 3.1 Phase 1: Split A 上自动精磨 Specification

**输入**: Split A cases + 多轮 LLM Judge 结果 + 人工 Judge 结果

```
初始化: 从评测规范文档生成初始 Specification S₀

for round = 1, 2, 3, ...:
    1. 评测: 用 S_round 驱动 LLM Judge 对 Split A 评分
    2. 对比: LLM 判定 vs 人工判定 → 不一致集合 D
    3. 诊断: 对每个不一致 (case, criterion) ∈ D，自动分类原因:
       - TYPE_AMBIGUOUS:  criteria 表述模糊 → 改写为 binary-observable
       - TYPE_MISSING:    缺少锚定示例 → 从一致 case 中提取正/反例
       - TYPE_GAP:        维度缺失 → 从不一致模式中归纳新维度
       - TYPE_WEIGHT:     权重不合理 → 根据人工判定调整
       - TYPE_EDGE:       边界 case → 补充边界条件到决策树
    4. 修复: Opus 自动修改 Specification（人工 review）
    5. 检查: α ≥ 0.80 且 |D| 收敛? → 输出 S*; 否则继续
```

**输出**: 精磨后的 Specification S* = {共享层, A 的实例层}

**关键**: 不一致的 case 是最有价值的信号——每轮修复的信息被编码进共享层（判定逻辑、错误模式库、锚点），而非仅修复 case-specific rubric。

### 3.2 Phase 2: 迁移到 Split B

**输入**: Split B cases + S* 的共享层 + 1 批次人工 Judge 结果（约 50 case）

```
    1. 复用共享层: 评测维度、判定逻辑、决策树、通用锚点 → 直接保留
    2. 生成实例层:
       - 对 B 的每个 case，基于共享层的维度定义 + 通用锚点
       - 自动生成 case-specific criteria
       - 利用共享层中的错误模式库预判可能的边界情况
    3. 校准 (用 1 批次数据):
       - 在有人工标注的 ~50 case 上对比: 自动 rubric vs 人工判定
       - 微调: 调整权重/阈值/表述
       - 仅调实例层，不动共享层
    4. 部署: 校准后的 Spec 应用到 B 的全部 case
```

**输出**: B 上的完整评测结果 + B 的 Specification

**类比**: 就像 ASR 迁移中，固定 hidden layers，只在新语言的小数据上 fine-tune output layer。

---

## 4. 文献支撑

### 4.1 关键发现（支持我们的方法设计）

| 发现 | 来源 | 对我们的启示 |
|------|------|----------|
| 自适应维度生成 (+0.14r) > 骨干模型能力 (-0.11) | AdaRubric | Specification 质量比模型能力更重要 |
| Precision+Recall 分解: r 从 0.12→0.78 | DeCE | 共享层应包含结构化的分解逻辑 |
| 专家 rubric κ=0.60 vs LLM κ=0.46 | LH-Bench | 共享层需编码专家级的 binary-observable 标准 |
| GPT-4o Hard rubric 判断仅 55.97% | RubricEval | 实例层的 criteria 表述质量至关重要 |
| Task-specific > generic prompt (100%→90%) | MVES | 实例层必须 case-specific，不能用通用模板 |
| 跨域迁移有效 | AdaRubric/Cross-Lingual | 共享层确实可以跨 case 迁移 |
| α ≥ 0.80 是实用部署标准 | AdaRubric | 精磨目标和校准验收标准 |

### 4.2 Research Gap

| Gap | 说明 | 我们如何填补 |
|-----|------|----------|
| **评测知识不可复用** | 换 case 就要重新磨 rubric，行业通病 | 两层架构 + 迁移算法 |
| **精磨过程不可自动化** | 多轮迭代依赖人工逐条调整 | Phase 1 自动诊断+修复 |
| **缺少迁移效果的量化** | 没有工作度量"评测经验"的迁移效率 | Split A/B 实验设计 |
| **多模型鲁棒性未验证** | 同一 specification 跨模型表现未知 | GPT/GLM/Qwen 多模型矩阵 |

---

## 5. 实验设计

### 5.1 数据集与切分

每个数据集一切为二，独立验证迁移效果：

| 数据集 | 总量 | Split A (精磨) | Split B (迁移) | 来源 |
|--------|------|---------------|---------------|------|
| 392 语音助手 | ~392 case | ~196 | ~196 | 内部，已有人工标注 |
| 医疗 | 550 case | ~275 | ~275 | 内部，11批×50 case |
| 开源 A | 待定 | 一半 | 一半 | 公开，需有人类标注 |
| 开源 B | 待定 | 一半 | 一半 | 公开，需有人类标注 |

### 5.2 推理模型矩阵

| 模型 | 用途 | 说明 |
|------|------|------|
| **Opus** | Phase 1 精磨中的诊断与修复 | 最强模型做 Specification 设计 |
| **GPT-4o** | Judge 推理 | 顶级商业模型基线 |
| **GLM5.1** | Judge 推理 | 中等能力模型 |
| **Qwen-2.5** | Judge 推理 | 开源模型代表 |

### 5.3 实验矩阵

| 实验 | 目的 | 设计 |
|------|------|------|
| **E1: 精磨收敛** | Phase 1 自动精磨的收敛速度和最终质量 | 记录每轮迭代的 α/r/不一致数，直到收敛 |
| **E2: 迁移效果** | 核心实验——Split A→B 的迁移后 IRA 下降多少 | 4 个数据集 × (A精磨 IRA vs B迁移 IRA vs B人工精磨 IRA) |
| **E3: 校准数据量** | 迁移时需要多少人工数据 | 0 / 10 / 25 / 50 case 的校准数据，观察 α 恢复曲线 |
| **E4: 多模型鲁棒性** | 同一 Specification 在不同推理模型上的表现 | 4 数据集 × 3 模型，同一 Spec 比较 |
| **E5: 消融实验** | 两层架构各组件的贡献 | 去掉错误模式库/去掉锚点/去掉决策树/去掉校准步骤 |
| **E6: 基线对比** | 与现有方法的公平对比 | 直接 Prompting / HealthBench 式 / DeCE / AdaRubric |

### 5.4 人工标注协议

**双人独立标注 + 对齐（Adjudication）**:

```
标注员 A ──独立标注──┐
                     ├→ 对齐会议 → 人工共识 (Gold Standard)
标注员 B ──独立标注──┘
```

1. 两位领域标注员对同一 case 的**每条 criterion** 独立判定 True/False
2. 标注完成后进行对齐（adjudication），讨论解决分歧
3. 对齐后的共识结果作为 **"人工 Judge"** 的最终判定（Gold Standard）
4. 历史经验：对齐前双人不一致率 < 5%

**争议 case 处理策略**:

| 处理方式 | 说明 | 用途 |
|---------|------|------|
| **排除争议 case（主表）** | 仅保留对齐后双人完全一致的 case | 主要报告指标 |
| **含争议 case（附表）** | 以对齐后共识为准，但标记争议 case | 敏感性分析 |
| **争议 case 分析** | 分析 LLM Judge 在争议 case 上的判定分布 | 洞察 LLM 边界识别能力 |

**注**: 争议 case 往往是人工也难以确定的"边界 case"。如果 LLM 在这些 case 上同样表现不确定（例如多次推理结果不一致），这反而说明 Specification 正确编码了评判难度——可作为一个有趣的附加分析。

### 5.5 评估指标

**两层评估粒度**:

| 层级 | 粒度 | 比较对象 | 适用指标 |
|------|------|---------|---------|
| **Criterion 级** | 每条 rubric criterion 的 True/False | LLM 判定 vs 人工共识 | Agreement Rate, Cohen's κ |
| **Case 级** | 每个 case 的加权综合分 (0–1) | LLM 综合分 vs 人工综合分 | Pearson r, Krippendorff's α |

**指标定义与目标**:

| 指标 | 层级 | 定义 | 目标 |
|------|------|------|------|
| Agreement Rate | Criterion | (LLM 判定 = 人工共识的 criterion 数) / 总 criterion 数 | ≥ 85% |
| Cohen's κ | Criterion | 一致率扣除随机一致比例（2 评审者、名义量尺） | ≥ 0.70 |
| Pearson r | Case | LLM 综合分与人工综合分的线性相关 | ≥ 0.78 |
| Krippendorff's α | Case | LLM 综合分与人工综合分的绝对一致性（区间量尺） | Split A ≥ 0.80, Split B ≥ 0.77 |
| **Transfer Gap** | Case | α(Split A) − α(Split B) | **≤ 0.03**（核心指标）|
| Calibration Efficiency | — | 迁移到 Split B 时，达到 α ≥ 0.77 所需校准样本数 | ≤ 50 case |
| Cross-model Δ | Case | 同一 Spec、不同推理模型的 α 差异范围 | ≤ 0.05 |
| 迭代轮次 | — | Phase 1 精磨收敛所需轮次 | ≤ 5 轮 |

**术语澄清**:

- **"人工共识"** = 两位标注员独立标注 → 对齐后的一致判定结果（Gold Standard）
- **Krippendorff's α (interval)**: 将 LLM Judge 和人工共识视为 2 个"评审者"，对 case 级分数（连续 0–1）计算区间量尺下的一致性。α = 1 表示完全一致，α = 0 表示等同于随机。阈值 0.80 参考 AdaRubric 的部署标准
- **Cohen's κ (nominal)**: 用于 criterion 级二值判定（True/False），校正偶然一致。阈值 0.70 参考 McHugh (2012) 的"substantial agreement"等级
- **Pearson r** 不校正系统偏差（如 LLM 整体偏高/偏低），α 会惩罚这种偏差——两者互补

---

## 6. 拟定论文标题

> **"Specification Once, Evaluate Anywhere: Transferable Evaluation Programs for Training-Free LLM Assessment"**
>
> 规范一次，评测通用：面向无训练 LLM 评测的可迁移评测规范

**备选**:
- "Reusable Evaluation Specifications: A Transfer Learning Approach to LLM-as-Judge"
- "From Case-Bound to Transferable: Two-Layer Evaluation Specifications for Scalable LLM Assessment"

---

## 7. 论文故事线

1. **痛点**: 换测试集就要重磨 rubric，评测规范的投入无法复用（行业通病）
2. **洞察**: 评测知识有两层——可迁移的评测逻辑 + case-specific 的实例化标准
3. **类比**: 像迁移学习一样，共享层直接复用，实例层用少量数据重新生成
4. **方法**: 两层架构 + Phase 1 自动精磨 + Phase 2 低成本迁移
5. **验证**: 4 个数据集 × Split A/B × 3 个推理模型，Transfer Gap ≤ 0.03
6. **贡献**: 把 rubric 精磨的工作量从 O(N × case) 降到 O(1) + O(校准)

---

## 8. 下一步

1. **选定开源测试集** — 从 MT-Bench / AlpacaEval / HealthBench(英文) / PRBench 中选 2 个有人类标注的
2. **设计共享层 schema** — 定义共享层包含哪些具体组件（维度/决策树/锚点/错误模式库的数据结构）
3. **实现 Phase 1 自动诊断算法** — 不一致 case 的自动分类和修复建议生成
4. **在医疗 Split A 上跑第一轮** — 验证自动精磨的可行性
5. **迁移到医疗 Split B** — 验证 Transfer Gap
