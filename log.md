# Wiki 操作日志

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
