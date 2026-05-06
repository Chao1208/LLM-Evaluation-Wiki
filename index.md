# LLM Evaluation Wiki 索引

> 最后更新: 2026-05-06 | 总页面数: 108 | 总资料数: 82

## 资料摘要
| 页面 | 类型 | 摘要 | 日期 |
|------|------|------|------|
| [TTS-PRISM](wiki/sources/tts-prism.md) | 论文+仓库 | 清华/小米, 12 维中文 TTS 诊断评测框架, Schema-driven Rubric Judge, 7B 超越 Gemini-2.5-Pro | 2026-05-06 |
| [Rubric-Based 推理数据调研报告](wiki/sources/rubric-based-reasoning-data-survey.md) | 调研报告 | 5 大 Rubric-Based Benchmark 全景调研、6 阶段构建方法论（P0-P5）、编写五大原则、LLM Judge 校准方法 | 2026-04-09 |
| [Rubric-Forge 报告](wiki/sources/rubric-forge.md) | 内部实践 | 百度内部 Rubric 自动评分系统，一致率从 ~71% 提升到 ~89%，关键洞察：质量重于数量 | 2026-04-09 |
| [AdvancedIF 仓库文档](wiki/sources/advancedif-repo.md) | 仓库 | Meta 开源评测工具链：3 种任务类型、两种专用 Judge、三级评测指标、CC-BY-NC | 2026-04-09 |
| [PRBench 仓库文档](wiki/sources/prbench-repo.md) | 仓库 | Scale AI 评测 harness：evals.py 入口、sampled/prefilled 模式、mean_clipped 指标 | 2026-04-09 |
| [BigLaw Bench 仓库文档](wiki/sources/biglaw-bench-repo.md) | 仓库 | Harvey AI 三层法律评测：Core(16种任务) + Workflows(SPA提取) + Retrieval(合同/邮件)、正负分 rubric | 2026-04-09 |
| [simple-evals 仓库文档](wiki/sources/simple-evals-repo.md) | 仓库 | OpenAI 评测框架：9 个 benchmark、大量模型跑分数据、14 语言多语言 MMLU、零样本+CoT 范式 | 2026-04-09 |
| [392 语音助手评测集](wiki/sources/392toolkit.md) | 内部实践 | 百度 392 条 query rubric 策略库，黑盒/白盒/超黑盒评测数据，语音助手情感陪伴场景 | 2026-04-09 |
| [HELM 全面评测](wiki/sources/helm.md) | 论文 | Stanford CRFM, 42 场景×7 指标类别，30 模型全面评测，TMLR 2023 | 2026-04-21 |
| [LLM 评测综述 (Chang)](wiki/sources/llm-eval-survey-chang.md) | 论文 | ACM TIST 2024 综述，what/where/how 三维度全景 | 2026-04-21 |
| [LLM 评测综述 (Future Internet)](wiki/sources/llm-eval-survey-future-internet.md) | 论文 | 文本生成评测方法综述，自动指标+人工评估+基准数据集 | 2026-04-21 |
| [幻觉综述](wiki/sources/hallucination-survey.md) | 论文 | 基础模型幻觉全景综述，事实性/忠实性分类，检测与缓解方法 | 2026-04-21 |
| [ChatGPT 替代人工评测](wiki/sources/chatgpt-human-eval-alternative.md) | 论文 | ACL 2023, LLM 能否替代人工评测，冗余偏差/自我增强偏差分析 | 2026-04-21 |
| [LLMBar 元评测](wiki/sources/llmbar.md) | 论文 | ICLR 2024, LLM 评审者质量元评测基准，自然+对抗测试集 | 2026-04-21 |
| [IFEval 指令遵循](wiki/sources/ifeval.md) | 论文 | Google, ~500 可编程验证指令，25 类型，无需人工/LLM 判断 | 2026-04-21 |
| [长上下文扩展](wiki/sources/effective-long-context.md) | 论文 | Meta NAACL 2024, Llama 2 → 32K 上下文，RoPE 缩放 | 2026-04-21 |
| [RewardBench](wiki/sources/rewardbench.md) | 论文 | NAACL 2025, RLHF 奖励模型评测基准，生成式 Judge 有竞争力 | 2026-04-21 |
| [AGIEval](wiki/sources/agieval.md) | 论文 | NAACL 2024, 20 项真实考试任务，双语（中英），人类中心评测 | 2026-04-21 |
| [MME 多模态评测](wiki/sources/mme.md) | 论文 | 14 子任务覆盖感知+认知，Yes/No 客观评测 | 2026-04-21 |
| [MMBench](wiki/sources/mmbench.md) | 论文 | Shanghai AI Lab, ~3000 选择题，20 细粒度能力，CircularEval | 2026-04-21 |
| [MM-Vet](wiki/sources/mm-vet.md) | 论文 | ICML 2024, 6 核心 VL 能力集成评测，218 题，GPT-4 Judge | 2026-04-21 |
| [MathVista](wiki/sources/mathvista.md) | 论文 | ICLR 2024, 6141 样本，视觉数学推理，28+3 数据集整合 | 2026-04-21 |
| [LVLM-EHub](wiki/sources/lvlm-ehub.md) | 论文 | VLM 全面评测基准，在线 Arena 人工评测，多维度能力量化 | 2026-04-21 |
| [VLMEvalKit](wiki/sources/vlmevalkit.md) | 论文 | MM '24, VLM 评测开源工具包，统一框架，模块化设计 | 2026-04-21 |
| [EvalCrafter](wiki/sources/evalcrafter.md) | 论文 | CVPR 2024, 700 prompt 视频生成评测，17 指标，人机相关性分析 | 2026-04-21 |
| [PIXIU/FLARE 金融评测](wiki/sources/pixiu-flare.md) | 论文 | 金融 NLP 多任务基准，FinMA 模型，5 类任务 | 2026-04-21 |
| [GPT 机器翻译评测](wiki/sources/gpt-mt-evaluation.md) | 论文 | Microsoft, GPT 翻译能力评测，多语言对比，BLEU/ChrF++ | 2026-04-21 |
| [代码 LLM 系统评测](wiki/sources/polycoder-code-eval.md) | 论文 | MAPS '22, PolyCoder 12 语言训练，HumanEval/MBPP 评测 | 2026-04-21 |
| [Prometheus](wiki/sources/prometheus.md) | 论文 | 开源 LLM 评审模型，Rubric 驱动细粒度评测，100K 反馈数据 | 2026-04-21 |
| [Prometheus 2](wiki/sources/prometheus-2.md) | 论文 | EMNLP 2024, 直接评分+成对排名双模式，权重合并技术 | 2026-04-21 |
| [Rubric Is All You Need](wiki/sources/rubric-is-all-you-need.md) | 论文 | 问题特定 Rubric 提升 LLM 代码评测准确性与一致性 | 2026-04-21 |
| [RL with Rubric Anchors](wiki/sources/rl-rubric-anchors.md) | 论文 | Rubric 锚点指导强化学习奖励模型训练 | 2026-04-21 |
| [Tulu 3](wiki/sources/2411-recent-eval.md) | 论文 | Ai2, 开放模型 Post-Training 前沿：SFT/DPO/RLVR 全流程 | 2026-04-21 |
| [Dramatron 剧本协作](wiki/sources/dramatron.md) | 论文 | CHI '23, LLM 协作写剧本，层级生成，15 位编剧用户研究 | 2026-04-21 |
| [ClassEval 类级代码生成](wiki/sources/classeval.md) | 论文 | ICSE '24, 100 个类级 Python 任务，3 种生成策略 | 2026-04-21 |
| [ChatGPT 医学准确性](wiki/sources/chatgpt-medical-accuracy.md) | 论文 | 33 医生×17 专科×284 题，中位准确度 5.5/6，8-17 天后显著改善 | 2026-04-21 |
| [LLM 临床决策评测](wiki/sources/llm-clinical-decision.md) | 论文 | Nature Medicine 2024, MIMIC-CDM 2400 病例，LLM 显著逊于医生 | 2026-04-21 |
| [评分量规形成性评估综述](wiki/sources/1-s2.0-scoring-rubrics-formative.md) | 论文 | 21 项研究系统综述，Rubric 促进透明性/反馈/自我调节 | 2026-04-21 |
| [Rubrics 与学习目标](wiki/sources/rubrics-learning-goals.md) | 论文 | Allen & Tanner 2006, STEM 教育 Rubric 设计实践指南 | 2026-04-21 |
| [Teaching with Rubrics](wiki/sources/teaching-with-rubrics.md) | 论文 | Andrade 2005, 教学中使用 Rubric 的优势与风险 | 2026-04-21 |
| [Rubric 设计研究](wiki/sources/rubric-design-education.md) | 论文 | Frontiers in Education 2018, Rubric 质量因素与评分可靠性 | 2026-04-21 |
| [Scoring Rubrics 指南](wiki/sources/scoring-rubrics-what-when-how.md) | 论文 | Moskal 2000, 分析型/整体型/通用/任务特定 Rubric 开发流程 | 2026-04-21 |
| [Rubric 缺陷与改进](wiki/sources/whats-wrong-right-rubrics.md) | 论文 | Popham 1997, 四大缺陷批判，倡导简短教学导向 Rubric | 2026-04-21 |
| [评分者间信度](wiki/sources/interrater-reliability.md) | 论文 | McHugh 2012, Kappa 统计量教程，信度等级标准 | 2026-04-21 |
| [临床预测模型评估](wiki/sources/clinical-prediction-model-eval.md) | 论文 | BMJ 2024, TRIPOD+AI 指南，区分度/校准/决策曲线 | 2026-04-21 |
| [ML 模型评估方法论](wiki/sources/ml-model-evaluation.md) | 论文 | Varoquaux & Colliot 2023, 交叉验证/偏差-方差/评估指标 | 2026-04-21 |
| [过拟合与模型调优](wiki/sources/overfitting-model-tuning.md) | 论文 | Montesinos López 2022, 正则化/超参调优/验证策略 | 2026-04-21 |
| [MT-Bench & Chatbot Arena](wiki/sources/mt-bench-chatbot-arena.md) | 论文 | LMSYS, 80 题多轮基准 + 众包 ELO 对战，GPT-4 Judge 与人类一致率 >80% | 2026-04-21 |
| [GPT-4 Technical Report](wiki/sources/gpt4-technical-report.md) | 论文 | OpenAI, 多模态大模型，Bar Exam 90th 百分位，MMLU 86.4%，可预测扩展 | 2026-04-21 |
| [InstructGPT](wiki/sources/instructgpt.md) | 论文 | OpenAI, RLHF 对齐里程碑，1.3B InstructGPT 优于 175B GPT-3，SFT→RM→PPO | 2026-04-21 |
| [Chain-of-Thought Prompting](wiki/sources/chain-of-thought.md) | 论文 | Google, CoT 推理提示，>100B 模型涌现能力，GSM8K +39pp | 2026-04-21 |
| [MMLU](wiki/sources/mmlu.md) | 论文 | UC Berkeley, 57 学科 15,908 题多任务理解基准，LLM 评测事实标准 | 2026-04-21 |
| [BLIP-2](wiki/sources/blip2.md) | 论文 | Salesforce, Q-Former 桥接冻结图像编码器与 LLM，188M 参数超 Flamingo80B | 2026-04-22 |
| [LLaVA](wiki/sources/llava.md) | 论文 | NeurIPS 2023, 视觉指令微调，GPT-4 数据生成，LLaVA-Bench | 2026-04-22 |
| [Llama 2](wiki/sources/llama2.md) | 论文 | Meta, 7B-70B 开源模型，RLHF 方法论，安全评测体系，"质量重于数量" | 2026-04-22 |
| [Rubicon 框架报道](wiki/sources/rubicon-news.md) | 新闻 | 蚂蚁浙大 Rubric 驱动 RL，Veto/饱和度感知聚合，5000+样本击败 671B | 2026-04-22 |
| [Rubric Is All You Need 博客](wiki/sources/rubric-all-you-need-blog.md) | 博客 | CRE/PRE/EME 三种评估方法技术解读，Leniency 指标 | 2026-04-22 |
| [Rubric Python 库](wiki/sources/rubric-lib-repo.md) | 仓库 | The LLM Data Company, 加权 Rubric 评测库，三种评分策略，RL 训练支持 | 2026-04-22 |
| [DR Tulu](wiki/sources/dr-tulu-repo.md) | 仓库 | Ai2, 演化 Rubric RL 训练深度研究 agent，8B 匹配 OpenAI DR | 2026-04-22 |
| [C-Eval 仓库](wiki/sources/ceval-repo.md) | 仓库 | SJTU-LIT, 13,948 道多选题覆盖 52 学科，NeurIPS 2023，中文考试型评测 | 2026-04-22 |
| [CLUE 仓库](wiki/sources/clue-repo.md) | 仓库 | CLUEbenchmark 社区, 9 个 NLU 任务+14G 语料库，COLING 2020，中文语言理解基准 | 2026-04-22 |
| [ChineseGLUE 仓库](wiki/sources/chineseglue-repo.md) | 仓库 | CLUEbenchmark 社区, 13 个任务经典版，中文 NLU 基准先驱 | 2026-04-22 |
| [SuperCLUE 仓库](wiki/sources/superclue-repo.md) | 仓库 | CLUEbenchmark 社区, 中文大模型综合评测，四大象限十大能力，含 Agent 和安全 | 2026-04-22 |
| [Prometheus 仓库](wiki/sources/prometheus-repo.md) | 仓库 | KAIST AI, 开源评测 LM，Rubric 驱动细粒度评测，Feedback Collection 数据集 | 2026-04-22 |
| [OpenCompass 评测平台](wiki/sources/opencompass-repo.md) | 仓库 | 上海 AI Lab, 一站式评测平台，70+ 数据集，20+ 模型，CompassKit/Hub/Rank 三件套 | 2026-04-22 |
| [lm-evaluation-harness](wiki/sources/lm-evaluation-harness-repo.md) | 仓库 | EleutherAI, 60+ benchmark，HuggingFace Open LLM Leaderboard 后端，行业事实标准 | 2026-04-22 |
| [HELM 评测框架](wiki/sources/helm-repo.md) | 仓库 | Stanford CRFM, 全面评测框架，多维度指标，扩展至 VHELM/HEIM/MedHELM 等 | 2026-04-22 |
| [AlpacaEval 自动评估器](wiki/sources/alpaca-eval-repo.md) | 仓库 | Stanford Tatsu Lab, LLM-as-Judge 成对评测，LC Win Rate 与 Arena 相关 0.98 | 2026-04-22 |
| [FastChat 平台](wiki/sources/fastchat-repo.md) | 仓库 | LMSYS/UC Berkeley, Chatbot Arena 底层平台，MT-Bench，Vicuna 训练/服务/评测 | 2026-04-22 |
| [HumanEval 仓库](wiki/sources/human-eval-repo.md) | 仓库 | OpenAI, 164 道 Python 代码生成题，pass@k 指标，执行式评测范式 | 2026-04-22 |
| [EvalScope 仓库](wiki/sources/evalscope-repo.md) | 仓库 | 阿里/ModelScope, 多后端评测框架，50+ 基准，LLM/VLM/Agent/Audio | 2026-04-22 |
| [Inspect AI 仓库](wiki/sources/inspect-ai-repo.md) | 仓库 | UK AISI, 200+ 预置评测，可组合组件，政府级 AI 安全评测基础设施 | 2026-04-22 |
| [PromptBench 仓库](wiki/sources/promptbench-repo.md) | 仓库 | Microsoft, Prompt 鲁棒性评测，4 级对抗攻击，DyVal 动态评测 | 2026-04-22 |
| [BEIR 仓库](wiki/sources/beir-repo.md) | 仓库 | TU Darmstadt, 17 数据集信息检索评测，零样本迁移，多架构支持 | 2026-04-22 |
| [RubricRAG](wiki/sources/2603.20882-rubricrag.md) | 论文 | 检索增强 Rubric 生成，从已有 rubric 库检索相似 rubric 指导新 rubric 生成 | 2026-04-23 |
| [RubricEval](wiki/sources/2603.25133-rubriceval.md) | 论文 | Rubric 级元评测基准，3,486 实例，GPT-4o 在 Hard 级仅 55.97% | 2026-04-23 |
| [RubricBench](wiki/sources/2603.01562-rubricbench.md) | 论文 | 评估 LLM rubric 与人类标准对齐度，覆盖度/精确度/可操作性/正交性评估 | 2026-04-23 |
| [DeCE](wiki/sources/2509.16093-dece.md) | 论文 | 分解式评测，Precision+Recall 拆分，r 从 0.12 提升至 0.78 | 2026-04-23 |
| [AdaRubric](wiki/sources/2603.21362-adarubric.md) | 论文 | Alibaba, 任务自适应 Rubric 生成，r=0.79, α=0.83，自适应维度>骨干模型能力 | 2026-04-23 |
| [LH-Bench](wiki/sources/2603.22744-lh-bench.md) | 论文 | Metaphi.ai, 长周期 Agent 三层评测，专家 rubric κ=0.60 vs LLM κ=0.46，binary-observable | 2026-04-23 |
| [Cross-Lingual Judge](wiki/sources/2603.18557-cross-lingual-judge.md) | 论文 | CISPA/Amazon, UCS 跨语言评测迁移，英文训练→11+语言零样本迁移 | 2026-04-23 |
| [MVES 框架](wiki/sources/2601.22025-mves-framework.md) | 论文 | Define-Test-Diagnose-Fix 评测循环，task-specific>generic prompt，MVES 三层框架 | 2026-04-23 |

