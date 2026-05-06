# 实施路线图：Specification Once, Evaluate Anywhere

> 日期: 2026-04-23
> 状态: 初版
> 前置文档: [001-研究选题报告](001-specification-driven-judge-proposal.md)

---

## 总览

```
Phase 0          Phase 1          Phase 2              Phase 3            Phase 4           Phase 5
数据准备  ──→  共享层设计  ──→  自动精磨(医疗A)  ──→  迁移验证(医疗B)  ──→  扩展3个域  ──→  多模型+消融
 1 周            1 周             2-3 周                1 周              3-4 周            2 周
```

**关键依赖**:
- Phase 2 需要 Phase 0 的数据 + Phase 1 的 schema
- Phase 3 需要 Phase 2 的精磨后 Specification
- Phase 4 可以在 Phase 3 完成后并行推进多个域
- Phase 5 可以和 Phase 4 后半段交叉进行

---

## Phase 0: 数据准备

### 0.1 医疗数据

**现有数据**:
- `project/raw-data/evaluation-data/合并评测数据_10版本_已排序_白盒7批_带人工答案原因.xlsx` — 7 批白盒，每批 50 case × 4 维度
- `project/raw-data/evaluation-data/合并评测数据_10版本_已排序_黑盒3批_带人工答案原因.xlsx` — 3 批黑盒
- `project/raw-data/evaluation-data/新白盒1个（编号0417）_50session_3528.xlsx` — 新增 1 批白盒
- `project/raw-data/evaluation-data/健康数据case_index汇总.xlsx` — 全量 case 索引
- `project/raw-data/rules/标注主规范融合标准.md` — 标注规范
- `project/raw-data/rules/AI问诊标准完善.md` — 补充标准
- `project/raw-data/prompts/system_{solution,questioning,dispatch,style}.md` — 4 维度现有 prompt

**Split A/B 设计**:

注意：这里的 Split A/B 是**论文实验用**的，与 HealRub 项目的 train/validate/OOS 划分不同。

| Split | 用途 | 数据源 | 规模 |
|-------|------|--------|------|
| **Split A（精磨集）** | Phase 1 精磨 + Phase 2 自动精磨 | 白盒 batch 1-4 | ~200 case |
| **Split B（迁移集）** | Phase 3 迁移验证 | 白盒 batch 5-8 | ~200 case |
| **Split B-cal（校准子集）** | 迁移时的校准数据 | Split B 中随机抽 50 case | 50 case |
| **OOS（保留集）** | 最终独立验证 | 黑盒 3 批 | ~150 case |

**原则**:
- 按 batch 整批划分（而非随机抽样），因为不同 batch 可能有系统性差异（时间、标注员）
- 黑盒数据严格隔离，不参与精磨也不参与迁移验证，仅最终报告
- Split B-cal 的 50 case 用于迁移校准后，从 Split B 的评估中排除（或单独报告）

**交付物**:
- [ ] `project/data/medical/split_a.json` — Split A cases (query + dialogue + human_judge per dimension)
- [ ] `project/data/medical/split_b.json` — Split B cases
- [ ] `project/data/medical/split_b_cal.json` — 校准子集
- [ ] `project/data/medical/metadata.json` — 切分元信息（batch 映射、case 统计）

### 0.2 392 语音助手数据

**现有数据**:
- `raw/benchmarks/392toolkit/392_q_rubric_all.xlsx` — 全量 392 query + rubric
- `raw/benchmarks/392toolkit/策略库.py` — rubric 策略库（Python 代码）
- `raw/benchmarks/392toolkit/4_black_box_392.csv` / `6_white_box_392.csv` — 黑盒/白盒评测数据
- `raw/benchmarks/392toolkit/十批数据整理（修正评分版）.xlsx` — 10 批评测数据
- `raw/benchmarks/392toolkit/超黑盒/` — 多 persona 超黑盒评测

**需要做的**:
1. 分析 392 query 的类别分布，筛选出适合 rubric 评测实验的子集
2. 确认哪些 case 有人工标注（human judge）
3. 设计 Split A/B 切分策略（可按 query 类别分层抽样）

**交付物**:
- [ ] `project/data/voice_assistant/split_a.json`
- [ ] `project/data/voice_assistant/split_b.json`
- [ ] `project/data/voice_assistant/metadata.json`

### 0.3 开源数据集选择

**候选**:

| 数据集 | 有人工标注? | 有 Rubric? | 规模 | 语言 | 适合性 |
|--------|-----------|-----------|------|------|--------|
| MT-Bench | ✓ (GPT-4 + 部分人工) | ✗ (整体评分) | 80 题 | EN | 规模偏小 |
| AlpacaEval | ✓ (人工偏好) | ✗ (成对偏好) | 805 题 | EN | 非 rubric 模式 |
| HealthBench | ✓ (医生共识) | ✓ (48K criteria) | 5,000 对话 | EN | 很合适，但与医疗域重叠 |
| PRBench | ✓ (专家 IRA 93.9%) | ✓ (19K criteria) | 1,100 任务 | EN | 很合适，法律+金融 |
| WildBench | ✓ (人工) | ✗ | 1,024 | EN | 非 rubric |
| LLMBar | ✓ (人工+对抗) | ✗ | 419 | EN | 元评测，非 rubric |

