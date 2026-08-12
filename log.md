# Wiki 操作日志

## [2026-08-12] ingest | 71 篇盲验证 / 锚定实测 / 抽检设计 / 评测作弊论文
- 资料路径: `raw/papers/` 新增 71 篇 PDF（1974–2026，跨法庭科学、医学影像筛查、工业质量控制、审计统计、教育测量、因果实验设计、LLM 安全评测）
- **入库前去重**: 删除 6 个 md5 完全相同的冗余文件（3 个与已入库 PDF 重名不同名：`2405.11919-klie-*`、`arxiv2306-yang-*`、`emnlp2016-berzak-*`；3 对内部重复：`arxiv2605.06993-*`、`eurradiol2022-cooper-*`、`iitk-kundu-*`）
- **修正错误资料**: `kdd2017-lakkaraju-selective-labels-problem.pdf` 原内容实为日球层物理论文（arXiv:1702.00399），已替换为 KDD 2017 正式版原文（DOI 10.1145/3097983.3098066）
- 新建概念页 (1): wiki/concepts/evaluation-awareness-and-gaming.md —— 评测感知/sandbagging/对 judge 的作弊三层次 + 高风险考核经济学类比
- 扩充概念页 (1): wiki/concepts/annotation-noise-and-pipeline-quality.md —— 新增簇 8-12（盲验证跨领域先例、锚定效应实测、抽检与验收抽样设计、干预设计买识别力、部分识别与自适应采集推断）+ 簇 2/3 补充表，累计索引 155 篇
- 新建 source 页 (9): eurrad2021-cooper-blinding-second-reader-mammography.md, lrec2022-mikulova-pre-annotation-bias.md, fbi-frd501-verification-and-blind-verification.md, meas2019-severn-steiner-mackay-targeted-verification-conditional-sampling.md, kdd2017-lakkaraju-selective-labels-problem.md, 2103.14749-pervasive-label-errors-test-sets.md, 2505.23836-llms-often-know-when-they-are-being-evaluated.md, 2406.07358-ai-sandbagging-strategic-underperformance-evaluations.md, 2604.15224-context-over-content-evaluation-faking-automated-judges.md
- 更新页面: wiki/concepts/llm-as-judge.md（新增「上下文框架可以移动 judge 判定」一节）、index.md、log.md
- 关键发现:
  * **盲验证在法庭科学与医学影像已有成文标准与大样本实证**——「随机化质检可见性」不需要从零论证。FBI/SWGFAST/OSAC 有 blind verification 标准；Cooper 等在 111.9 万人乳腺筛查队列上量化了 alliterative bias
  * **锚定是单向的、条件于一阶段为阳性**：Cooper 数据中一读召回时二读跟随率非盲 74.7% vs 盲化 69.8%（差 4.9pp），但一读不召回时两组几乎相同（2.33% vs 2.32%）。含义：$P(Q|Y,A)$ 不能用单一「锚定强度」标量建模
  * **偏倚存在 ≠ 准确率下降，且在无 gold 时观测不可见**：Mikulová 等发现预标注后准确率完全没降（UAS 96.5 vs 96.5）、速度快 1.7 倍，但 κ 反而上升（0.99 vs 0.96），作者自陈这个上升正是偏倚的表现。所以在无金标准的流水线里，偏倚只表现为「一致率很高」——这恰是症状而非质量证据
  * **目标核验的信息高度集中**：Severn 等只核验两个中间分箱（平均 8.4% 的重复测量件）即获得约 90% 的精度与偏差改善收益；示例中 14 次金标准测量可替代 100 次
  * **标签错误会翻转基准排名**：10 个常用测试集平均 ≥3.3% 标签错误（ImageNet val 5.83%）；纠正后 ResNet-18 从 34/34 升至 1/34、NASNet 从 1/34 跌至 29/34
  * **judge 的 rationale 检测不到上下文攻击**：冻结 1520 条回答、只在 judge system prompt 加一句后果框架 → 三个 judge 检出率全降（峰值 −9.8pp / 相对 −29.6%），而 4560 次推理判定中 CoT 提及该框架 **0 次**。依赖「看 judge 理由」做质量保证对此无效
  * **imputation 会得出方向性错误的结论**：Lakkaraju 等的选择性标注实验中，propensity matching 等方法让「实际比法官差」的模型看起来「优于法官」
- **方法论说明**: 71 篇中 9 篇逐篇通读全文并建 source 页；其余在概念页以分簇索引表收录（标题+ID+一句话），**未通读全文**，概念页已显式标注
- **遗留问题**: 全库链接检查发现 17 处断链，全部来自 2026-05-06 视觉模型摄入批次（指向未创建的 clip.md / fid.md / clipscore.md / data-contamination.md 等页），本轮新增页面无断链。待后续 lint 处理

