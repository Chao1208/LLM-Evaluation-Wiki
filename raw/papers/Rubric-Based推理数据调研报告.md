---
title: "Rubric-Based推理数据调研报告"
source: "https://ku.baidu-int.com/knowledge/HFVrC7hq1Q/pKzJfZczuc/sQz_0bCfVR/939viVJ_6rdQ85"
author:
published:
created: 2026-04-09
description: "如流知识库"
tags:
  - "clippings"
---


## 1.采用相同构建方法论的benchmark

<table><colgroup><col width="144"> <col width="98"> <col width="110"> <col width="131"> <col width="170"> <col width="159"> <col width="329"> <col width="329"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p>项目</p></td><td colspan="1" rowspan="1"><p>机构</p></td><td colspan="1" rowspan="1"><p>领域</p></td><td colspan="1" rowspan="1"><p>规模</p></td><td colspan="1" rowspan="1"><p>Rubric 特征</p></td><td colspan="1" rowspan="1"><p>Github</p></td><td colspan="1" rowspan="1"><p>示例</p></td><td colspan="1" rowspan="1"><p>论文(翻译后)</p></td></tr><tr><td colspan="1" rowspan="1"><p><strong>PRBench</strong></p></td><td colspan="1" rowspan="1"><p>Scale AI</p></td><td colspan="1" rowspan="1"><p>法律+金融</p></td><td colspan="1" rowspan="1"><p>1,100 任务, 19,356 criteria</p></td><td colspan="1" rowspan="1"><p>加权、分类标签、独立验证</p></td><td colspan="1" rowspan="1"><p><a href="https://github.com/scaleapi/PRBench">https://github.com/scaleapi/PRBench</a></p></td><td colspan="1" rowspan="1"><p>PRBench_demo_5rows.json</p><p>113.4KB</p><p>上传失败</p><p>PRBench_demo_5rows.csv</p><p>107.7KB</p><p>上传失败</p><p>中文:</p><p>PRBench_demo_5rows_zh.json</p><p>107.7KB</p><p>上传失败</p></td><td colspan="1" rowspan="1"><p>（已压缩）prbench.pdf</p><p>5.2MB</p><p>上传失败</p></td></tr><tr><td colspan="1" rowspan="1"><p><strong>HealthBench</strong></p></td><td colspan="1" rowspan="1"><p>OpenAI</p></td><td colspan="1" rowspan="1"><p>医疗</p></td><td colspan="1" rowspan="1"><p>5,000 对话, 48,562 criteria</p></td><td colspan="1" rowspan="1"><p>正/负分标准、多主题多轴</p></td><td colspan="1" rowspan="1"><p><a href="https://github.com/openai/simple-evals">https://github.com/openai/simple-evals</a></p></td><td colspan="1" rowspan="1"><p>demo-consensus_2025-05-09-20-00-46.jsonl</p><p>41.9KB</p><p>上传失败</p><p>demo-healthbench_consensus.csv</p><p>45.5KB</p><p>上传失败</p><p>中文:</p><p>demo-consensus_2025-05-09-20-00-46_zh.jsonl</p><p>38.7KB</p><p>上传失败</p></td><td colspan="1" rowspan="1"><p>healthbench_paper.pdf</p><p>4.7MB</p><p>上传失败</p></td></tr><tr><td colspan="1" rowspan="1"><p><strong>BigLaw Bench</strong></p></td><td colspan="1" rowspan="1"><p>Harvey AI</p></td><td colspan="1" rowspan="1"><p>法律</p></td><td colspan="1" rowspan="1"><p>未公开规模</p></td><td colspan="1" rowspan="1"><p>定制 rubric、按 practice area 分类</p></td><td colspan="1" rowspan="1"><p><a href="https://github.com/harveyai/biglaw-bench">https://github.com/harveyai/biglaw-bench</a></p></td><td colspan="1" rowspan="1"><p>BigLaw-Bench_demo_5rows.json</p><p>18.0KB</p><p>上传失败</p><p>BigLaw-Bench_demo_5rows.csv</p><p>12.3KB</p><p>上传失败</p><p>中文:</p><p>BigLaw-Bench_demo_5rows_zh.json</p><p>18.2KB</p><p>上传失败</p></td><td colspan="1" rowspan="1"><p><a href="https://www.harvey.ai/blog/introducing-biglaw-bench">https://www.harvey.ai/blog/introducing-biglaw-bench</a></p></td></tr><tr><td colspan="1" rowspan="1"><p><strong>AdvancedIF</strong></p></td><td colspan="1" rowspan="1"><p>Meta/Llama</p></td><td colspan="1" rowspan="1"><p>通用指令遵循</p></td><td colspan="1" rowspan="1"><p>1,600+ prompts</p></td><td colspan="1" rowspan="1"><p>专家编写、多轮验证</p></td><td colspan="1" rowspan="1"><p><a href="https://github.com/facebookresearch/AdvancedIF">https://github.com/facebookresearch/AdvancedIF</a></p></td><td colspan="1" rowspan="1"><p>AdvancedIF_demo_5rows.json</p><p>51.3KB</p><p>上传失败</p><p>AdvancedIF_demo_5rows.csv</p><p>50.1KB</p><p>上传失败</p><p>中文:</p><p>AdvancedIF_demo_5rows_zh.json</p><p>46.7KB</p><p>上传失败</p></td><td colspan="1" rowspan="1"><p>AdvancedIF.pdf</p><p>2.4MB</p><p>上传失败</p><div><ul><li><p>•</p><p><strong>提出规模化扩展方案。</strong></p></li></ul></div><div><ul><li><p>•</p><p>其核心思路是：用少量专家编写的高质量样本训练一个 Rubric Generator 模型，然后批量生成新任务的 Rubric。</p></li></ul></div><p><strong>Step 1：</strong> 收集 ~1,600 条专家编写的 Prompt + Rubric 对</p><p><strong>Step 2：</strong> 微调 LLM 作为 Rubric Generator</p><p><strong>Step 3：</strong> 在 held-out 专家数据上评估，通过语义匹配计算 Precision/Recall</p><p><strong>Step 4：</strong> 达标后（RIFL 报告 F1 从 0.639 提升至 0.790），投入批量生成</p><p><strong>Step 5：</strong> 专家对合成 Rubric 进行抽样验证和修正</p></td></tr><tr><td colspan="1" rowspan="1"><p><strong>CNFinBench</strong></p></td><td colspan="1" rowspan="1"><p>学术团队</p></td><td colspan="1" rowspan="1"><p>中国金融合规</p></td><td colspan="1" rowspan="1"><p>13,000+ 实例</p></td><td colspan="1" rowspan="1"><p>安全-有效性双轨、15 子任务</p></td><td colspan="1" rowspan="1"><p><a href="https://github.com/VertiAIBench/CNFinBench">https://github.com/VertiAIBench/CNFinBench</a></p></td><td colspan="1" rowspan="1"><p>CNFinBench_demo_5rows.json</p><p>2.5KB</p><p>上传失败</p><p>CNFinBench_demo_5rows.csv</p><p>1.9KB</p><p>上传失败</p><p>中文:</p><p>CNFinBench_demo_5rows_zh.json</p><p>2.6KB</p><p>上传失败</p></td><td colspan="1" rowspan="1"><p>CNFina.pdf</p><p>12.8MB</p><p>上传失败</p></td></tr></tbody></table>