**初步建议**: PRBench + HealthBench（或另一个非医疗领域的 rubric-based 数据集）

**需要验证**:
1. PRBench 的原始数据是否可获取（criteria + human annotations）
2. HealthBench 数据可获取性（已有 simple-evals 仓库）
3. 是否有其他非医疗、非法律的 rubric-based 开源数据集

**交付物**:
- [ ] `project/data/opensource_a/` — 第一个开源数据集
- [ ] `project/data/opensource_b/` — 第二个开源数据集

---

## Phase 1: 共享层 Schema 设计

**目标**: 定义 Specification 的具体数据结构，使得"共享评测逻辑"可以被机器读取、LLM 解析、自动迁移。

### 1.1 Schema 定义

设计一个 YAML/JSON schema，包含以下组件:

```yaml
specification:
  metadata:
    domain: string          # 评测领域（如 medical, voice_assistant）
    version: string
    created: date
    last_refined: date
    refinement_rounds: int  # Phase 1 精磨轮次
    
  shared_layer:             # ← 迁移时直接复用
    dimensions:             # 评测维度定义
      - name: string
        description: string
        sub_dimensions: [...]
        weight: float
        aggregation: min|avg|weighted_sum
        
    decision_trees:         # 每个维度的判定逻辑
      - dimension: string
        nodes: [...]        # 决策树节点
        
    quality_anchors:        # 通用质量锚点
      - dimension: string
        level: int          # 分档
        description: string
        positive_examples: [string]
        negative_examples: [string]
        
    error_patterns:         # 从 Split A 不一致中归纳的典型错误
      - pattern_id: string
        description: string
        affected_dimensions: [string]
        diagnostic_rule: string   # 如何识别
        correction: string        # 如何在 rubric 中预防
        
    scoring_logic:          # 评分规则
      dimension_aggregation: string  # 维度→总分的聚合方式
      score_range: [min, max]
      clip_range: [0, 1]
      special_rules: [...]   # veto、min-normalized 等
      
    prompt_templates:       # 已验证的 prompt 模板
      judge_system: string
      judge_user: string
      output_format: string
      
  instance_layer:           # ← 每个 case 自动生成
    cases:
      - case_id: string
        criteria:           # case-specific rubric
          - criterion_id: string
            dimension: string
            description: string   # binary-observable 表述
            polarity: positive|negative
            weight: float
            boundary_notes: string  # 边界条件
```

### 1.2 医疗域初始 Specification

用现有的 4 维度 prompt（`project/raw-data/prompts/system_*.md`）+ 标注规范（`标注主规范融合标准.md`）实例化 schema:

| 维度 | 子维度 | 分值范围 | 聚合方式 |
|------|--------|---------|---------|
| solution（解决方案） | accuracy, relevance, completeness, memory | [-2, 3] | min |
| questioning（对话引导/反问） | 逻辑, 质量, 效率, 必要性 | [-2, 3] | min |
| dispatch（服务调度） | 小结时机 | [-2, 3] | single |
| style（表达风格） | 易懂性, 简洁性, 情感关怀, 展现/排版 | [-2, 3] | min |

**交付物**:
- [ ] `wiki/researches/003-shared-layer-schema.md` — Schema 定义文档
- [ ] `project/data/specifications/medical_v0.yaml` — 医疗域初始 Specification

### 1.3 Schema 通用性验证

用 392 语音助手的 rubric 结构（strategy1/strategy2 双策略）检验 schema 是否能表达不同领域的评测逻辑。如果不能，调整 schema。

---

## Phase 2: Phase 1 算法实现 — 自动精磨（医疗 Split A）

### 2.1 诊断模块

**输入**: LLM Judge 判定 vs 人工共识，不一致 case 列表

**核心逻辑**:
```
for each inconsistent (case, criterion):
    1. 提取上下文: case 内容 + criterion 文本 + LLM 判定 + 人工判定 + LLM 理由
    2. Opus 分类: → TYPE_AMBIGUOUS / TYPE_MISSING / TYPE_GAP / TYPE_WEIGHT / TYPE_EDGE
    3. 生成修复建议: 具体的 Specification 修改提案
```

**交付物**:
- [ ] `project/HealRub/src/diagnosis.py` — 诊断模块
- [ ] `project/HealRub/src/prompts/diagnosis_system.md` — Opus 诊断 prompt

### 2.2 修复模块

**输入**: 诊断结果 + 当前 Specification

**核心逻辑**:
```
for each diagnosis:
    AMBIGUOUS  → 改写 criterion 为 binary-observable（参考 LH-Bench）
    MISSING    → 从一致 case 中提取正/反例 → 补充锚点
    GAP        → 从不一致模式归纳新维度/子维度
    WEIGHT     → 基于人工判定方向调整权重
    EDGE       → 补充边界条件到决策树
```

**交付物**:
- [ ] `project/HealRub/src/repair.py` — 修复模块
- [ ] `project/HealRub/src/prompts/repair_system.md` — Opus 修复 prompt