## [2026-08-10] ingest | 84 篇标注噪声 / 无 gold 可识别性 / 样本高效评测论文
- 资料路径: `raw/papers/` 新增 84 篇 PDF（1979–2026，跨众包系统、诊断医学统计、计量经济学、弱监督、标签噪声学习、PPI/active testing、LLM-as-Judge 可信度七个社区）
- 新建概念页 (1): wiki/concepts/annotation-noise-and-pipeline-quality.md —— 七大主题簇 + 全部 84 篇分簇索引表
- 新建 source 页 (10): dawid-skene-1979-observer-error-rates-em.md, hcomp2013-baba-kashima-statistical-quality-estimation-crowdsourcing.md, 1605.07723-data-programming-creating-large-training-sets-quickly.md, 1711.10160-snorkel-weak-supervision.md, 2607.15455-design-based-supervised-learning-noisy-human-labels.md, 2606.15031-partial-identification-from-llm-prompts.md, 2605.29800-nine-judges-two-effective-votes.md, 2210.06812-crowdlab-consensus-labels-quality-scores.md, 2103.05331-active-testing-sample-efficient-model-evaluation.md, 2408.15204-confidence-driven-inference-llm-annotations.md
- 更新页面: wiki/concepts/llm-as-judge.md（新增「误差相关性」「rating indeterminacy」「高一致率≠可替代人工标注」三节）、wiki/concepts/evaluation-methodology-foundations.md（新增「参考标签本身的噪声」板块）、index.md、log.md
- 关键发现:
  * **多 judge 投票收益远低于名义值**：Nine Judges 实测 judge 误差相关后，9 个 judge 的 Kish 有效样本量≈2 票；Great Models Think Alike 显示模型越强误差越像。推论：无限 token 买到的是相关重复，不是有效通道数增长
  * **高一致率不保证下游推断有效**：Confidence-Driven Inference 在 stance 任务 κ=0.57（三设定中最高）下，纯 LLM 的 odds-ratio 估计方向反了、覆盖率 0%
  * **跨簇收敛的工程判据**：PA-DSL（簇 3）与 active testing/PPI（簇 6）独立地都要求「已知抽样概率的 gold 层」——回顾式、抽检规则未记录的流水线无法使用这整套工具。这为 Argonaut(2015) 的「复核预算里留随机抽检层」提供了理论必要性
  * **MAR/MNAR 是定义性区分**：抽检只依赖已观测的 (A,x) 是 MAR 不是 MNAR；误标会导致选错整套估计方法
  * **代理模型「不同」比「更准」值钱**（Active Testing）：随机森林代理评测 ResNet-18 胜过 ResNet 系代理；主动学习采集函数在评测场景反而更差（压制了评测需要的 aleatoric 不确定性）
- **方法论说明**: 84 篇中 10 篇逐篇通读全文并建 source 页；其余 74 篇在概念页以分簇索引表收录（标题+ID+一句话），描述基于标题与既有概念页的引用语境，**未通读全文**，概念页已就此显式标注

## [2026-07-08] ingest | 5 篇 Rubric 评测/训练论文（HealthBench + RaR + OpenRubrics + Reflect-and-Revise + RubricHub）
- 资料路径: `raw/papers/2505.08775-healthbench.pdf`, `raw/papers/2507.17746-rubrics-as-rewards.pdf`, `raw/papers/2510.07743-scalable-synthetic-rubric.pdf`, `raw/papers/2510.09030-reflect-and-revise.pdf`, `raw/papers/2601.08430-rubrichub.pdf`
- 新建 source 页 (5): healthbench.md, rubrics-as-rewards.md, openrubrics.md, reflect-and-revise.md, rubrichub.md
- 新建实体页 (1): li-auto.md（理想汽车，RubricHub 主导者）
- 更新页面: healthbench.md（benchmark 专页回填一手论文摘要链接、排行榜、Consensus/元评测细节）、scale-ai.md（补 RaR）、openai.md（补 HealthBench 一手论文）、rubric-based-evaluation.md（rubric-as-reward + 负向 criteria 矛盾 + OpenRubrics/RubricHub/Reflect-and-Revise 自动生成）、llm-as-judge.md（grader 可信度元评测 + Rubric-RM）、rlhf.md（RaR/RubricHub 加入 Rubric-Driven RL）、index.md、log.md
- 关键发现:
  * HealthBench 用 60,896+ 元评测样本证明 grader-医师一致性 ≈ 医师间一致性（55-75%），确立 LLM-as-Judge 在高风险域可信度
  * RaR 把 rubric 当 RL 密集奖励，将 RLVR 从可验证域扩展到医学/科学，RaR-Implicit +31% HealthBench
  * RubricHub 用 Qwen3-14B 在 HealthBench 达 69.3，超过 GPT-5（67.2）
  * OpenRubrics 用 Contrastive Rubric Generation 训练可解释奖励模型 Rubric-RM