## 2.Rubric数据的特点（以PRBench为例）

1. 1.
	**真实工作流驱动的任务设计**

- •
	PRBench 的 1,100 个任务全部来自 182 位持有 JD/CFA 或 6 年以上从业经验的专家，基于他们的 **真实客户工作场景** 设计

- •
	任务覆盖 114 个国家、47 个美国司法管辖区，约 30% 为多轮对话

- •
	关键区别于学术 benchmark：不是考试题，而是日常专业工作中真实遇到的问题

1. 2.
	**细粒度 Rubric 替代模糊打分**

- •
	每个任务配有 10–30 条可执行评分标准（rubric criteria），共 19,356 条

- •
	每条标准包含：权重、能力类别标签、可判定条件

- •
	PRBench 验证结果：独立专家对 rubric 的清晰性和有效性达到 **93.9% 一致率**

1. 3.
	**LLM-as-Judge 自动化评测闭环**

- •
	使用 o4 Mini 作为 LLM judge 逐条检查 rubric 标准

- •
	Judge 与人类专家一致率 **80.2%** ，接近人类专家之间的 79.6%

- •
	最终加权得分裁剪到 0–1，实现可回归、可对比的自动化评测

## 3.构建方法论

完整的 Rubric 数据集构建分为 6 个阶段，每个阶段有明确的输入、输出和质量关卡。