## 模型
| 页面 | 机构 | 摘要 | 资料数 |
|------|------|------|--------|

## 评测基准
| 页面 | 领域 | 摘要 | 资料数 |
|------|------|------|--------|
| [PRBench](wiki/benchmarks/prbench.md) | 法律+金融 | Scale AI，1,100 任务 / 19,356 criteria，专家 IRA 93.9% | 2 |
| [HealthBench](wiki/benchmarks/healthbench.md) | 医疗 | OpenAI，5,000 对话 / 48,562 criteria，Consensus 机制 | 2 |
| [BigLaw Bench](wiki/benchmarks/biglaw-bench.md) | 法律 | Harvey AI，三层架构 Core+Workflows+Retrieval，正负分 rubric | 2 |
| [AdvancedIF](wiki/benchmarks/advancedif.md) | 通用指令遵循 | Meta/Llama，1,600+ prompts，3 种任务类型，RIFL 规模化方案 | 2 |
| [CNFinBench](wiki/benchmarks/cnfinbench.md) | 中国金融合规 | 学术团队，13,000+ 实例，安全-有效性双轨 | 1 |
| [392 语音助手评测集](wiki/benchmarks/392-voice-assistant.md) | 语音助手/情感陪伴 | 百度内部，392 query，双策略 rubric，三值评分，多 persona | 1 |