- **重要跨论文矛盾（已在多页标注）**: 评测场景保留负分扣分捕捉幻觉（HealthBench/PRBench 7:3~8:2），但 RL 训练场景负向 criteria 作用有限甚至有害——RaR 去掉 pitfall 影响甚微、RubricHub 加惩罚项反而降低性能（66.2→63.2）、Reflect-and-Revise 引 Furuhashi 2025 删组件反而提升。二者负向 criteria 设计应区分对待。

## [2026-06-11] research | vadioPK 数字人视频评测专项调研
- 任务来源: vadioPK 项目（短视频对相似度评测），决策"v0.1 用什么 metric"
- 抓取 raw（暂存于项目内 research/raw/，未全部存 PDF）：
  arxiv:2511.04520 (THEval), arxiv:2410.07718 (Hallo2),
  arxiv:2402.17485 (EMO), arxiv:2409.02634 (Loopy),
  arxiv:2403.17694 (AniPortrait), arxiv:2503.21755 (VBench-2.0),
  arxiv:2406.15252 (VideoScore), arxiv:2509.22799 (VideoScore2),
  arxiv:2508.07989 (Motion Blindness), arxiv:2508.00144 (WCS)
- 新建 source 页 (1): wiki/sources/theval.md
- 新建 research 报告 (1): wiki/researches/digital-human-video-eval-survey.md
- 关键发现:
  * THEval (2025) 以纯 pip 工具栈达到 ρ=0.870 vs 人工 — 比所有
    talking-head 论文的 metric 组合更可靠
  * SyncNet 与人工评分**负相关** (LSE-C ρ=−0.16, LSE-D ρ=−0.27)
  * OpenFace AU 当代 talking-head 论文无人使用，社区改用 E-FID + py-feat
  * VBench-2.0 显式排除 lip-sync from MLLM judging — Gemini judge 应限制
    在 A/Z 等静态语义维度
- 影响: 触发 openspec change `reset-eval-pipeline-to-theval-aligned`
  (位于 vadioPK 项目内)
- 调研失败模式记录: 子 agent spawn 两次失败/虚假完成，最终主 agent 自己
  WebFetch + WebSearch 完成；arxiv abstract 页拿不到正文表格，必须用
  `/html/<id>` 路径

## [2026-05-06] research | 视觉模型训练数据与评测方法全景研究
- 资料路径: `raw/papers/llava-onevision.pdf`, `raw/papers/cambrian-1.pdf`, `raw/papers/sharegpt4v.pdf`, `raw/papers/stable-diffusion-3.pdf`, `raw/papers/dalle3-system-card.pdf`, `raw/papers/florence-2.pdf`, `raw/papers/datacomp.pdf`, `raw/papers/laion-5b.pdf`, `raw/papers/pickscore.pdf`, `raw/papers/vbench.pdf`, `raw/papers/imagereward.pdf`, `raw/papers/hpsv2.pdf`, `raw/papers/t2i-compbench.pdf`, `raw/papers/genai-bench.pdf`, `raw/papers/lmms-eval.pdf`, `raw/papers/cogvideox.pdf`, `raw/papers/panda-70m.pdf`, `raw/papers/internvid.pdf`, `raw/papers/qwen-vl-2.5.pdf`
- 新建摘要页 (20): llava-onevision.md, cambrian-1.md, sharegpt4v.md, stable-diffusion-3.md, dalle3-system-card.md, florence-2.md, datacomp.md, laion-5b.md, pickscore.md, vbench.md, imagereward.md, hpsv2.md, t2i-compbench.md, genai-bench.md, lmms-eval.md, cogvideox.md, panda-70m.md, internvid.md, internvl-2.5.md, qwen-vl-2.5.md
- 新建分析报告 (2): visual-model-training-data-landscape.md, visual-model-evaluation-methods.md
- 更新: index.md（总页面数 108→129，总资料数 82→101）
- 关键发现: "少而精"胜"多而杂"贯穿所有论文；合成 caption 成为标准；学习偏好模型远优于 FID/CLIP；VLM Judge 在图像评测仍不成熟（hard case 仅 49%）
- 注: `raw/papers/internvl-2.5.pdf` 实际为 VTC-CLS 论文（PDF 命名错误）

