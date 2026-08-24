# Wiki 操作日志

## [2026-08-21] ingest | 排序部分识别两篇全文核（Crippa & Fedchenko 2410.18272 / Zhang et al. 2201.13132）
- 新增 PDF (0，均已在库，本轮首次逐字全文读)：`2410.18272-partially-identified-rankings-from-pairwise-interactions.pdf`（970644 字节，10 pages/pdftotext 提取 29 页正文，MD5 2e394c58a8c9c06a759770a2f19a869a）、`2201.13132-on-the-identifiability-of-mixtures-of-ranking-models.pdf`（650849 字节，44 pages，MD5 b40c60aae659eaf3b94248459d89e5a4）
- 新建 source 页 (2): `wiki/sources/2410.18272-partially-identified-rankings-from-pairwise-interactions.md`、`wiki/sources/2201.13132-identifiability-of-mixtures-of-ranking-models.md`
- 关键发现（领域知识，不含立论）：Crippa & Fedchenko 的部分识别来源是**图不完整**（观测量在被观测边上精确无偏），非参数模型下需图直径≤2 才点识别，锐识别集用 moment-inequality 形式刻画并给出似然比检验；额外施加 strong stochastic transitivity 比单纯传递闭包能认证更多对。Zhang et al. 的两组分 BTL/MNL/PL 混合一般可识别性用代数几何（Gröbner 基+Zariski 闭集）框架证明，混合权重是待点识别连续参数、不设上界、不用传递性反推
- 归属概念页: 无新建概念页，两篇均归为 sources 独立摘要
- 更新页面: index.md（资料摘要表新增 2 行）、log.md
- 检索: Semantic Scholar `/citations` of `arXiv:1806.02051` ⇒ **402 篇（未截断）**；OpenAlex `cites:W2902154261` ⇒ **382 篇**；两源并集按标题归一化去重后 **449 篇不同工作**（年份 2018–2026，摘要覆盖 78.2%）
- 新增 PDF (2): `foucart-2021-processing-multi-expert-annotations-digital-pathology-gleason-2019.pdf`、`foucart-2023-evaluating-participating-methods-image-analysis-challenges-monusac-2020.pdf`（均来自 ULB 机构库 dipot.ulb.ac.be，GREEN/hybrid OA）；`2307.02191-…-dermatology.pdf` 查重发现**早已在库**，本轮首次逐页读
- 新建 source 页 (1): `wiki/sources/2307.02191-evaluating-ai-systems-under-uncertain-ground-truth.md`
- 关键发现:
  * **「不确定金标 ⇒ 排序会变」已由 DeepMind 一支在 Medical Image Analysis 2025 写下**（Stutz 等）：统计聚合（PrIRN / Plackett–Luce）+ uncertainty-adjusted 指标；可靠度参数 γ 显式等价于标注者数量；**该文的附加案例包含 CIFAR-10H 与 AVA** ⇒ CIFAR-10H 作为「标注不确定性感知评价」的底材已被使用过
  * **共识方法（STAPLE / majority vote / weighted vote）会改变「专家」的排序**（Foucart 等 2021, Gleason2019 数据；各专家与共识的 unweighted κ 为 0.454–0.711）；⚠️ 对**算法**排序的后果原文只写作可能性（"may in turn influence"），未实测
  * **排序不稳定性至少有三个独立来源**：参考标注的观察者身份（Maier-Hein 2018）、测试数据重抽（同文 bootstrap）、**参赛方法描述的歧义**（Reinke, Gotkowski, Maier-Hein, BVM 2023《Challenge Results Are Not Reproducible》：重实现 ROBUST-MIS 2019 后排行榜"substantially"不同）
  * 该社群已把排序稳定性分析**工具化**：`challengeR`（Wiesenfarth 等, Scientific Reports 2021, arXiv:1910.05121），并有两篇 Nature Methods 共识文（Metrics Reloaded, arXiv:2206.01653；Understanding metric-related pitfalls, arXiv:2302.01790）
  * Maier-Hein 2018 三组数字**已逐字核回原文 PDF 第 10 页**：观察者两两比较改变排序的比例 15%/46%/62%（分别对应 DSC/HD/HD95，基于 13 组两两比较），Kendall τ 区间 [0.78,1] / [-0.02,1] / [0.07,1]；⚠️ 而「冠军仍是冠军的任务仅 21%/11%/9%」属于**测试数据 bootstrap**、与标注者无关；同页另有「留一个测试样例出去，非冠军队排第一的情形最高 16%，某任务达 67%」