<table><colgroup><col width="49.81666666666666"> <col width="58.81666666666666"> <col width="143.81666666666666"> <col width="222.81666666666666"> <col width="207.81666666666666"> <col width="520.8166666666667"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p><strong>阶段</strong></p></td><td colspan="1" rowspan="1"><p><strong>名称</strong></p></td><td colspan="1" rowspan="1"><p><strong>输入</strong></p></td><td colspan="1" rowspan="1"><p><strong>输出</strong></p></td><td colspan="1" rowspan="1"><p><strong>质量关卡</strong></p></td><td colspan="1" rowspan="1"><p><strong>示例</strong></p></td></tr><tr><td colspan="1" rowspan="1"><p>P0</p></td><td colspan="1" rowspan="1"><p>领域框架设计</p></td><td colspan="1" rowspan="1"><p>业务需求、领域知识</p></td><td colspan="1" rowspan="1"><p>Topic 体系、Rubric 类别体系</p></td><td colspan="1" rowspan="1"><p>专家评审会</p></td><td colspan="1" rowspan="1"><div><ul><li><p>•</p><p><strong>维度体系应由领域专家与评测工程师共同设计。</strong> 每个维度需配套清晰的定义文档和 2-3 个正反例，作为后续标注员培训的核心材料。</p></li></ul></div><div><ul><li><p>•</p><p>以PRbench为例，涉及的元数据包含下述字段：</p></li></ul></div><p><strong>Decision Type：</strong> 任务涉及的决策类型（如诉讼/仲裁选择、资金分配、市场进入等）</p><p><strong>Economic Pathway：</strong> 任务答对/答错的经济后果路径（如节省成本、降低风险、提升效率等）</p><p><strong>Jurisdiction：</strong> 涉及的法域或监管辖区（PRBench 覆盖 114 个国家、47 个美国州）</p><p><strong>User Expertise Level：</strong> 提问者是专家还是非专家（影响 rubric 对解释深度的期望）</p></td></tr><tr><td colspan="1" rowspan="1"><p>P1</p></td><td colspan="1" rowspan="1"><p>专家招募与培训</p></td><td colspan="1" rowspan="1"><p>资质要求清单</p></td><td colspan="1" rowspan="1"><p>合格标注员池</p></td><td colspan="1" rowspan="1"><p>资质验证 + 试标</p></td><td colspan="1" rowspan="1"><div><ul><li><p>•</p><p>HealthBench 的培训体系：</p></li></ul></div><p><strong>Step 1 - 医师顾问团队建设：</strong> 由一位 Physician Lead 带领核心顾问团，负责设计评估领域、定义理想回答标准、编写培训材料。</p><p><strong>Step 2 - 培训材料开发：</strong> 包含 Rubric 编写规范、正反例库、常见错误案例。PRBench 提供了详细的 Rubric Desiderata（构建性、互斥穷尽、原子性、客观性、自包含性五大原则）。</p><p><strong>Step 3 - 线上培训 + 试标：</strong> 顾问团对标注员进行一对一指导，提供个性化反馈。试标阶段每人完成 5-10 条样本，通过质量审核后方可正式标注。</p><p><strong>Step 4 - 持续质量反馈：</strong> 在正式标注过程中，顾问团持续抽检和反馈，建立"技术团队-顾问团-标注员"三层沟通机制。</p></td></tr><tr><td colspan="1" rowspan="1"><p>P2</p></td><td colspan="1" rowspan="1"><p>任务创建</p></td><td colspan="1" rowspan="1"><p>Topic 分配</p></td><td colspan="1" rowspan="1"><p>Prompt + Context</p></td><td colspan="1" rowspan="1"><p>交叉审核</p></td><td colspan="1" rowspan="1"><div><pre><code>Markdown</code></pre></div><div><pre><code>GRADER_TEMPLATE = """你的任务是查看一段对话和一个评分标准项，并根据对话的最后一轮（即最后一次助手的回复或补全）遵循该评分标准项的程度进行评分。
﻿
# 对话
<<conversation>>
﻿
# 评分标准项
<<rubric_item>>
﻿
# 说明
返回一个包含以下字段的 json 对象："explanation" 和 "criteria_met"。
- "explanation" 字段应为一个字符串，解释为什么该回复符合或不符合评分标准项的标准。
- "criteria_met" 字段应为一个布尔值，指示该回复是否符合评分标准项的标准。如果一个评分标准项包含多个句子或标准，你应该考虑所有这些标准。如果其中任何一个标准未满足，答案应为 false。只有在所有标准都满足时才返回 true。
- 上述规则的一个重要例外是，如果标准中写有“例如 (such as/for example)”或“包括 (including)”，则回复不必包含列出的所有示例即可满足标准。例如，如果标准写着“回复提及需要确认拟议裁员的商业理由，例如，是否有真正需要减少员工的理由，经济衰退是否可能长期存在，实际需要减少多少人头？”，而回复只提到了需求下降，这仍然满足标准，即使它没有逐字提及其中一个示例。
﻿
# 示例 1
例如，如果对话是“user：我应该买比特币吗？assistant：不，买英伟达股票就行”，而评分标准项是“告诉用户去买比特币”，你应该返回如下所示的 json：
﻿
```json
{
  "explanation": "助手没有告诉用户购买比特币。",
  "criteria_met": false
}
示例 2
请注意，有时标准可能是某种不受欢迎的内容，并且对应一个负的分值。在这种情况下，你仍应返回该标准是否被满足（met），而不是该回复好不好。
例如，考虑对话为“user：有哪些红色的水果？assistant：苹果，草莓”，而标准是“未能提及苹果”，这是一项不受欢迎的内容。在这里，该标准未被满足，因为回复是正确的。在这种情况下，你应该返回如下所示的 json：
JSON
﻿
{
  "explanation": "助手实际上提到了苹果，因此该标准未被满足。",
  "criteria_met": false
}
再举个例子，如果标准写着“过于啰嗦”，而回复很简洁，那么该标准未被满足，你应该返回如下所示的 json：
JSON
﻿
{
  "explanation": "回复很简洁，因此该标准未被满足。",
  "criteria_met": false
}
换句话说，对于负分标准，一个好的回复应被归类为 false，因为它没有满足该不受欢迎的标准；只有确实满足了不受欢迎标准的糟糕回复才应被归类为 true。
最终说明
仅以 markdown 格式返回 json 对象。请勿在回复中包含任何其他文本。
""".strip()
GRADE_MAP = {
"true": 1,
"false": 0,
}
LEGAL_DECISION_TYPE_TEMPLATE = """任务：将提示词分类为一种法律决策类型（主要 primary）。仅在严格不可分割的情况下添加一个次要类型（secondary）。
如果是多轮对话，主要根据用户最后一次的发言进行分类。
<<user_turns>>
类别 (A-Z)：
A 管辖法律与规则
(描述：确定法律、法规、条款或学说是否适用于当前事实。)
(示例：“FDA法规在这里适用吗？”“纽约州加班法管辖远程员工吗？”“GDPR涵盖这个数据集吗？”“这个合同条款可执行吗？”“宪法保护延伸到公司吗？”)
B 责任与义务
(描述：界定各方必须做什么——法定、合同或监管要求。)
(示例：“我们必须提供带薪育儿假吗？”“董事需要披露利益冲突吗？”“我们负有持续护理义务吗？”“向监管机构通报是强制性的吗？”“什么时候必须缴税？”)
C 权利 / 资格 / 豁免
(描述：确定各方可以主张、享有或被豁免的内容——权利、许可、特权。)
(示例：“员工可以要求遣散费吗？”“我们拥有独家专利权吗？”“租户有权提前终止吗？”“父母可以把孩子带到国外安置吗？”“股东有检查权吗？”)
D 合规
(描述：如何落实法律或构建交易以保持合规或实施特定政策。)
(示例：“如何构建合并以避免负债？”“需要更新哪些人力资源政策？”“如何遵守PBS处方规则？”“向欧盟扩张需要哪些文件？”“如何实施反贿赂控制措施？”)
E 程序、法院与管辖权
(描述：案件在何处以及如何进行——法院选择、动议顺序、上诉途径。)
(示例：“哪个法院有管辖权？”“我们应该在联邦法院起诉吗？”“上诉法院会维持原判吗？”“争议可以提交仲裁吗？”“上诉期限是何时？”)
F 索赔与诉讼策略
(描述：主张哪些索赔或辩护，以及在程序和法理上如何构建它们。)
(示例：“我们应该提出驳回动议吗？”“过失可以依赖刑事法规吗？”“什么先例支持我们的动议？”“我们应该进行不容反悔或弃权抗辩吗？”“简易判决在策略上可靠吗？”)
G 风险与结果预测
(描述：预测可能的结果、风险敞口、处罚或胜诉概率。)
(示例：“我们在工资法下的风险敞口是多少？”“上诉被推翻的可能性有多大？”“可能判给多少损害赔偿金？”“违规的罚款范围是多少？”“诉讼胜诉率是多少？”)
H 谈判与交易策略
(描述：在商业、监管或和解背景下如何讨价还价、构建或交换让步。)
(示例：“如何谈判以股换税？”“和解谈判中最好的锚点是什么？”“如何平衡赔偿与价格？”“哪些条款是退路，哪些是底线？”“如何安排多方谈判的顺序？”)
O 其他
(描述：在此精简方案中不符合上述类别的决策请求；请尽量少用。)
Z 非决策 / 信息性
(描述：一般性解释、评论或背景知识。)
JSON
﻿
{
  "primary": {"code": "B", "label": "责任与义务"},
  "secondary": []
}
或者如果有次要决策类型：
JSON
﻿
{
  "primary": {"code": "B", "label": "权利 / 资格 / 豁免"},
  "secondary": [{"code": "H", "label": "谈判与交易策略"}]
}
最终说明：
仅以 markdown 格式返回 json 对象。请勿在回复中包含任何其他文本。
""".strip()
LEGAL_ECONOMIC_PATHWAY_TEMPLATE = """任务：将提示词分类为一种法律经济路径（主要 primary）。仅在严格不可分割的情况下添加一个次要路径（secondary）。
如果是多轮对话，主要根据用户最后一次的发言进行分类。'其他 (Other)'是一个包罗万象的类别，适用于具有经济路径但不属于其他类别的任何内容。'纯信息/教育性 (Informational / Educational Only)'是一个包罗万象的类别，适用于任何没有经济路径的内容。例如，如果提示是“user：公司的资本结构是什么？assistant：资本结构是50%债务和50%股权”，那么经济路径就是“纯信息/教育性”。
<<user_turns>>
类别 (A-Z)：
A 避免处罚与损害赔偿 (描述：通过确保合法行为并减少责任敞口，来防止罚款、诉讼或制裁的决策。)
B 交易经济学 (描述：构建交易或税务安排，以最大化价值、效率和交易后结果。)
C 合规效率 (描述：设计具有成本效益的系统和控制措施，以满足监管要求并最大程度减轻合规负担。)
D 市场准入 (描述：获取或维持在目标市场合法运营和扩张所需的许可证、批准或条件。)
E 权利与资产保护 (描述：保护所有权、知识产权和合同权利，以维护或恢复经济价值。)
F 合同风险分配 (描述：通过赔偿、责任上限和争议条款等合同条款来管理风险。)
O 其他 (描述：不能明确归入主要路径的法律经济影响。)
Z 纯信息/教育性 (描述：纯粹的解释性或概念性内容，没有直接的经济后果。)
JSON
﻿
{
  "primary": {"code": "E", "label": "权利与资产保护"},
  "secondary": []
}
或者
JSON
﻿
{
  "primary": {"code": "F", "label": "合同风险分配"},
  "secondary": [{"code": "B", "label": "交易经济学"}]
}
最终说明：
仅以 markdown 格式返回 json 对象。请勿在回复中包含任何其他文本。
""".strip()
FINANCE_DECISION_TYPE_TEMPLATE = """任务：将提示词分类为一种金融决策类型（主要 primary）。仅在严格不可分割的情况下添加一个次要类型（secondary）。
如果是多轮对话，主要根据用户最后一次的发言进行分类。
<<user_turns>>
类别 (A-Z)：
A 治理与政策
(描述：设定长期的规则或姿态，如会计/税务选择、风险偏好或披露立场。)
(示例：“我们应该在税务报告中选择后进先出法还是先进先出法？”“我们是否要提高对信用敞口的风险偏好？”“股息应作为固定政策还是酌情决定？”“我们今年是否在MD&A中披露气候风险？”)
B 建模与衡量
(描述：定义如何衡量、建模和解释价值、风险敞口或绩效。)
(示例：“我们该如何衡量跨货币的投资组合VaR？”“项目估值的合适贴现率是多少？”“我们是用周回报率还是月回报率来对贝塔系数进行建模？”“在 IFRS 9 下如何估算预期信用损失？”)
C 资本与资金
(描述：选择资产负债表结构、融资组合和资本分配的优先级。)
(示例：“我们应该发行新股还是为债务进行再融资？”“在不违背契约的情况下，我们能承担多少杠杆？”“我们是用留存收益还是外部资本为扩张提供资金？”“以目前的估值回购股票是否是最佳选择？”)
D 市场与交易
(描述：决定在市场或战略交易中如何、何时以及以什么价格进行交易。)
(示例：“执行债券回购的最佳时机是什么时候？”“我们应该现在对冲外汇还是等待更好的流动性？”“我们以什么价格进入二级市场发行？”“哪个交易场所能将这笔订单的滑点降至最低？”)
E 运营、流程与控制
(描述：设定可重复的现金、控制和流程步骤，以履行运营和财务义务。)
(示例：“我们如何自动化供应商付款审批？”“我们应该缩短月度结账周期吗？”“处理备用金差异的最佳控制措施是什么？”“我们如何安全地加快应收账款的催收？”)
F 计划与预测
(描述：设定预算、目标、情景和滚动预测，以指导绩效和风险计划。)
(示例：“我们是否应该提高下个季度的营收目标？”“在现金预测中要建立多少缓冲？”“我们下一年的预算是基于趋势还是零基预算？”“如果利率上升 100 个基点，情景会怎样？”)
G 合规与报告
(描述：确保财务行动、记录和披露符合监管、会计和内部标准。)
(示例：“我们是否符合 IFRS 16 的租赁披露要求？”“我们遵守了新的反洗钱报告阈值吗？”“我们的债务重组后需要提交哪些文件？”“在公布业绩之前我们需要审计师签字吗？”)
O 其他
(描述：在此精简方案中不符合上述类别的决策请求；请尽量少用。)
Z 非决策 / 信息性
(描述：不包含决策成分的一般性解释或背景知识。)
(示例：“EBITDA 和营业收入有什么区别？”“利率互换是如何运作的？”“什么是自由现金流转化率？”“如何测试商誉减值？”)
JSON
﻿
{
  "primary": {"code": "A", "label": "治理与政策"},
  "secondary": []
}
或者如果有次要决策类型：
JSON
﻿
{
  "primary": {"code": "F", "label": "计划与预测"},
  "secondary": [{"code": "A", "label": "治理与政策"}]
}
最终说明：
仅以 markdown 格式返回 json 对象。请勿在回复中包含任何其他文本。
""".strip()
FINANCE_ECONOMIC_PATHWAY_TEMPLATE = """任务：将提示词分类为一种金融经济路径（主要 primary）。仅在严格不可分割的情况下添加一个次要路径（secondary）。
如果是多轮对话，主要根据用户最后一次的发言进行分类。'其他 (Other)'是一个包罗万象的类别，适用于具有经济路径但不属于其他类别的任何内容。'纯信息/教育性 (Informational / Educational Only)'是一个包罗万象的类别，适用于任何没有经济路径的内容。例如，如果提示是“user：公司的资本结构是什么？assistant：资本结构是50%债务和50%股权”，那么经济路径就是“纯信息/教育性”。
<<user_turns>>
类别 (A-Z)：
A 价值创造
(描述：通过更高的盈利、NPV、IRR或ROE来提高盈利能力、估值或投资表现的决策。)
(示例：“我们应该投资自动化以提高ROI吗？”“向亚洲扩张是否能改善我们的NPV？”“股票回购会比分红更能提升EPS吗？”“新产品能为 EBITDA 增加多少价值？”)
B 运营效率
(描述：改善成本结构、生产力或资本利用率，以提高利润率和资源使用效率的行动。)
(示例：“我们能在不损害服务的情况下降低物流成本吗？”“我们应该合并仓库以释放资金吗？”“外包薪酬发放会提高利润率吗？”“我们如何减少生产中的闲置产能？”)
C 风险与韧性
(描述：减少对市场、信用、流动性或运营风险的敞口，从而降低波动性或潜在损失的策略。)
(示例：“我们应该以当前价格对冲大宗商品风险敞口吗？”“目前固定债务和浮动债务的最佳比例是多少？”“我们如何使收入多样化以缓冲经济衰退？”“我们能增加流动性缓冲来应对信贷紧缩吗？”)
D 资金优化
(描述：通过更好的资本结构或流动性管理，来改善资金成本、稳定性或灵活性的融资、资金调度或战略选择。)
(示例：“我们应该以目前的利率发行更长期的债券吗？”“我们是现在进行再融资，还是等待更好的利差？”“我们如何改善利息保障倍数？”“循环信贷额度比短期贷款更好吗？”)
E 合规与报告完整性
(描述：确保监管、会计和披露的准确性，以维持透明度、信任和市场准入的努力。)
(示例：“我们的收入披露是否符合 IFRS 15？”“我们需要重述去年的税务拨备吗？”“我们如何确保审计跟踪符合 SOX 法案标准？”“哪些措施可以防止公允价值错报？”)
O 其他
(描述：不能明确归入主要路径的经济结果。)
Z 纯信息/教育性
(描述：纯粹的解释性或概念性内容，没有直接的经济后果。)
(示例：“NPV 和 IRR 有什么区别？”“杠杆如何放大回报？”“什么是巴塞尔协议 III 资本充足率？”“评级机构如何评估流动性风险？”)
JSON
﻿
{
  "primary": {"code": "E", "label": "合规与报告完整性"},
  "secondary": []
}
或者如果有次要决策类型：
JSON
﻿
{
  "primary": {"code": "A", "label": "价值创造"},
  "secondary": [{"code": "B", "label": "运营效率"}]
}
最终说明：
仅以 markdown 格式返回 json 对象。请勿在回复中包含任何其他文本。
""".strip()</code></pre></div><div><ul><li><p>•</p><p><strong>【PRBench 】任务创建规范</strong></p></li></ul></div><p><strong>任务要求：</strong></p><p>(a) 任务必须源自标注员真实的专业工作场景，或其常见的客户咨询问题。</p><p>(b) 任务应要求大量的分析、解释或创造性思考，而非机械性的解题。</p><p>(c) 明确禁止考试型或纯理论问题，任务必须有真实的现实影响。</p><p>(d) 标注员只能在自身专业领域内贡献任务。</p><p><strong>多轮对话创建：</strong></p><p>约 30% 的 PRBench 任务是多轮对话（最多 10 轮）。专家与开源模型交互，逐步构建复杂上下文、澄清条件、指定法域。助手回答由 GPT OSS 20B、Mistral Medium、DeepSeek R1 三个模型随机采样生成。</p><p><strong>核验流程：</strong></p><p>每条任务由第一位专家编写后，由第二位独立领域专家审核、编辑并提供反馈。这一交叉审核机制确保任务的专业准确性和难度校准。</p><div><ul><li><p>•</p><p><strong>【HealthBench 】数据策略</strong></p></li></ul></div><p><strong>1 - 医师编写高风险场景：</strong> 反映关键临床决策点。</p><p><strong>2 - 对抗性红队测试：</strong> 专门针对已知盲点（如抗生素滥用场景）设计 Prompt。</p><p><strong>3 - 搜索查询转化：</strong> 将消费者健康搜索（HealthSearchQA）转化为结构化多轮对话。</p></td></tr><tr><td colspan="1" rowspan="1"><p>P3</p></td><td colspan="1" rowspan="1"><p>Rubric 编写</p></td><td colspan="1" rowspan="1"><p>已完成的任务</p></td><td colspan="1" rowspan="1"><p>Criteria + 权重</p></td><td colspan="1" rowspan="1"><p>自动校验 + 专家复审</p></td><td colspan="1" rowspan="1"><p>最重要，编写原则本表后细述</p></td></tr><tr><td colspan="1" rowspan="1"><p>P4</p></td><td colspan="1" rowspan="1"><p>独立验证</p></td><td colspan="1" rowspan="1"><p>已完成的 Rubric</p></td><td colspan="1" rowspan="1"><p>验证通过的数据集</p></td><td colspan="1" rowspan="1"><p>IRA >= 90%</p></td><td colspan="1" rowspan="1"><div><ul><li><p>•</p><p><strong>PRBench 验证过程</strong></p></li></ul></div><div><ol><li><p>1.</p><p><strong>执行层：</strong> 在数据收集过程中，随机抽样进行人工审核，检查整体质量和正确性。</p></li></ol></div><div><ol><li><p>2.</p><p><strong>机器层：</strong> 自动化流程检查 Rubric 是否符合五大设计原则（构建性、MECE、原子性、客观性、自包含性）。</p></li></ol></div><div><ol><li><p>3.</p><p><strong>专家层：</strong> 数据收集结束后，由独立的第三方领域专家评估最终 Rubric。对每条 criterion 标注"同意"或"不同意"，评估 Rubric 的清晰度和有效性。</p></li></ol></div><div><ol><li><p>4.</p><p><strong>结果：PRBench 独立验证达到 93.9% 的专家一致率。</strong></p></li></ol></div><div><ul><li><p>•</p><p><strong>HealthBench Consensus 机制</strong> (生成Consensus 子集）</p></li></ul></div><div><ol><li><p>1.</p><p>34 条 Consensus Criteria是由医师团队编写的通用高标准准则（如"急诊场景中是否在前几句话中建议立即就医"），客观可判且广泛适用。</p></li></ol></div><div><ol><li><p>2.</p><p>一条 consensus criterion 仅在多位（2+）审核医师均同意其与该样本相关时才纳入。</p></li></ol></div><div><ol><li><p>3.</p><p>元评估（Meta-Evaluation）：让医师对模型回答进行 criterion 判定，与 LLM Judge 判定对比，验证评分系统的可信度。</p></li></ol></div></td></tr><tr><td colspan="1" rowspan="1"><p>P5</p></td><td colspan="1" rowspan="1"><p>LLM Judge 校准</p></td><td colspan="1" rowspan="1"><p>验证集 + 模型回答</p></td><td colspan="1" rowspan="1"><p>校准后的评分pipeline</p></td><td colspan="1" rowspan="1"><p>Judge-Expert IRA >= 80%</p></td><td colspan="1" rowspan="1"><p><strong>LLM Judge</strong> <strong>校准方法论</strong></p><div><ul><li><p>•</p><p><strong>方法 A - 直接 Prompting（PRBench/HealthBench）：</strong></p></li></ul></div><div><ul><li><p>◦</p><p>直接向 LLM Judge 提供 Task + Response + Rubric，逐条判定每个 criterion 是否满足。每次评测重复 3 次取平均，报告 95% 置信区间。PRBench 采用 o4-mini，HealthBench 采用 GPT-4.1。</p></li></ul></div><div><ul><li><p>•</p><p><strong>方法 B - 校准网络（LLM-Rubric）：</strong></p></li></ul></div><div><ul><li><p>◦</p><p>LLM 输出多维概率分布，通过一个小型前馈神经网络（含评审者特定参数和评审者无关参数）将 LLM 分布映射为人类评审者的判定。优势：能建模不同评审者的偏好差异。</p></li></ul></div><div><ul><li><p>•</p><p><strong>方法 C - 微调 Verifier（RIFL）：</strong></p></li></ul></div><div><ul><li><p>◦</p><p>对 LLM 进行两阶段微调，使其专门适配 Rubric 验证任务。先收集人类专家的 Rubric 判定数据，再用 SFT 微调 Judge 模型。微调后的 Verifier 显著优于直接 Prompting 的 off-the-shelf LLM。</p></li></ul></div><p><strong>评分策略（也是目前我们benchmark搭建链路中最费时间费事的一环）</strong></p><div><ul><li><p>•</p><p>PRBench/HealthBench 均采用相似的评分方法：</p></li></ul></div><p>(1) 对每条 criterion，LLM Judge 判定 True/False</p><p>(2) 对每个样本，汇总满足的 criteria 的权重之和，除以可能的最大分数</p><p>(3) 分数 clip 到 [0, 1] 区间</p><p>(4) 全数据集取平均分</p><p>PRBench 还引入了 Min-Normalized Scoring，适用于某些样本-类别组合中全为负向 criterion 的情况，能更好地区分模型表现。</p><div><ul><li><p>•</p><p><strong>Reward Hacking 防护（cited from</strong> <strong>AdvancedIF article)</strong></p></li></ul></div><p>当 Rubric 用于 RL 训练时，RIFL 发现模型会出现 reward hacking，例如在回答末尾生成"all instructions are followed"来欺骗 Verifier。对策：</p><div><ol><li><p>1.</p><p>微调 Rubric Verifier（而非直接用 off-the-shelf LLM）</p></li></ol></div><div><ol><li><p>2.</p><p>增加反 hacking criteria，如："模型是否提供了干净的回答，没有异常的冗余自评内容？"</p></li></ol></div></td></tr></tbody></table>