## [2026-04-23] ingest | 8 篇 Rubric/评测分解前沿论文批量摄取
- 资料路径: `raw/papers/2603.20882-rubricrag.pdf`, `raw/papers/2603.25133-rubriceval.pdf`, `raw/papers/2603.01562-rubricbench.pdf`, `raw/papers/2509.16093-dece.pdf`, `raw/papers/2603.21362-adarubric.pdf`, `raw/papers/2603.22744-lh-bench.pdf`, `raw/papers/2603.18557-cross-lingual-judge.pdf`, `raw/papers/2601.22025-mves-framework.pdf`
- 新建摘要页:
  - wiki/sources/2603.20882-rubricrag.md — RubricRAG: 检索增强 Rubric 生成
  - wiki/sources/2603.25133-rubriceval.md — RubricEval: Rubric 级元评测基准
  - wiki/sources/2603.01562-rubricbench.md — RubricBench: LLM Rubric 与人类标准对齐
  - wiki/sources/2509.16093-dece.md — DeCE: 分解式 Precision+Recall 评测
  - wiki/sources/2603.21362-adarubric.md — AdaRubric: 任务自适应 Rubric（r=0.79, α=0.83）
  - wiki/sources/2603.22744-lh-bench.md — LH-Bench: 长周期 Agent 三层评测
  - wiki/sources/2603.18557-cross-lingual-judge.md — Cross-Lingual Judge: UCS 跨语言迁移
  - wiki/sources/2601.22025-mves-framework.md — MVES: 评测驱动迭代框架
- 更新概念页:
  - wiki/concepts/llm-as-judge.md — 新增"评测分解与自适应"章节（DeCE/AdaRubric/UCS/LH-Bench/RubricEval/MVES）
  - wiki/concepts/rubric-based-evaluation.md — 新增"自动 Rubric 生成"章节（AdaRubric/RubricRAG/专家vs LLM rubric/元评测）
- 更新: index.md（总页面数 98→106，总资料数 73→81）
- 关键发现: 自适应维度生成贡献>骨干模型能力（AdaRubric +0.14r），分解评测 r 从 0.12→0.78（DeCE），专家 rubric κ=0.60>LLM κ=0.46（LH-Bench）

## [2026-04-22] aggregate | 6 个主题聚合概念页
- 目的: 消除 36 个孤立资料页，按主题建立导航聚合
- 新建页面:
  - wiki/concepts/multimodal-evaluation.md — 多模态大模型评测（8 个资料页聚合）
  - wiki/concepts/evaluation-frameworks.md — 评测框架与工具（12 个资料页聚合）
  - wiki/concepts/chinese-evaluation-benchmarks.md — 中文评测基准（5 个资料页聚合）
  - wiki/concepts/code-evaluation.md — 代码生成评测（3 个资料页聚合）
  - wiki/concepts/domain-specific-evaluation.md — 领域专项评测（9 个资料页聚合）
  - wiki/concepts/evaluation-methodology-foundations.md — 评测方法论基础（7 个资料页聚合）
- 更新页面: index.md, log.md
- 总页面数: 98

## [2026-04-22] lint | 健康检查与修复
- 发现问题: 57 个（5 CRITICAL, 30 IMPORTANT, 22 MINOR）
- 已修复:
  - **C-1**: 修复 digital-human-eval-rubric-v1.md 中 3 个断裂链接（补全 `../concepts/` 前缀）
  - **C-2**: 修复 rubicon-news.md YAML frontmatter 中文引号解析问题（双引号→单引号）
  - **C-3**: 修正 rubric-based-evaluation.md 中 criteria 数量描述（"2~3 条"→"~4.4 条"）
  - **C-4**: 更新 index.md 页面总数（85→92）
  - **C-5**: 修正 mme.md 发表会议（"NeurIPS 2025"→"arXiv 2023"）
  - 更新 4 个实体页 updated 日期（openai, harvey-ai, rubric-forge, scale-ai）
  - 新建 3 个缺失页面: google.md（实体）、lmsys.md（实体）、rlhf.md（概念）
  - 添加跨页面链接: openai→human-eval-repo/rewardbench, meta→llava/effective-long-context, llm-as-judge→prometheus-repo/alpaca-eval/fastchat/rlhf
- 待修复: 22 个 MINOR 问题（孤立资料页缺少入链）

## [2026-04-22] create | LMSYS / Stanford 实体页
- 新建页面: wiki/entities/lmsys.md
- 内容: LMSYS（UC Berkeley）与 Stanford 的评测贡献汇总——MT-Bench、Chatbot Arena、FastChat、AlpacaEval、HELM
- 更新页面: index.md, log.md

## [2026-04-22] ingest | 5 个评测框架 GitHub 仓库 README 批量摄取
- 资料路径: `raw/github/opencompass/README.md`, `raw/github/lm-evaluation-harness/README.md`, `raw/github/helm/README.md`, `raw/github/alpaca_eval/README.md`, `raw/github/FastChat/README.md`
- 摘要页:
  - wiki/sources/opencompass-repo.md -- OpenCompass 一站式评测平台（上海 AI Lab，70+ 数据集，多推理后端）
  - wiki/sources/lm-evaluation-harness-repo.md -- lm-evaluation-harness（EleutherAI，Open LLM Leaderboard 后端，60+ benchmark）
  - wiki/sources/helm-repo.md -- HELM 全面评测框架（Stanford CRFM，多维度指标，VHELM/MedHELM 扩展）
  - wiki/sources/alpaca-eval-repo.md -- AlpacaEval 自动评估器（Stanford Tatsu Lab，LC Win Rate，与 Arena 0.98 相关）
  - wiki/sources/fastchat-repo.md -- FastChat 平台（LMSYS，Chatbot Arena 底层，MT-Bench，Vicuna）