- 检索条件（**必须随结论一并引用**）: 本轮 **WebSearch 零调用**（会话配额 200/200 耗尽）；**OpenReview 连续两轮不可达**（超时 / 403 人机验证）；**DBLP / Scopus / WoS / Google Scholar 四源未走**。故「未检索到」只能读作「在两源并集 + 78% 摘要覆盖的条件下未见」
- 遗留缺口: ① Foucart 等《Shortcomings and areas for improvement in digital pathology image segmentation challenges》(CMIG 2023, DOI 10.1016/j.compmedimag.2022.102155) 摘要提到 inter-expert disagreement 处理的缺陷，**未获全文**（ULB handle 2013/352391 的 bitstream 未定位） ② Reinke 等《Understanding metric-related pitfalls》**未获全文**（PMC 不可连、arODES 镜像返 0 字节，该空文件已删除，未留坏文件） ③ Stutz 等一支的前向引文未查
- 归属概念页: wiki/concepts/annotation-noise-and-pipeline-quality.md
- 更新页面: index.md, log.md

## [2026-08-20] ingest | 标注者变异导致排行榜重排：跨领域实测两篇
- 新增 PDF (7): `1806.02051-why-rankings-of-biomedical-image-analysis-competitions-should-be-interpreted-with-care.pdf`, `2509.22242-clinical-uncertainty-impacts-machine-learning-evaluations.pdf`, `2605.04624-auditrepairbench-paired-execution-trace-corpus-evaluator-channel-ranking-instability.pdf`, `1908.07086-human-uncertainty-makes-classification-more-robust.pdf`, `2010.03532-chaosnli-what-can-we-learn-from-collective-human-opinions-on-nli-data.pdf`, `2404.02112-imagenot-a-contrast-with-imagenet-preserves-model-rankings.pdf`（+ 4 篇查重命中、未重复下载）
- 新建 source 页 (2): `wiki/sources/1806.02051-why-rankings-of-biomedical-challenges-interpreted-with-care.md`, `wiki/sources/2509.22242-clinical-uncertainty-impacts-ml-evaluations.md`
- 关键发现:
  * **「换参考标注者会换冠军」在 2018 年已有大样本实测**（Nature Communications）：13 组两两观察者比较中 15%/46%/62% 产生不同排序，Kendall τ 区间 [0.78,1] / [−0.02,1] / [0.07,1]；同文 bootstrap 显示 DSC/HD/HD95 下「冠军仍是冠军」的任务只占 21%/11%/9%
  * **把标注置信度写进指标同样会重排名次**（ML4H 2025）：s-AUROC / s-AP 与普通指标的排序在 CheXpert-Pneumothorax 上 R² 仅 0.465（同任务 AP 为 0.983），VinDr 多任务换冠军；1,000 次标注 bootstrap 下软指标排序更稳
  * **报告缺失是这类风险不可评估的根因**：66% 的挑战赛任务未描述参考标注如何产生、45% 有多标注者的任务未说明聚合方式
  * `arXiv:2605.04624`（AuditRepairBench）标题里的 “evaluator-channel ranking instability” 指的是**方法内部 selector 消费 evaluator 派生信号**造成的重排，与真人标注通道无关；该文 **v2（2026-07-24）已由作者撤稿**，理由为实验设计与评价存在影响主结论有效性的重大问题
  * 底材事实核对：CIFAR-10H 的真人软标签在 CIFAR-10 **测试集**（10,000 图、511,400 判定、均 51 判定/图）；CIFAR-10N 的真人硬标签在 **训练集**（50,000 图、每图 3 名标注者、噪声率 9.03%–40.21%）；ChaosNLI 为 SNLI/MNLI 3,113 例 + αNLI 1,532 例、每例 100 条标注
- 检索条件（**必须随结论一并引用**）: 本轮 **WebSearch 零调用**（配额耗尽），**OpenReview 全通道不可达**（api 超时 / api2 返 403 challenge / pdf 超时 / WebFetch ECONNREFUSED）；结论走 arXiv API、export.arxiv.org、arXiv HTML 全文、Semantic Scholar `/citations` 与 search、OpenAlex。故本轮「未检索到」不得被引为「该方向为学术空白」
- 遗留缺口: ① Gordon et al. 2021 *The Disagreement Deconvolution*（CHI, DOI 10.1145/3411764.3445423）无开放版本，未获全文 ② Reinke et al. 2024 指标共识文未检索 ③ CIFAR-10H 公开发布文件是否携带 annotator id 未核（决定能否做标注者重抽）
- 归属概念页: wiki/concepts/annotation-noise-and-pipeline-quality.md
- 更新页面: index.md, log.md

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