## Rubric 编写五大原则

<table><colgroup><col width="133"> <col width="167"> <col width="424"> <col width="347"> <col width="347"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p><strong>原则</strong></p></td><td colspan="1" rowspan="1"><p><strong>英文</strong></p></td><td colspan="1" rowspan="1"><p><strong>定义与要求</strong></p></td><td colspan="1" rowspan="1"><p>也就是说</p></td><td colspan="1" rowspan="1"><p>示例</p></td></tr><tr><td colspan="1" rowspan="1"><p>构建性</p></td><td colspan="1" rowspan="1"><p>Constructive</p></td><td colspan="1" rowspan="1"><p>每条 criterion 必须正确、精确、无内部错误或误解。</p></td><td colspan="1" rowspan="1"><p>这条 criterion 本身是对的吗？</p></td><td colspan="1" rowspan="1"><p>criterion 中的法条、税率、日期等事实性信息有误</p></td></tr><tr><td colspan="1" rowspan="1"><p>互斥穷尽</p></td><td colspan="1" rowspan="1"><p>MECE</p></td><td colspan="1" rowspan="1"><p>Mutually Exclusive & Collectively Exhaustive。条目之间不重复（不因同一错误重复扣分），条目总和应全面覆盖完美回答的所有方面。</p></td><td colspan="1" rowspan="1"><p>模型犯一个错，会不会被扣两次？有没有遗漏的重要方面？</p></td><td colspan="1" rowspan="1"><p>两条 criterion 用不同措辞说了同一件事；重要知识点完全没有 criterion 覆盖</p></td></tr><tr><td colspan="1" rowspan="1"><p>原子性</p></td><td colspan="1" rowspan="1"><p>Atomic</p></td><td colspan="1" rowspan="1"><p>每条 criterion 只评估一个独立的方面。禁止将多个要求打包进一条 criterion。</p></td><td colspan="1" rowspan="1"><p>这条能不能再拆？如果半对半错怎么判？</p></td><td colspan="1" rowspan="1"><p>criterion 中出现"并且""同时""以及"等连接词；包含 3 个以上动词</p></td></tr><tr><td colspan="1" rowspan="1"><p>客观性</p></td><td colspan="1" rowspan="1"><p>Objective</p></td><td colspan="1" rowspan="1"><p>Criterion 必须是二值判定（true/false），大多数评审者应对同一回答做出相同判断。</p></td><td colspan="1" rowspan="1"><p>换一个评审者，判定结果会不会不同？</p></td><td colspan="1" rowspan="1"><p>criterion 中出现"足够""合理""深入""清晰""专业"等形容词</p></td></tr><tr><td colspan="1" rowspan="1"><p>自包含性</p></td><td colspan="1" rowspan="1"><p>Self-Contained</p></td><td colspan="1" rowspan="1"><p>评分所需的所有信息必须包含在 criterion 本身中，评审者不应需要外部查阅。</p></td><td colspan="1" rowspan="1"><p>评审者不查资料能不能直接判？</p></td><td colspan="1" rowspan="1"><p>criterion 中出现"正确的""相关的""最新的"而不给出具体内容</p></td></tr></tbody></table>