- 新建页面: opencompass-repo.md, lm-evaluation-harness-repo.md, helm-repo.md, alpaca-eval-repo.md, fastchat-repo.md
- 更新页面: index.md, log.md

## [2026-04-22] ingest | 5 个 GitHub 仓库 README 批量摄取
- 资料路径: `raw/github/ceval/README.md`, `raw/github/CLUE/README.md`, `raw/github/ChineseGLUE/README.md`, `raw/github/SuperCLUE/README.md`, `raw/github/prometheus/README.md`
- 摘要页:
  - wiki/sources/ceval-repo.md -- C-Eval 中文考试型评测基准（52 学科，13,948 题，NeurIPS 2023）
  - wiki/sources/clue-repo.md -- CLUE 中文语言理解评测基准（9 个 NLU 任务，COLING 2020）
  - wiki/sources/chineseglue-repo.md -- ChineseGLUE 经典版（13 个任务，CLUE 前身）
  - wiki/sources/superclue-repo.md -- SuperCLUE 中文大模型综合评测（四大象限，含 Agent 和安全）
  - wiki/sources/prometheus-repo.md -- Prometheus 开源评测 LM（Rubric 驱动，Feedback Collection 数据集）
- 新建页面: ceval-repo.md, clue-repo.md, chineseglue-repo.md, superclue-repo.md, prometheus-repo.md
- 更新页面: index.md, log.md

## [2026-04-05] init | Wiki 初始化
- 名称: LLM-Evaluation-Wiki
- 领域: 大模型评测（LLM Evaluation & Benchmarking）
- 描述: 面向大模型评测工作的知识库，持续积累评测方法论、基准测试、模型对比、行业动态和评测实践经验
- 语言: 中文为主
- 资料分类: 新闻、论文、聊天记录、评测集合与记录

## [2026-04-09] ingest | Rubric-Based 推理数据调研报告
- 资料路径: `raw/report/Rubric-Based推理数据调研报告.md`
- 备注: `raw/papers/` 下有同名副本，按用户要求仅处理 `raw/report/` 版本
- 摘要页: wiki/sources/rubric-based-reasoning-data-survey.md
- 新建页面: wiki/benchmarks/prbench.md, wiki/benchmarks/healthbench.md, wiki/benchmarks/biglaw-bench.md, wiki/benchmarks/advancedif.md, wiki/benchmarks/cnfinbench.md, wiki/entities/scale-ai.md, wiki/entities/openai.md, wiki/entities/harvey-ai.md, wiki/entities/meta.md, wiki/concepts/rubric-based-evaluation.md, wiki/concepts/llm-as-judge.md
- 更新页面: index.md, log.md

## [2026-04-09] ingest | Rubric-Forge：基于 Rubric 的 LLM 自动评分系统
- 资料路径: `raw/report/Rubric-Forge：基于 Rubric 的 LLM 自动评分系统.md`
- 摘要页: wiki/sources/rubric-forge.md
- 新建页面: wiki/entities/rubric-forge.md
- 更新页面: wiki/concepts/rubric-based-evaluation.md, wiki/concepts/llm-as-judge.md, index.md, log.md

## [2026-04-09] ingest | AdvancedIF 官方仓库
- 资料路径: `raw/benchmarks/AdvancedIF/README.md`, `raw/benchmarks/AdvancedIF/README_zh.md`
- 摘要页: wiki/sources/advancedif-repo.md
- 新建页面: （无）
- 更新页面: wiki/benchmarks/advancedif.md, wiki/entities/meta.md, wiki/concepts/rubric-based-evaluation.md, wiki/concepts/llm-as-judge.md, index.md, log.md

## [2026-04-09] ingest | PRBench 官方仓库
- 资料路径: `raw/benchmarks/PRBench/README.md`, `raw/benchmarks/PRBench/README_zh.md`
- 摘要页: wiki/sources/prbench-repo.md
- 新建页面: （无）
- 更新页面: wiki/benchmarks/prbench.md, wiki/entities/scale-ai.md, index.md, log.md

## [2026-04-09] ingest | BigLaw Bench 官方仓库
- 资料路径: `raw/benchmarks/biglaw-bench/README.md` 及子目录 README
- 摘要页: wiki/sources/biglaw-bench-repo.md
- 新建页面: （无）
- 更新页面: wiki/benchmarks/biglaw-bench.md, wiki/entities/harvey-ai.md, wiki/concepts/rubric-based-evaluation.md, index.md, log.md

