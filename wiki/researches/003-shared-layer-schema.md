# 共享层 Schema 设计

> 日期: 2026-04-23
> 状态: 初版（以医疗域为原型实例化）
> 前置文档: [001-研究选题](001-specification-driven-judge-proposal.md), [002-路线图](002-implementation-roadmap.md)

---

## 1. 设计原则

### 1.1 从现有 prompt 中提取的洞察

分析 HealRub 项目已有的 4 个维度 prompt（`project/raw-data/prompts/system_*.md`），发现**现有 prompt 已经隐式实现了两层架构**：

| 现有 prompt 中的组件 | 对应的 Schema 层 | 形式化方式 |
|---------------------|----------------|-----------|
| 维度定义（"解决方案回答这一个问题：…"） | 共享层 / dimensions | 结构化 dimension 定义 |
| 子维度评分判据表（分→判定原则） | 共享层 / quality_anchors | 每个分档的 anchor 描述 |
| "必须避免的 hack 思路"列表 | 共享层 / anti_patterns | 明确的禁止规则 |
| case rubric 的角色说明 | 架构层 / 两层交互协议 | 明确实例层与共享层的关系 |
| 输出格式（JSON schema） | 共享层 / output_format | 标准化输出定义 |
| 自检清单 | 共享层 / self_check | 验证规则列表 |
| **case 专属 rubric** | **实例层** | 自动生成的 case-specific criteria |

### 1.2 Schema 设计目标

1. **机器可读**: LLM 能解析并基于此生成 prompt / rubric
2. **人可审查**: 评测专家能阅读和修改
3. **可 diff**: 每轮精磨的变更可以被追踪
4. **跨域通用**: 医疗、语音助手、法律、通用对话都能用同一 schema 表达
5. **分层明确**: 共享层和实例层的边界清晰，迁移时共享层不动

---

## 2. Schema 定义（YAML）

```yaml
# ============================================================
# Evaluation Specification — 顶层结构
# ============================================================
specification:
  metadata:
    name: string                    # 规范名称
    domain: string                  # 评测领域（medical / voice_assistant / legal / general）
    version: string                 # 语义版本号
    created: date
    last_refined: date
    refinement_rounds: int          # Phase 1 精磨轮次
    refinement_history:             # 变更记录
      - round: int
        date: date
        changes_summary: string
        alpha_before: float
        alpha_after: float

  # ============================================================
  # SHARED LAYER — 迁移时直接复用
  # ============================================================
  shared_layer:

    # ------ 1. 维度定义 ------
    dimensions:
      - id: string                  # 如 "solution", "questioning"
        name: string                # 显示名，如 "解决方案"
        description: string         # 一句话定义（"回答这一个问题：…"）
        weight: float               # 维度权重（用于 case 级聚合）
        
        sub_dimensions:
          - id: string              # 如 "accuracy", "relevance"
            name: string
            description: string     # 子维度衡量什么
            score_range: [int, int] # 如 [-2, 3]
            
        aggregation: string         # 子维度 → 维度分的聚合方式: "min" | "avg" | "weighted_sum"
        
        scope_boundary: string      # 评测范围边界（如 "只看小结前的追问轮次"）
        
    # ------ 2. 质量锚点 ------
    quality_anchors:
      - dimension_id: string
        sub_dimension_id: string
        level: int                  # 分档值
        criterion: string           # 判定原则（binary-observable 表述）
        positive_signals: [string]  # 给此分的典型正面信号
        negative_signals: [string]  # 不应给此分的典型反面信号
        boundary_notes: string      # 与相邻分档的区分说明

    # ------ 3. 禁止模式（Anti-patterns） ------
    anti_patterns:
      - id: string
        description: string         # 被禁止的判分依据
        affected_dimensions: [string]  # 哪些维度受影响
        example: string             # 典型错误示例
        # 例: "❌ 用字数/token数来判断丰富全面性"

    # ------ 4. 错误模式库（从 Split A 精磨中归纳） ------
    error_patterns:
      - id: string
        type: string                # AMBIGUOUS | MISSING | GAP | WEIGHT | EDGE
        description: string         # 典型不一致模式
        affected_dimensions: [string]
        diagnostic_signal: string   # 如何识别这种不一致
        correction_applied: string  # 在 Specification 中做了什么修正
        discovered_in_round: int    # 哪轮精磨发现的
        example_cases: [string]     # 触发此模式的典型 case ID

    # ------ 5. 评分逻辑 ------
    scoring_logic:
      # 维度 → Case 级总分
      case_aggregation:
        method: string              # "min" | "weighted_avg" | "custom"
        formula: string             # 如 "min(solution, style)" 或自定义公式
        special_rules:
          - name: string            # 如 "veto", "min_normalized", "offset"
            condition: string       # 触发条件
            effect: string          # 效果描述
      
      # 分数标准化（可选）
      normalization:
        method: string              # "clip" | "min_max" | "none"
        range: [float, float]       # 如 [0, 1]

    # ------ 6. 评测范围规则 ------
    scope_rules:
      - dimension_id: string
        rule: string                # 如 "只评医生侧的内容，不评患者"
      
    # ------ 7. 两层交互协议 ------
    instance_protocol:
      description: string           # "case rubric 是对通用判据的领域细化"
      priority_rule: string         # "case rubric 与 system prompt 矛盾时，以 case rubric 为准"
      instance_must_contain:        # 实例层必须包含的要素
        - string                    # 如 "该 case 主诊断是什么"
        - string                    # 如 "该 case 必须命中的关键判断方向"
        - string                    # 如 "该 case 的典型扣分模式"
        - string                    # 如 "该 case 的临界情形及分档归属"

    # ------ 8. 输出格式 ------
    output_format:
      type: string                  # "json"
      schema: object                # JSON schema 定义
      constraints:
        - string                    # 如 "理由不超过 80 字"
        - string                    # 如 "不要输出 session 分"

    # ------ 9. 自检清单 ------
    self_check:
      - check: string              # 如 "分数是否在允许范围内"
      - check: string              # 如 "理由是否引用了被禁特征"

    # ------ 10. Prompt 模板 ------
    prompt_templates:
      judge_system_template: string # system prompt 模板（含占位符）
      judge_user_template: string   # user prompt 模板
      instance_generation_template: string  # 生成 case rubric 的模板

  # ============================================================
  # INSTANCE LAYER — 每个 case 自动生成，迁移时重新生成
  # ============================================================
  instance_layer:
    generation_model: string        # 生成实例层的模型（如 "opus"）
    generation_prompt: string       # 引用 prompt_templates.instance_generation_template
    
    cases:
      - case_id: string
        case_summary: string        # case 摘要（由生成模型提取）
        
        rubric:                     # case-specific rubric
          - criterion_id: string
            dimension_id: string
            sub_dimension_id: string
            description: string     # binary-observable 表述
            polarity: string        # "positive" | "negative"
            anchor_level: int       # 对应的质量锚点分档
            boundary_notes: string  # 边界条件
        
        clinical_context:           # （医疗域特有，其他域可自定义）
          primary_diagnosis: string
          key_findings: [string]
          risk_level: string
```