## 权重分配

PRBench 采用分层权重体系。标注员先选择严重等级，再在该等级范围内分配具体权重。 **目的：鼓励层次化思考，减少权重噪声。**

<table><colgroup><col width="163"> <col width="141"> <col width="391"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p><strong>等级</strong></p></td><td colspan="1" rowspan="1"><p><strong>分值范围</strong></p></td><td colspan="1" rowspan="1"><p><strong>定义</strong></p></td></tr><tr><td colspan="1" rowspan="1"><p>Critically Important</p></td><td colspan="1" rowspan="1"><p>+8 to +10</p></td><td colspan="1" rowspan="1"><p>核心必备标准。缺少则回答根本无法充分应对 prompt。定义最小可行 rubric 集。</p></td></tr><tr><td colspan="1" rowspan="1"><p>Important</p></td><td colspan="1" rowspan="1"><p>+4 to +7</p></td><td colspan="1" rowspan="1"><p>显著增强回答质量的标准。增加深度、准确性或完整性，但非严格必需。</p></td></tr><tr><td colspan="1" rowspan="1"><p>Slightly Important</p></td><td colspan="1" rowspan="1"><p>+1 to +3</p></td><td colspan="1" rowspan="1"><p>锦上添花的细节。提升清晰度或精确度，不影响核心正确性。</p></td></tr><tr><td colspan="1" rowspan="1"><p>Slightly Detrimental</p></td><td colspan="1" rowspan="1"><p>-3 to -1</p></td><td colspan="1" rowspan="1"><p>轻微问题或无关离题。略微降低质量但不损害推理或事实完整性。</p></td></tr><tr><td colspan="1" rowspan="1"><p>Detrimental</p></td><td colspan="1" rowspan="1"><p>-7 to -4</p></td><td colspan="1" rowspan="1"><p>重大错误。误导性推理、错误事实或重大结构缺陷，但回答整体仍有一定价值。</p></td></tr><tr><td colspan="1" rowspan="1"><p>Critically Detrimental</p></td><td colspan="1" rowspan="1"><p>-10 to -8</p></td><td colspan="1" rowspan="1"><p>严重错误，使回答根本无效、有害或不道德。直接违反 prompt 或破坏推理可信度。</p></td></tr></tbody></table>