## [2026-04-09] ingest | OpenAI simple-evals 仓库（含 HealthBench）
- 资料路径: `raw/benchmarks/HealthBench-simple-evals/README.md`, `README_zh.md`, `multilingual_mmlu_benchmark_results.md`
- 摘要页: wiki/sources/simple-evals-repo.md
- 新建页面: （无）
- 更新页面: wiki/benchmarks/healthbench.md, wiki/entities/openai.md, wiki/entities/meta.md, wiki/concepts/llm-as-judge.md, index.md, log.md

## [2026-04-09] ingest | 392 语音助手评测集
- 资料路径: `raw/benchmarks/392toolkit/`（从 `/Users/lichao/Downloads/392toolkit` 移入）
- 备注: 百度内部语音助手评测集合，392 条 query，含策略库、黑盒/白盒/超黑盒评测数据
- 摘要页: wiki/sources/392toolkit.md
- 新建页面: wiki/benchmarks/392-voice-assistant.md
- 更新页面: wiki/concepts/rubric-based-evaluation.md, index.md, log.md

## [2026-04-09] query | 392 Rubrics 可用性提升分析
- 问题: "为了提高 392 的 rubrics 可用性，应该从哪些方面进行提升？"
- 参考页面: wiki/benchmarks/392-voice-assistant.md, wiki/sources/392toolkit.md, wiki/concepts/rubric-based-evaluation.md, wiki/sources/rubric-forge.md, wiki/sources/rubric-based-reasoning-data-survey.md
- 答案保存: wiki/analyses/392-rubric-improvement.md
- 核心结论: 五大方向（原子化拆分→模板化→统一评分→校准闭环→能力标签），建议先以 q1-q30 做 pilot

## [2026-04-16] query | 数字人视频重建质量评测方法
- 问题: "视频重建的自动化评估方法都有什么？需要一套业界可执行的评测方法来评测数字人视频重建质量"
- 场景: 数字人 caption→JSON→生成视频→与原始视频一致性评估；几百条30秒视频，内部几十人评测+VLM评测
- 参考页面: 无（新领域，基于业界综述整理）
- 答案保存: wiki/concepts/digital-human-video-evaluation.md
- 覆盖内容: 七大评测维度体系、自动化指标（全参考/数字人专用/时序/无参考四层）、人工评测（MOS/成对对比/ABX）、VLM-as-Judge 范式、业界方案参考、常用测试集、五大趋势

## [2026-04-16] query | 数字人视频重建人工评测量表设计
- 问题: "基于 caption 四层结构规范，设计可落地的人工评测量表"
- 输入资料: Clippings/规范_全局情绪0414.md（caption 规范）、Clippings/切片抽帧_描述与重建.md（测试样例）
- 参考页面: wiki/concepts/digital-human-video-evaluation.md
- 答案保存: wiki/analyses/digital-human-eval-rubric-v1.md
- 设计要点: 8+1 维度（全局属性/身份保持/情绪一致性/面部朝向/视线头颈/五官档位/微表情/唇同步+总评）、1-5 分制、校准培训方案、加权总分、VLM 衔接预留

## [2026-04-21] ingest | 批量摄取 raw/papers/ 下 40 篇论文
- 资料路径: `raw/papers/*.pdf`（41 个 PDF，1 篇不相关已跳过）
- 跳过: `tecipa-542.pdf`（经济学论文，与 LLM 评测无关）
- 新建摘要页（39 个）:
  - **LLM 评测综述与方法论**: helm.md, llm-eval-survey-chang.md, llm-eval-survey-future-internet.md, hallucination-survey.md
  - **LLM-as-Judge / Rubric**: chatgpt-human-eval-alternative.md, llmbar.md, prometheus.md, prometheus-2.md, rubric-is-all-you-need.md, rl-rubric-anchors.md
  - **指令遵循与对齐**: ifeval.md, effective-long-context.md, rewardbench.md, agieval.md, 2411-recent-eval.md (Tulu 3)
  - **多模态评测**: mme.md, mmbench.md, mm-vet.md, mathvista.md, lvlm-ehub.md, vlmevalkit.md, evalcrafter.md
  - **领域评测**: pixiu-flare.md, gpt-mt-evaluation.md, polycoder-code-eval.md, classeval.md, dramatron.md
  - **医学评测**: chatgpt-medical-accuracy.md, llm-clinical-decision.md
  - **Rubric 教育方法论**: 1-s2.0-scoring-rubrics-formative.md, rubrics-learning-goals.md, teaching-with-rubrics.md, rubric-design-education.md, scoring-rubrics-what-when-how.md, whats-wrong-right-rubrics.md
  - **评估统计方法**: interrater-reliability.md
  - **ML 评测方法论**: clinical-prediction-model-eval.md, ml-model-evaluation.md, overfitting-model-tuning.md
- 更新页面: index.md, log.md
- 备注: 本轮仅创建资料摘要页，未对已有 entity/concept/benchmark 页面做逐一更新