---

## 3. 医疗域实例化

### 3.1 维度结构

基于现有 4 个 prompt，医疗域的维度结构如下：

```yaml
dimensions:
  - id: solution
    name: "解决方案"
    description: >
      医生针对患者的主诉，给出的判断/检查/处置建议
      在临床上对不对、全不全、跟不跟得上对话里出现过的事实、
      有没有犯重复或遗忘错误。
    weight: 0.35
    sub_dimensions:
      - id: accuracy
        name: "准确性"
        description: "医生说的医学事实/推理/处置在临床上对不对"
        score_range: [-2, 3]
      - id: relevance
        name: "相关性"
        description: "医生的回答和患者真正想问的事对不对得上"
        score_range: [0, 3]
      - id: completeness
        name: "丰富全面性"
        description: "医生给的内容够不够撑起一份对患者真正可执行的建议"
        score_range: [1, 3]
      - id: memory
        name: "记忆和重复"
        description: "多轮对话中有没有记住前文、有没有重复、有没有杜撰"
        score_range: [-2, 3]
    aggregation: min
    scope_boundary: "评价范围为医生的全部回复内容"

  - id: questioning
    name: "对话引导（反问）"
    description: >
      医生在给出最终小结/诊断之前的问诊追问阶段，
      问的问题够不够好——有没有问到该问的、有没有问了不该问的、
      有没有重复问已答的。
    weight: 0.25
    sub_dimensions:
      - id: necessity
        name: "必要性"
        description: "问的问题是不是真正需要问的"
        score_range: [0, 3]
      - id: relevance
        name: "相关性"
        description: "每个问题是否与核心诊疗路径相关"
        score_range: [-2, 3]
      - id: memory
        name: "记忆和重复"
        description: "反问阶段有没有重复追问已答问题"
        score_range: [-1, 3]
    aggregation: min
    scope_boundary: "只看小结（give_diagnosis_and_summary）之前的医生追问轮次"

  - id: dispatch
    name: "服务调度"
    description: >
      医生有没有在合适的时机、给出正确方向的就医/检查/分流建议。
    weight: 0.15
    sub_dimensions:
      - id: recall
        name: "召回"
        description: "有没有在合适时机把关键就医/检查路径告诉患者"
        score_range: [0, 3]
      - id: precision
        name: "准确"
        description: "调度方向/类型是否正确，优先级是否合理"
        score_range: [-2, 3]
    aggregation: min
    scope_boundary: "整段对话（含反问和小结）"

  - id: style
    name: "表达风格"
    description: >
      医生的回答在表达层面是否易懂、简洁、有条理、有温度、符合产品人设。
    weight: 0.25
    sub_dimensions:
      - id: readability
        name: "易懂性"
        description: "对患者来说是否容易理解"
        score_range: [0, 3]
      - id: formatting
        name: "展现/排版"
        description: "视觉结构是否清晰"
        score_range: [0, 3]
      - id: conciseness
        name: "简洁性"
        description: "表达是否精练、不啰嗦"
        score_range: [0, 3]
      - id: empathy
        name: "情感关怀"
        description: "是否展现了适当的共情和关怀"
        score_range: [-2, 3]
      - id: persona
        name: "产品人设"
        description: "是否符合线上医患咨询的产品人设"
        score_range: [1, 3]
    aggregation: min
    scope_boundary: "评价范围为医生的全部回复内容"
```