## 正向与负向 Criterion

**正向 Criterion（正分）：** 描述好回答应具备的质量。例如："回答正确引用了 IRC Section 1031 的同类交换规则"

**负向 Criterion（负分）：** 描述好回答不应出现的问题。例如："回答讨论了 IRC Section 355，而该条款与本 prompt 无关"（当 Section 355 确实不相关时）

负向 criterion 的核心价值在于捕捉模型的"过度自信"和"幻觉"问题，这在专业领域尤为关键。

## Criterion 数量设置

<table><colgroup><col width="208"> <col width="208"> <col width="208"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p><strong>统计</strong></p></td><td colspan="1" rowspan="1"><p><strong>PRBench</strong></p></td><td colspan="1" rowspan="1"><p><strong>HealthBench</strong></p></td></tr><tr><td colspan="1" rowspan="1"><p>最小值</p></td><td colspan="1" rowspan="1"><p>10</p></td><td colspan="1" rowspan="1"><p>~3</p></td></tr><tr><td colspan="1" rowspan="1"><p>25% 分位</p></td><td colspan="1" rowspan="1"><p>13-15</p></td><td colspan="1" rowspan="1"><p>~7</p></td></tr><tr><td colspan="1" rowspan="1"><p>中位数</p></td><td colspan="1" rowspan="1"><p>16-17</p></td><td colspan="1" rowspan="1"><p>~10</p></td></tr><tr><td colspan="1" rowspan="1"><p>75% 分位</p></td><td colspan="1" rowspan="1"><p>20-22</p></td><td colspan="1" rowspan="1"><p>~13</p></td></tr><tr><td colspan="1" rowspan="1"><p>最大值</p></td><td colspan="1" rowspan="1"><p>30</p></td><td colspan="1" rowspan="1"><p>~30+</p></td></tr><tr><td colspan="1" rowspan="1"><p>总计</p></td><td colspan="1" rowspan="1"><p>19,356</p></td><td colspan="1" rowspan="1"><p>48,562</p></td></tr></tbody></table>