## [2026-04-21] clone | 批量 clone 18 个 GitHub 仓库到 raw/github/
- 新 clone:
  - Awesome-LLM-Long-Context-Modeling, alpaca_eval, beir, evaluate (huggingface), EasyLM, evalscope
  - helm (stanford-crfm), promptbench, LLMZoo, human-eval, SuperCLUE, fast-agent
  - VLMEvalKit, CLUE, ParlAI, opencompass, ToolBench, transformerlab-app
- 已有（跳过）: inspect_ai
- 路径: `raw/github/`，共 28 个仓库
- 备注: 仅 clone，未 ingest

## [2026-04-21] ingest | 高引用论文补充摄取（Step 3）
- 资料路径: `raw/papers/` 下 5 篇跨引用最多的评测相关论文
- 新建摘要页:
  - wiki/sources/mt-bench-chatbot-arena.md — MT-Bench & Chatbot Arena (2306.05685)
  - wiki/sources/gpt4-technical-report.md — GPT-4 Technical Report (2303.08774)
  - wiki/sources/instructgpt.md — InstructGPT / RLHF (2203.02155)
  - wiki/sources/chain-of-thought.md — Chain-of-Thought Prompting (2201.11903)
  - wiki/sources/mmlu.md — MMLU 多任务理解基准 (2009.03300)
- 更新页面: index.md, log.md

## [2026-04-22] ingest | 3 篇新论文精读摄取
- 资料路径: `raw/papers/2301.12597-blip2.pdf`, `raw/papers/2304.08485-llava.pdf`, `raw/papers/2307.09288-llama2.pdf`
- 新建摘要页:
  - wiki/sources/blip2.md — BLIP-2: Q-Former 视觉语言预训练 (Salesforce, ICML 2023)
  - wiki/sources/llava.md — LLaVA: 视觉指令微调 (NeurIPS 2023)
  - wiki/sources/llama2.md — Llama 2: 开源基础和对话模型 (Meta, 2023)
- 更新页面: wiki/entities/meta.md, wiki/entities/openai.md, wiki/concepts/llm-as-judge.md, wiki/concepts/rubric-based-evaluation.md, index.md, log.md

## [2026-04-22] ingest | 1 篇新闻 + 1 篇博客摄取
- 资料路径:
  - `raw/news  /蚂蚁浙大提出基于"评分细则"（Rubric）的奖励机制...md`
  - `raw/blog/论文评述 Rubric Is All You Need...md`
- 新建摘要页:
  - wiki/sources/rubicon-news.md — Rubicon 框架新闻报道（补充 rl-rubric-anchors.md）
  - wiki/sources/rubric-all-you-need-blog.md — Rubric Is All You Need 博客评述
- 更新页面: wiki/concepts/rubric-based-evaluation.md, index.md, log.md

## [2026-04-22] ingest | 批量摄取 17 个 GitHub 评测仓库
- 资料路径: `raw/github/` 下 17 个仓库的 README
- 新建摘要页（17 个）:
  - **评测框架**: opencompass-repo.md, lm-evaluation-harness-repo.md, helm-repo.md, evalscope-repo.md, inspect-ai-repo.md
  - **LLM-as-Judge 评测**: alpaca-eval-repo.md, fastchat-repo.md, prometheus-repo.md
  - **代码与检索评测**: human-eval-repo.md, beir-repo.md
  - **Prompt 评测**: promptbench-repo.md
  - **中文评测**: ceval-repo.md, clue-repo.md, chineseglue-repo.md, superclue-repo.md
  - **Rubric 工具**: rubric-lib-repo.md, dr-tulu-repo.md
- 跳过（与评测关联较弱）: EasyLM, LLMZoo, Awesome-LLM-Long-Context-Modeling, ToolBench, ParlAI, fast-agent, miles, training_extensions, transformerlab-app
- 更新页面: index.md, log.md

## [2026-04-22] update | 下游页面更新
- 基于新摄取资料更新已有页面:
  - wiki/entities/meta.md — 添加 Llama 2 信息和评测数据
  - wiki/entities/openai.md — 添加 InstructGPT、GPT-4 论文链接
  - wiki/concepts/llm-as-judge.md — 添加 MT-Bench 里程碑、LLaVA 多模态 Judge、Prometheus 开源 Judge、Rubric 库
  - wiki/concepts/rubric-based-evaluation.md — 添加 Rubicon/DR Tulu RL 应用、教育领域研究、Rubric 库

## [2026-05-06] ingest | TTS-PRISM: 感知推理语音诊断模型
- 资料路径: `raw/papers/tts-prism.pdf` + `raw/github/tts-prism/`
- 摘要页: wiki/sources/tts-prism.md
- 新建页面: wiki/entities/xiaomi.md
- 更新页面: wiki/concepts/rubric-based-evaluation.md（添加跨模态语音评测章节）, wiki/concepts/llm-as-judge.md（添加语音 Judge 模型段落）, index.md, log.md

