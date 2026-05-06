# Wiki 操作日志

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