## [2026-08-22] ingest | 定性矩阵理论 / sign-solvability 概念族 + 排序侧对偶锥刻画
- 主题: 只知符号、幅度完全任意（`Q(A)` ＝ 所有同符号模式实矩阵，**无任何有界性假设**）时线性系统性质的可决定性；概念族 sign-solvable / partial sign-solvable / conditionally sign-solvable / sign inconsistent / sign-consistency + 定性 Farkas / sign-central / unambiguous entry
- 关键书目核实: Maybee & Quirk 1969 **SIAM Review 11(1):30–51, DOI 10.1137/1011004**（Zbl 0186.33503）；Bassett, Maybee & Quirk 1968 **Econometrica 36(3):544–563, DOI 10.2307/1909522**（Zbl 0217.26802）；Brualdi & Shader 1995 专著 13 章页码全部经 Crossref 确证（前缀 10.1017/cbo9780511574733）；Stein, Mizzi & Pfaffenberger 1994 **EJOR 74(1):78–85**（其 Theorem 1(a)(b) 经 Viappiani 2020 Prop.1/2 逐字转述）
- 否证性结论: zbMATH 全库检索 `sign-semi-solvable`、`one-sided sign-solvability` 均 0 条 ⇒ 该支无「单侧蕴含」专有术语；`sign-solvability voting` 0 条 ⇒ 未见该支用于投票/排序/评测
- 新增 PDF (3): lee-1998-sign-consistency-and-solvability-of-constrained-linear-systems.pdf（ELA 4:1–18, 18 页, OA）, viappiani-2020-robust-winner-determination-in-positional-scoring-rules-with-uncertain-weights.pdf（29 页）, viappiani-2018-positional-scoring-rules-with-uncertain-weights.pdf（15 页）
- 未获全文: Brualdi & Shader 1995 专著、BMQ 1968、Maybee-Quirk 1969、Klee-Ladner-Manber 1984、Stein et al. 1994、Handbook of Linear Algebra ch.33（付费墙；本页相关陈述来自 zbMATH 书评与 Lee-Shader 的引用转述）
- 检索条件（**必须随结论一并引用**）: 无 WebSearch 配额；Crossref 题录 22 条 + zbMATH Open API 19 条 + Unpaywall 13 个 DOI + arXiv 2 条；fatcat API 5 次全超时、Google Books API 5 次全 429、assets.cambridge.org 超时。故「未检索到」不得被引为「该方向空白」
- 通道技巧: HAL（Anubis 反爬）用 **curl 默认 UA**（不含 `Mozilla`）可直下 PDF；带 `-A "Mozilla/5.0"` 一律返回 12 KB JS 挑战页
- 遗留缺口: ① 专著第 1 章 pp.1–17 一页未读 ② Klee-Ladner-Manber 1984 与 asymptotic sign-solvability (2006) 判定对象待补 ③ Salo 1995 EJOR（Viappiani 称其 dominance 框架为上游）未查 ④ Zenklusen 2009「sign-central 的 LP 对偶证明」（10.1007/978-3-642-00142-0_88）未获
- 归属概念页: wiki/concepts/sign-solvability-qualitative-matrix-theory.md
- 更新页面: index.md, log.md