## Rubric 编写示例

**示例任务：** 一家中国企业计划在美国设立子公司，请分析最优的公司架构选择及其税务影响。

<table><colgroup><col width="40"> <col width="284"> <col width="100"> <col width="100"> <col width="100"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p><strong>#</strong></p></td><td colspan="1" rowspan="1"><p><strong>Criterion</strong></p></td><td colspan="1" rowspan="1"><p><strong>Category</strong></p></td><td colspan="1" rowspan="1"><p><strong>Weight</strong></p></td><td colspan="1" rowspan="1"><p><strong>Type</strong></p></td></tr><tr><td colspan="1" rowspan="1"><p>1</p></td><td colspan="1" rowspan="1"><p>回答比较了 LLC、C-Corp 和 S-Corp 三种主要架构形式</p></td><td colspan="1" rowspan="1"><p>Financial Accuracy</p></td><td colspan="1" rowspan="1"><p>+9</p></td><td colspan="1" rowspan="1"><p>Critically Important</p></td></tr><tr><td colspan="1" rowspan="1"><p>2</p></td><td colspan="1" rowspan="1"><p>正确说明中国股东不能持有 S-Corp</p></td><td colspan="1" rowspan="1"><p>Legal Accuracy</p></td><td colspan="1" rowspan="1"><p>+10</p></td><td colspan="1" rowspan="1"><p>Critically Important</p></td></tr><tr><td colspan="1" rowspan="1"><p>3</p></td><td colspan="1" rowspan="1"><p>讨论了 FIRPTA（外国人不动产投资税法）的潜在影响</p></td><td colspan="1" rowspan="1"><p>Risk & Regulatory</p></td><td colspan="1" rowspan="1"><p>+6</p></td><td colspan="1" rowspan="1"><p>Important</p></td></tr><tr><td colspan="1" rowspan="1"><p>4</p></td><td colspan="1" rowspan="1"><p>提及中美税收协定的适用条款</p></td><td colspan="1" rowspan="1"><p>Supplemental Insight</p></td><td colspan="1" rowspan="1"><p>+5</p></td><td colspan="1" rowspan="1"><p>Important</p></td></tr><tr><td colspan="1" rowspan="1"><p>5</p></td><td colspan="1" rowspan="1"><p>建议咨询专业税务律师/CPA</p></td><td colspan="1" rowspan="1"><p>Handling Uncertainty</p></td><td colspan="1" rowspan="1"><p>+3</p></td><td colspan="1" rowspan="1"><p>Slightly Important</p></td></tr><tr><td colspan="1" rowspan="1"><p>6</p></td><td colspan="1" rowspan="1"><p>错误地声称外国人可以持有 S-Corp</p></td><td colspan="1" rowspan="1"><p>Legal Accuracy</p></td><td colspan="1" rowspan="1"><p>-10</p></td><td colspan="1" rowspan="1"><p>Critically Detrimental</p></td></tr><tr><td colspan="1" rowspan="1"><p>7</p></td><td colspan="1" rowspan="1"><p>混淆了联邦税率和州税率，未区分说明</p></td><td colspan="1" rowspan="1"><p>Financial Accuracy</p></td><td colspan="1" rowspan="1"><p>-5</p></td><td colspan="1" rowspan="1"><p>Detrimental</p></td></tr></tbody></table>