## [2026-08-07] ingest | 众包质量估计 / 标注流水线相关论文 5 篇
- 新增 PDF (5): 1605.04481-anchoring-and-agreement-in-syntactic-annotations.pdf, aij2013-turkontrol-pomdp-control-crowdsourcing-workflows.pdf, jmlr2016-spectral-methods-meet-em-optimal-crowdsourcing.pdf, pvldb2015-argonaut-macrotask-crowdsourcing-complex-data-processing.pdf, hcomp2013-baba-kashima-statistical-quality-estimation-crowdsourcing.pdf
- 归属概念页: wiki/concepts/annotation-noise-and-pipeline-quality.md
- 更新页面: index.md, log.md

## [2026-08-10] ingest | 付费墙论文假设集核实（Tai & Zhou / Sato & Miyazawa / Hansen et al.）
- Tai & Zhou (AoAS 2025, 10.1214/25-AOAS2081): 其 Assumption 2 **要求** index test 与不完美参考标准条件独立（T(CST) 与 S(amyloid PET)），辩护理由「T and S derive information from distinct sources」，模拟 Scenario G 专门用 corr(T,S)=0.355 违背它。高置信但非全文确证（证据为已索引全文片段 + 出版页元数据）
- Sato & Miyazawa (LRE 2022, 10.1007/s10579-022-09617-0): **含 review 阶段，已确证**（LREC 2020 前身全文 2020.lrec-1.29 + Springer 图注 + 官方代码输入格式 creation.tsv / review.tsv 三方交叉）；reviewer 看 artifact 且被刻意盲化（不告知 creator 意图）、review 全覆盖无抽检
- Hansen et al. (CSCW 2013, 10.1145/2441776.2441848): **未获全文**。ACM DL/ResearchGate 订阅墙、BYU ScholarsArchive 站内检索 HTTP 500、Unpaywall/OpenAlex/S2 三方均 closed。间接证据（摘要 + 59 篇引文上下文）指向 field experiment 式机制对比、无建模痕迹，按铁律不下断言
- 新增 PDF (2): aoas2025-tai-zhou-simultaneous-adjustment-verification-imperfect-gold-firstpage.pdf（首页预览）, lrec2020-sato-miyazawa-quality-estimation-partially-subjective-classification.pdf（全文 CC-BY-NC）
- 更新页面: index.md, log.md

## [2026-08-12] ingest | 阈值处操纵与 bunching 检验相关论文 8 篇
- 主题: 监控统计量在公开阈值处被扭曲的跨领域证据与检验工具（教育考试评分、环境审计、汽车检测、实验室检测），以及 bunching / McCrary 密度检验一族方法
- 关键书目核实: Dee, Dobbie, Jacob & Rockoff 的期刊版为 **AEJ: Applied 11(3):382–423, 2019**（非仅 NBER w22165）；Blomquist, Newey, Kumar & Liang 的期刊版为 **JPE 129(8):2320–2343, 2021**，且其结论方向为**负面**——「阈值处的堆积本身不构成识别」，故 bunching 是可用的**检验**指纹而非识别杠杆
- 另核实通过: QE 11(3):839–870 (Gerard-Rokkanen-Rothe, 操纵下 sharp bounds) ✓、J. Law & Econ 33(1):233–276 (Feinstein, detection controlled estimation) ✓、QJE 128(4):1499–1545 (Duflo-Greenstone-Pande-Ryan, 第三方审计者) ✓
- 新增 PDF (8): w22165-causes-consequences-test-score-manipulation-regents.pdf, w22207-teacher-discretion-grading-high-stakes-tests.pdf, w19259-truth-telling-third-party-auditors-india.pdf, w24136-on-bunching-and-identification-of-taxable-income-elasticity.pdf, mccrary-2007-manipulation-running-variable-density-test-nber-t0334.pdf, bokhove-2023-are-some-school-inspectors-more-lenient-than-others.pdf, zoorob-2021-thc-concentration-discontinuity-20-percent-threshold.pdf, checco-2020-adversarial-attacks-on-crowdsourcing-quality-control.pdf
- 检索条件（**必须随结论一并引用**）: WebSearch 全会话配额已耗尽，Google Scholar 不可用，Semantic Scholar 持续 429。全部 165 条查询（OpenAlex 91 / Crossref 52 / arXiv 22）走 API **标题与摘要字段**匹配，**无任何全文检索通道**。故本轮「未检索到」不得被引为「该方向为学术空白」
- 遗留缺口: ① 会计/OB「主管评分宽容化」是否有门限版本（找到 Bol TAR 86:1549–1575, 2011 等核心文献，但摘要无阈值堆积内容）② arXiv `peer prediction` + `external incentives` 查询 429 未取回 ③ MSA / Gage R&R attribute agreement analysis 仍未查成
- 归属概念页: wiki/concepts/evaluation-awareness-and-gaming.md
- 更新页面: index.md, log.md