## [2026-08-22] ingest | Horowitz & Manski 1992 全文精读（§3–§6 首次逐页读完）+ 验证子样本支线题录
- 主题: contaminated / corrupted sampling 下分布与泛函的 tight 界；identification breakdown 与 robust breakdown point 的关系；「箱＝逐坐标区间乘积」为外界而非锐识别集；plug-in `τ(Qₙ)` 在识别分析中的地位
- 一手读取范围: 在库 PDF 共 36 页（印刷 33 页，**印刷页码 = PDF 页序 − 2**）。印刷第 7、10–23 页用 `pdftoppm -r 150 -png` 渲染成图像后**逐页一手读完**（含 §3.1 分位数、§3.2 随机占优、§4 无穷小识别分析、§5 估计与 CPS 实证、§6 讨论全篇）；印刷 1–6、8–9 页读 `pdftotext -layout`；附录（印刷 24–33 页）只读 OCR，**未看图像**
- 关键勘误（对该 PDF 的 OCR）: 该文件是**扫描件 + OCR**（Creator `HP Smart Document Scan Software 3.80`），λ 常被识别成 `l`，**式 (12a) 整行在 `pdftotext` 输出中丢失** ⇒ 读公式必须渲染图像。式 (12a) 的印刷原文为 `1 − λ ≤ Q(A) ≤ λ ⇒ Ψ₁₁(A;λ) = Ψ₁(A;λ) = [0,1]`
- 概念区分（印刷第 11 页一句话内同时给出，易混读）: **trivial**（识别区间退化为 [0,1]，**双边**触界）⟺ `1 − λ ≤ Q(A) ≤ λ`，需 λ ≥ 1/2；**identification breakdown**（**单边**触界即算）点 = `min[Q(A), 1 − Q(A)]`。差别源于 breakdown 的定义用了 *either* boundary（印刷第 8 页）
- 逐字要点: ① 命题 4 / 推论 4.1（印刷 13–14 页）给出「尊重随机占优的泛函」与「单调 g 的线性泛函 ∫g dψ」的 **tight** 界，corrupted 情形的界由 λ 质量退化为 `δ₋∞`／`δ∞` 点质量实现 ② 印刷第 11 页「not every function φ satisfying φ(A) ∈ Ψ₁₁(A;λ) is a probability distribution」⇒ 箱是外界 ③ 印刷第 23 页「τ(Q) is in the space of feasible values for τ(P₁)」且「estimation of τ(Q) yields **no information** on τ(P₁) beyond that contained in our tight bounds」
- 否证性结论（一手 `grep -ic`，全文正文）: `sign` **0**（唯二 2 次在文末 SSRI 工作论文清单里的 *DESIGN* / *SIGNAL*）、`same sign` 0、`box` 0、`rectangl` 0、`projection` 0、`affine` 0、`one-sided` 0、`known direction` 0、`Tenenbein` 0 ⇒ **该文没有任何「系数符号」型条件**；箱何时锐由**随机占优单调性**给出，不由符号给出
- 关键书目核实: 发表版为 **Horowitz & Manski, Econometrica 63(2):281–302, 1995, DOI 10.2307/2951627**（工作论文与发表版的命题编号对应关系**未核**）
- 后续支线（题录经 Crossref/OpenAlex 确证，**全文均未获**，Unpaywall 均 `is_oa: false`）: Dominitz & Sherman **JABES 9(3):319–338, 2004, DOI 10.1198/108571104x3389**「Sharp bounds under contaminated or corrupted sampling **with verification**」；Dominitz & Sherman **JAE 21:1295–1326, 2006, DOI 10.1002/jae.912**「mixture model **with verification**」（摘要已从 Crossref 一手取得：数据分为两集、其一为 verified set、用验证信息 + **单调性约束**界住学校表现的分歧区间）；Kreider & Pepper **JASA 102(478):432–441, 2007** 与 **JAE 23(3):329–349, 2008**；Molinari **J. Econometrics 144(1):81–117, 2008, DOI 10.1016/j.jeconom.2007.12.003**
- 新增 PDF (1): 2509.17269-distribution-testing-arbitrarily-dominant-noise-with-verification-queries.pdf（Black & Ye, cs.DS 2025-09-21，`pdfinfo Pages: 78`，1,324,301 B）——混合 `r = λp + (1−λ)q` 加「查询样本来自 p 还是 q」的 verification query 预言机，给出 uniformity/identity/closeness testing 的**样本-查询复杂度上下界匹配**；仅读摘要，正文未读
- 检索条件（**必须随结论一并引用**）: 无 WebSearch 配额（会话级 200/200 已耗尽）；OpenAlex `/works?search=` 当日预算耗尽（仅 DOI 直查可用）；Semantic Scholar `/paper/search` 第 4 次起 429。实际通道 = Crossref 题录 4 次 + arXiv API 4 次 + Unpaywall 2 个 DOI + OpenAlex/S2 各 1 个 DOI + S2 引文接口 2 次（返回 13 / 10 条）。arXiv `all:"corrupted sampling" AND all:"sharp bounds"` 与 `all:"known direction" AND all:"partial identification"` 均 `totalResults 0` ⇒ **只说明不在 arXiv，不得被引为该方向空白**
- 遗留缺口: ① Dominitz & Sherman 2004/2006 全文未获（可试作者主页 / RePEc / JAE data archive，⛔ 勿反复撞 Springer-Wiley）② 在库 `molinari-2008-…-cae-wp05-10.pdf` 与 `2004.11751-microeconometrics-with-partial-identification.pdf` 均**未通读**（后者本轮只读约 60 行）③ 在库 `2512.14616-estimating-program-participation-with-partial-validation.pdf` 未读 ④ 推论 5.1 的 (36)/(39) 是外界还是锐界，未定论（证明页只读 OCR）⑤ 非单调 g 的线性泛函精确锐界，1992 版未给，1995 版未核 ⑥ 医学诊断评价 / 审计抽样 / 心理测量三条镜像学科本轮一个查询都没发出
- 归属页面: wiki/sources/horowitz-manski-1992-identification-and-robustness-in-the-presence-of-errors-in-data.md
- 更新页面: index.md, log.md