## 机构与人物
| 页面 | 类型 | 摘要 | 资料数 |
|------|------|------|--------|
| [Scale AI](wiki/entities/scale-ai.md) | 机构 | AI 数据基础设施公司，PRBench 开发者 | 2 |
| [OpenAI](wiki/entities/openai.md) | 机构 | GPT 系列开发者，HealthBench 与 simple-evals 开发者 | 2 |
| [Harvey AI](wiki/entities/harvey-ai.md) | 机构 | 法律 AI 公司，BigLaw Bench 开发者，18 人团队 | 2 |
| [Meta](wiki/entities/meta.md) | 机构 | Llama 系列开发者，AdvancedIF 开发者 | 2 |
| [LMSYS / Stanford](wiki/entities/lmsys.md) | 机构 | MT-Bench & Chatbot Arena、FastChat、AlpacaEval、HELM 开发者 | 5 |
| [Google](wiki/entities/google.md) | 机构 | IFEval、Chain-of-Thought 提出者，Gemini 系列开发者 | 2 |
| [小米](wiki/entities/xiaomi.md) | 机构 | MiMo-Audio 开发者，TTS-PRISM 语音诊断评测框架 | 1 |

## 概念与方法论
| 页面 | 摘要 | 资料数 |
|------|------|--------|
| [Rubric-Based 评测方法论](wiki/concepts/rubric-based-evaluation.md) | 细粒度评分标准替代模糊打分，五大原则、六阶段构建流程、四种指标体系、自动 Rubric 生成前沿、语音模态扩展 | 11 |
| [LLM-as-Judge](wiki/concepts/llm-as-judge.md) | 用 LLM 作为自动评审者，三种校准方法，一致率基准，评测分解与自适应前沿（DeCE/AdaRubric/UCS），语音 Judge | 11 |
| [数字人视频重建质量评测方法](wiki/concepts/digital-human-video-evaluation.md) | 数字人视频重建评测全景：自动指标（CSIM/LPIPS/FVD/LSE 等）、人工 MOS/成对对比、VLM-as-Judge 新范式 | 0 |
| [RLHF](wiki/concepts/rlhf.md) | 基于人类反馈的强化学习，SFT→RM→PPO 经典流程，DPO/RLVR/Rubric-driven RL 演进方向 | 7 |
| [多模态大模型评测](wiki/concepts/multimodal-evaluation.md) | VLM 评测基准全景（MME/MMBench/MM-Vet/MathVista/EvalCrafter），评测方法论，BLIP-2/LLaVA 里程碑 | 8 |
| [评测框架与工具](wiki/concepts/evaluation-frameworks.md) | 12 个开源评测框架对比选型（OpenCompass/lm-eval-harness/HELM/EvalScope/Inspect AI 等） | 12 |
| [中文评测基准](wiki/concepts/chinese-evaluation-benchmarks.md) | ChineseGLUE→CLUE→C-Eval→SuperCLUE 发展脉络，中文特有评测挑战 | 5 |
| [代码生成评测](wiki/concepts/code-evaluation.md) | pass@k 执行式评测，函数→类→项目粒度演进，HumanEval/ClassEval/PolyCoder 对比 | 3 |
| [领域专项评测](wiki/concepts/domain-specific-evaluation.md) | 医疗/法律/金融/翻译/创作/长上下文六大领域评测全景，共性挑战分析 | 9 |
| [评测方法论基础](wiki/concepts/evaluation-methodology-foundations.md) | LLM 评测综述（What/Where/How）、幻觉分类、IRA/Kappa 统计方法、过拟合检测 | 7 |

## 工具
| 页面 | 摘要 | 资料数 |
|------|------|--------|
| [Rubric-Forge](wiki/entities/rubric-forge.md) | 百度内部 Rubric 自动评分系统，Sonnet 4.6 评分，veto 机制 | 1 |

## 分析报告
| 页面 | 问题 | 日期 |
|------|------|------|
| [392 Rubrics 可用性提升分析](wiki/analyses/392-rubric-improvement.md) | 392 评测集 rubrics 的五大改进方向：原子化拆分、模板化、统一评分、校准闭环、能力标签 | 2026-04-09 |
| [数字人视频重建人工评测量表 v1.0](wiki/analyses/digital-human-eval-rubric-v1.md) | 8+1 维度评分体系（对齐 caption 四层结构）、评分流程 SOP、校准培训、VLM 衔接方案 | 2026-04-16 |