### 3.2 核心 Anti-patterns（从现有 prompt 提取）

```yaml
anti_patterns:
  # === 跨维度通用 ===
  - id: ap_word_count
    description: "用字数/token数/段落数作为评分依据"
    affected_dimensions: [solution, questioning, dispatch, style]
    example: "'超过 500 字就扣分' — 看的是信息密度不是绝对长度"

  - id: ap_turn_count
    description: "用反问轮次数作为评分依据"
    affected_dimensions: [questioning, dispatch]
    example: "'反问了 5 轮所以效率低' — 看的是内容不是数量"

  - id: ap_model_brand
    description: "基于模型名/品牌偏好打分"
    affected_dimensions: [solution, questioning, dispatch, style]
    example: "'这是GPT-4的回答所以应该更好'"

  - id: ap_formatting_artifacts
    description: "基于系统级标记（如 tool_call）打分"
    affected_dimensions: [style]
    example: "tool_call 标签是系统标记，不是医生回答的排版"

  # === 跨维度污染 ===
  - id: ap_cross_dim_solution
    description: "把非解决方案维度的问题扣到解决方案上"
    affected_dimensions: [solution]
    example: "反问差或排版差，不应扣到解决方案"

  - id: ap_cross_dim_style
    description: "把内容正确性问题扣到表达风格上"
    affected_dimensions: [style]
    example: "解决方案方向错误不应扣表达风格"

  - id: ap_cross_dim_questioning
    description: "把解决方案或表达问题扣到反问上"
    affected_dimensions: [questioning]
    example: "解决方案的缺陷不应扣到反问维度"
```

### 3.3 评分逻辑

```yaml
scoring_logic:
  case_aggregation:
    method: custom
    formula: "min(solution, style)"
    notes: >
      来源：2026-04 对齐会议共识（diffcase对齐记录.md 0407 条目 52）。
      原始公式为 MIN(解决方案, 对话引导+1, 服务调度, 表达风格)，
      简化为 min(解决方案, 表达风格)，理由：Session 评分以解决方案为主、
      表达风格为辅，其他维度用于过程评估与问题定位。
    special_rules:
      - name: questioning_offset
        condition: "对话引导在包含解决方案的轮次中出现"
        effect: "对话引导分 +1 后参与计算"
      - name: solution_zero_floor
        condition: "解决方案 = 0 且其他维度 ≥ 2"
        effect: "session 分调整为 1（避免过度惩罚）"
  
  normalization:
    method: none  # 保持原始分值 [-2, 3]
    # 论文中报告 case 级指标时，可标准化到 [0, 1]
```

### 3.4 两层交互协议

```yaml
instance_protocol:
  description: >
    case rubric 是对共享层通用判据的「领域细化」。
    它不改变维度定义、子维度结构或评分范围，
    只提供该 case 特有的临床要点和判定锚点。
  priority_rule: >
    case rubric 与 system prompt 出现明显矛盾时，以 case rubric 为准
    （case rubric 是临床细化）。如果矛盾无法调和，按 system prompt 的
    子维度通用判据评。
  instance_must_contain:
    - "该 case 主诊断是什么"
    - "该 case 必须命中的关键判断方向 / 检查 / 处置"
    - "该 case 中典型的扣分模式"
    - "该 case 中可能出现的临界情形及其分档归属"
```

---

## 4. 跨域通用性分析

### 4.1 不同域的维度差异