## 2026-08-22 06:58 — 混合比例的单侧可识别性（MPE / PU learning / purity 谱系）

- 新增页面: `wiki/concepts/mixture-proportion-one-sided-identifiability.md`（概念页）
- 新增 PDF (6): `genovese-2004-a-stochastic-process-approach-to-false-discovery-control.pdf`（*AoS* 32(3):1035–1061, 2004，`pdfinfo Pages: 27`，221,906 B，Project Euclid 官方 PDF，§3.1 一手读）｜`blanchard-2010-semi-supervised-novelty-detection.pdf`（*JMLR* 11:2973–3009，Pages 37，291,502 B，jmlr.org 官方 PDF，§6 一手读）｜`1303.1208-…-maximal-denoising.pdf`（Pages 47，683,009 B，§1–5 一手读）｜`2306.01253-mixture-proportion-estimation-beyond-irreducibility.pdf`（ICML 2023 / PMLR 202，Pages 21，650,839 B，印刷页 1–3 一手读）｜`1811.04820-learning-from-positive-and-unlabeled-data-a-survey.pdf`（Pages 47，701,032 B，仅读 §8.3）｜`1710.01167-decontamination-of-mutual-contamination-models.pdf`（Pages 56，864,084 B，仅读摘要与 §2–3）｜另 `2111.00980-…-a-modern-approach.pdf`（NeurIPS 2021 Spotlight，Pages 27，4,500,777 B）**已落盘但仅读 arXiv 摘要**
- 关键谱系（一手核过印刷页码）: `κ` 的单侧可识别性最早成形于 **Genovese & Wasserman 2004 §3.1「Identifiability and purity」（印刷页 1042–1043）**，该文又把单侧界式 (15) 归给 Storey (2002) 与 Efron-Tibshirani-Storey-Tusher (2001)（**两篇未获全文、题录未核**）⇒ Blanchard-Lee-Scott *JMLR* 2010（印刷页 2989 明写 properness「recovers their notion of purity」）⇒ Scott-Blanchard-Handy COLT 2013 的 irreducibility / `κ*`
- 术语对照结论: FDR 侧的 `purity`、新奇检测侧的 `proper novelty distribution`、MPE 侧的 `irreducibility` 是同一条件的三个名字；ML 侧不用 `identified set` 一词（arXiv 标题＋摘要级 `all:"identified set" AND all:"mixture proportion"` 命中 **0**）⇒ 与计量侧基本互不引用
- 单向 vs 双向: 单向标签噪声（`ρ` 之一为 0）＝ PU learning，Bekker & Davis 印刷页 36 逐字给出 `one-sided label noise` 这个名字；双向情形两个比例的联合可行域是**闭四边形**（Scott 等 2013 Theorem 12(3)，印刷页 17），**不是乘积箱**
- 检索条件（**必须随结论一并引用**）: 无 WebSearch 配额（会话级 200/200）；OpenAlex `/works?search=` 当日预算耗尽（3/3 失败）；Semantic Scholar `/paper/search` 全部 429（3/3）；OpenReview `api2/notes/search` 3 次中 2 次超时。实际通道 = arXiv API 29 次 + Crossref 8 次。`all:"one-sided label noise"` 在 arXiv 命中 **0**，而该词组是 Bekker & Davis 的小节级标准术语 ⇒ **arXiv 摘要级检索会给假空白，任何「0 命中」不得被引为该方向空白**
- 遗留缺口: ① Storey 2002 / Efron 等 2001 全文与题录未取（最上游）② Menon-van Rooyen-Ong-Williamson ICML 2015 题录已由 `1303.1208` 印刷页 46 一手确证，**全文未获**（未试 PMLR v37 站内）③ `2111.00980` 未读 ④ `arXiv:1607.00071`（operator theoretic nonparametric mixture models）与 *EJS* 2022 `10.1214/22-ejs1987` 未读 ⑤ MPE × ranking / pairwise comparison 交集**未检索到**（五条 arXiv 交集查询全 0，但通道受限）⑥ 众包聚合（Dawid-Skene，annotator 混淆矩阵跨 item 共享）这条最接近「多个混合比例共享同一污染源」的支线本轮一个查询都没发出
- 归属页面: wiki/concepts/mixture-proportion-one-sided-identifiability.md
- 更新页面: index.md, log.md