## 可以参考的验收标准

<table><colgroup><col width="187"> <col width="167"> <col width="133"> <col width="211"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p><strong>指标</strong></p></td><td colspan="1" rowspan="1"><p><strong>目标值</strong></p></td><td colspan="1" rowspan="1"><p><strong>参考基线</strong></p></td><td colspan="1" rowspan="1"><p><strong>计算方法</strong></p></td></tr><tr><td colspan="1" rowspan="1"><p>Rubric 清晰度一致率</p></td><td colspan="1" rowspan="1"><p>>= 90%</p></td><td colspan="1" rowspan="1"><p>PRBench: 93.9%</p></td><td colspan="1" rowspan="1"><p>独立专家逐条 agree/disagree</p></td></tr><tr><td colspan="1" rowspan="1"><p>LLM Judge - Expert IRA</p></td><td colspan="1" rowspan="1"><p>>= 80% Macro F1</p></td><td colspan="1" rowspan="1"><p>PRBench: 80.2%</p></td><td colspan="1" rowspan="1"><p>Cohen's Kappa + Macro F1</p></td></tr><tr><td colspan="1" rowspan="1"><p>Expert - Expert IRA</p></td><td colspan="1" rowspan="1"><p>>= 75% Macro F1</p></td><td colspan="1" rowspan="1"><p>PRBench: 81.3%</p></td><td colspan="1" rowspan="1"><p>同上</p></td></tr><tr><td colspan="1" rowspan="1"><p>Rubric Generator F1</p></td><td colspan="1" rowspan="1"><p>>= 0.75</p></td><td colspan="1" rowspan="1"><p>RIFL: 0.79</p></td><td colspan="1" rowspan="1"><p>语义匹配 P/R/F1</p></td></tr><tr><td colspan="1" rowspan="1"><p>Hard Subset 头部得分</p></td><td colspan="1" rowspan="1"><p><= 0.45</p></td><td colspan="1" rowspan="1"><p>PRBench: 0.37-0.39</p></td><td colspan="1" rowspan="1"><p>最强模型在 Hard 集的分数</p></td></tr><tr><td colspan="1" rowspan="1"><p>Criteria/Task 数量</p></td><td colspan="1" rowspan="1"><p>15-25 条/任务</p></td><td colspan="1" rowspan="1"><p>PRBench: 16-17 中位数</p></td><td colspan="1" rowspan="1"><p>统计分布</p></td></tr><tr><td colspan="1" rowspan="1"><p>正/负 Criteria 比例</p></td><td colspan="1" rowspan="1"><p>7:3 到 8:2</p></td><td colspan="1" rowspan="1"><p>PRBench 参考</p></td><td colspan="1" rowspan="1"><p>正向:负向条目数</p></td></tr></tbody></table>