### 2.3 迭代循环

```
S₀ = initial specification (from Phase 1)
for round = 1 to max_rounds:
    judgments = run_judge(split_a, S_round, model="glm5.1")
    diffs = compare(judgments, human_consensus)
    if α >= 0.80 and |diffs| converged:
        break
    diagnoses = diagnose(diffs, S_round, model="opus")
    S_round+1 = repair(diagnoses, S_round, model="opus")
    human_review(S_round+1)  # 人工审核修改
```

**交付物**:
- [ ] `project/HealRub/src/iterate.py` — 迭代主循环
- [ ] `project/data/iterations/medical/round_{n}/` — 每轮结果存档
- [ ] 收敛曲线数据（α, r, agreement, |diffs| per round）

**目标**: ≤5 轮收敛到 α ≥ 0.80

---

## Phase 3: Phase 2 算法实现 — 迁移验证（医疗 Split B）

### 3.1 实例层自动生成

```
shared_layer = S*.shared_layer  # 从 Split A 精磨后的 Specification 中提取
for each case in split_b:
    instance = generate_instance(case, shared_layer, model="opus")
    # 基于共享层的维度定义 + 通用锚点 + 错误模式库
    # 生成 case-specific criteria
```

### 3.2 校准

用 Split B-cal（50 case）进行微调:
- 仅调实例层参数（权重/阈值/表述），不动共享层
- 测试 0 / 10 / 25 / 50 case 的校准数据量对 α 的影响（E3 实验）

### 3.3 Transfer Gap 测量

```
α_A = evaluate(split_a, S*, judge_model)
α_B = evaluate(split_b_remaining, S_transferred, judge_model)
transfer_gap = α_A - α_B  # 目标 ≤ 0.03
```

**交付物**:
- [ ] `project/HealRub/src/transfer.py` — 迁移模块
- [ ] `project/HealRub/src/calibrate.py` — 校准模块
- [ ] 迁移效果数据（α_A, α_B, transfer_gap, calibration curve）

---

## Phase 4: 扩展到其他域

对每个域重复 Phase 2-3:

| 域 | 数据源 | 维度（预估） | 优先级 |
|----|--------|------------|--------|
| 392 语音助手 | 内部 | solution + questioning + style（3维度） | P1 |
| 开源 A（PRBench） | 公开 | 法律/金融多维度 | P1 |
| 开源 B（待定） | 公开 | 待定 | P2 |

**每个域的交付物**:
- 初始 Specification → 精磨后 Specification → 迁移后 Specification
- α_A, α_B, transfer_gap 数据
- 收敛曲线

---

## Phase 5: 多模型鲁棒性 + 消融 + 基线对比

### 5.1 多模型实验 (E4)

同一 Specification，换不同推理模型:

| 模型 | 定位 |
|------|------|
| GPT-4o | 顶级商业 |
| GLM-5.1 | 中等能力 |
| Qwen-2.5 | 开源代表 |

4 域 × 3 模型 = 12 组实验，报告 Cross-model Δ

### 5.2 消融实验 (E5)

去掉共享层各组件，测量 α 下降:
- −错误模式库
- −质量锚点
- −决策树
- −校准步骤
- 仅共享层（无校准）

### 5.3 基线对比 (E6)

| 基线 | 说明 |
|------|------|
| Direct Prompting | 通用 prompt，无 rubric |
| HealthBench 式 | 人工编写 case-specific rubric（人工上限） |
| DeCE | Precision+Recall 分解 |
| AdaRubric | 任务自适应 rubric 生成 |
| 本方法（无迁移） | Split B 上从头精磨（对照：迁移 vs 重新精磨的成本） |

---

## 时间线概估

```
Week 1:     Phase 0 (数据准备) + Phase 1 (schema 设计)
Week 2-3:   Phase 1 完成 + Phase 2 开始 (医疗 Split A 精磨)
Week 4:     Phase 2 完成 + Phase 3 (医疗迁移验证)
Week 5-7:   Phase 4 (392 + 开源 A + 开源 B)
Week 8-9:   Phase 5 (多模型 + 消融 + 基线)
Week 10:    论文撰写
```

---

## 与 HealRub 项目的关系

本实施路线图是**论文级**的实验计划，与 HealRub 项目有重叠但不等同：

| 维度 | HealRub 项目 | 论文实验 |
|------|-------------|---------|
| **目标** | 提升医疗 Judge 准确率 | 验证 Specification 迁移方法的通用性 |
| **数据** | 8 白盒 + 3 黑盒 | 4 域 × Split A/B |
| **模型** | GLM5.1 为主 | GPT + GLM + Qwen 多模型 |
| **评价** | good/bad 二分类准确率 | α, κ, r, Transfer Gap |
| **范围** | 仅医疗 | 医疗 + 语音助手 + 2 开源 |

**共用**:
- 医疗域的标注规范、prompt 模板、人工标注数据
- Opus 驱动的 rubric 设计迭代方法
- 诊断-修复算法的核心实现

**论文独有**:
- Split A/B 迁移实验设计
- 多域验证
- 多模型鲁棒性实验
- 形式化的 Specification schema