| 域 | 维度（预估） | 特殊评分逻辑 | 实例层的"临床要点"替代物 |
|----|------------|------------|----------------------|
| 医疗 | solution / questioning / dispatch / style | min 聚合, questioning+1 offset | 主诊断、关键判断方向、扣分模式 |
| 392 语音助手 | 正向策略 / 负向策略 | 三值评分 (-1/0/1) | query 意图、情感陪伴场景要求 |
| PRBench（法律） | 多条正/负 criteria | 加权裁剪分, mean_clipped | 法律条款引用、正确法律推理步骤 |
| HealthBench（医疗EN） | 多条正/负 criteria | 正分-负分, clip [0,1] | 医学准确性、安全性、表达 |

### 4.2 Schema 的适应能力

| 需求 | Schema 支持方式 |
|------|---------------|
| 不同的维度数量 | `dimensions` 列表长度自由 |
| 不同的分值范围 | 每个 `sub_dimension.score_range` 独立定义 |
| 不同的聚合方式 | `aggregation` 支持 min/avg/weighted_sum |
| 正/负 criteria | `instance_layer.rubric[].polarity` = positive/negative |
| Veto / 一票否决 | `scoring_logic.special_rules` |
| 三值评分 | `score_range: [-1, 1]` |
| 加权裁剪分 | `scoring_logic.normalization.method: clip` |

### 4.3 需要为 392 域调整的部分

392 语音助手的 rubric 结构是 **strategy1（负向）+ strategy2（正向）**，每条 query 配 2 条策略，三值评分。这映射到 schema 中：

```yaml
# 392 语音助手的维度定义（草案）
dimensions:
  - id: negative_strategy
    name: "负向策略"
    description: "回答中不应出现的问题或错误"
    weight: 0.5
    sub_dimensions:
      - id: strategy1
        name: "策略1-负向"
        description: "case-specific 的负向检查项"
        score_range: [-1, 1]  # -1=违反, 0=不适用, 1=未违反
    aggregation: min  # 单子维度

  - id: positive_strategy
    name: "正向策略"
    description: "回答中应该具备的质量特征"
    weight: 0.5
    sub_dimensions:
      - id: strategy2
        name: "策略2-正向"
        description: "case-specific 的正向检查项"
        score_range: [-1, 1]  # -1=缺失, 0=不适用, 1=具备
    aggregation: min
```

---

## 5. 迁移算法与 Schema 的关系

### 5.1 精磨（Phase 1）修改的 Schema 区域

| 不一致类型 | 修改的 Schema 部分 | 示例 |
|-----------|------------------|------|
| TYPE_AMBIGUOUS | `quality_anchors[].criterion` | "附属信息不严谨" → "某句结论的措辞偏绝对、对某个体征的临床意义解读偏简化" |
| TYPE_MISSING | `quality_anchors[].positive_signals` / `negative_signals` | 补充 "需求响应不全是 2 分的典型情景" |
| TYPE_GAP | `dimensions[].sub_dimensions` 新增 | 发现需要增加"风险提示"子维度 |
| TYPE_WEIGHT | `dimensions[].weight` | 调整 solution 和 style 的相对权重 |
| TYPE_EDGE | `quality_anchors[].boundary_notes` | "同一处置建议在多轮反复出现 → 1分，不是0分" |

### 5.2 迁移（Phase 2）保留与重新生成的部分

```
┌─────────────────────────────────────────┐
│  直接复用（Zero-shot Transfer）           │
│  ✓ dimensions 定义                       │
│  ✓ quality_anchors                       │
│  ✓ anti_patterns                         │
│  ✓ error_patterns                        │
│  ✓ scoring_logic                         │
│  ✓ output_format                         │
│  ✓ self_check                            │
│  ✓ prompt_templates                      │
│  ✓ instance_protocol                     │
└─────────────────┬───────────────────────┘
                  │ Opus 自动生成
                  ▼
┌─────────────────────────────────────────┐
│  重新生成（Per-case Generation）          │
│  ↻ instance_layer.cases[*].rubric       │
│  ↻ instance_layer.cases[*].case_summary │
│  ↻ instance_layer.cases[*].clinical_context │
└─────────────────┬───────────────────────┘
                  │ 50 case 校准
                  ▼
┌─────────────────────────────────────────┐
│  微调（Calibration, ≤50 cases）          │
│  ≈ scoring_logic.case_aggregation.formula │  # 可能微调权重
│  ≈ quality_anchors 的阈值表述            │  # 可能微调锚点
│  ✗ 不动 dimensions / anti_patterns 等核心结构 │
└─────────────────────────────────────────┘
```

---

## 6. 下一步

1. **实现 YAML → Prompt 渲染器**: 将 schema 自动渲染为 LLM-as-Judge 的 system prompt
2. **实现 Prompt → Schema 反向提取**: 将现有 4 个医疗 prompt 反向解析为 YAML（验证 schema 完备性）
3. **实现实例层生成器**: 给定共享层 + case 内容 → 自动生成 case rubric
4. **在医疗域 Split A 上运行第一轮精磨**: 验证整个流程可行性
