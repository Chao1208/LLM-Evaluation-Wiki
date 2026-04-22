# raw/github/ — 已下载的 GitHub 仓库

> 本目录存放从 GitHub 克隆的开源仓库，作为 Wiki 的参考资料。
> 另有 4 个评测基准仓库位于 `raw/benchmarks/` 目录下（AdvancedIF、biglaw-bench、HealthBench-simple-evals、PRBench）。

共 28 个仓库，按用途分类如下。

## 评测框架与工具 (Frameworks & Tools)

| 仓库 | 简介 | 地址 |
|------|------|------|
| opencompass | 上海 AI Lab 一站式评测平台，70+ 数据集、20+ 模型支持 | https://github.com/open-compass/opencompass |
| lm-evaluation-harness | EleutherAI 评测框架，60+ benchmark，HuggingFace Open LLM Leaderboard 后端 | https://github.com/EleutherAI/lm-evaluation-harness |
| helm | Stanford CRFM 全面评测框架，42 场景 × 7 指标类别 | https://github.com/stanford-crfm/helm |
| evalscope | 阿里/ModelScope 多后端评测框架，50+ 基准，支持 LLM/VLM/Agent/Audio | https://github.com/modelscope/evalscope |
| inspect_ai | UK AISI 评测框架，200+ 预置评测，可组合组件，政府级 AI 安全评测 | https://github.com/UKGovernmentBEIS/inspect_ai |
| alpaca_eval | Stanford Tatsu Lab LLM-as-Judge 成对评测工具，LC Win Rate 与 Arena 相关 0.98 | https://github.com/tatsu-lab/alpaca_eval |
| FastChat | LMSYS/UC Berkeley 平台，Chatbot Arena 底层，MT-Bench 评测 | https://github.com/lm-sys/FastChat |
| promptbench | Microsoft Prompt 鲁棒性评测，4 级对抗攻击，DyVal 动态评测 | https://github.com/microsoftarchive/promptbench |
| evaluate | HuggingFace 评测指标库，统一接口调用各类 NLP 评测指标 | https://github.com/huggingface/evaluate |
| VLMEvalKit | 上海 AI Lab 视觉语言模型评测工具包，模块化设计 | https://github.com/open-compass/VLMEvalKit |

## 中文评测基准 (Chinese Benchmarks)

| 仓库 | 简介 | 地址 |
|------|------|------|
| ceval | SJTU-LIT C-Eval，13,948 道多选题覆盖 52 学科，NeurIPS 2023 | https://github.com/hkust-nlp/ceval |
| CLUE | CLUEbenchmark 中文语言理解基准，9 个 NLU 任务 + 14G 语料库 | https://github.com/CLUEbenchmark/CLUE |
| ChineseGLUE | ChineseGLUE 中文 NLU 基准先驱，13 个任务经典版 | https://github.com/ChineseGLUE/ChineseGLUE |
| SuperCLUE | 中文大模型综合评测，四大象限十大能力，含 Agent 和安全 | https://github.com/CLUEbenchmark/SuperCLUE |

## LLM-as-Judge / Rubric 评测 (Judge & Rubric)

| 仓库 | 简介 | 地址 |
|------|------|------|
| prometheus | KAIST AI 开源评测 LM，Rubric 驱动细粒度评测，Feedback Collection 数据集 | https://github.com/prometheus-eval/prometheus |
| rubric | The LLM Data Company 加权 Rubric 评测 Python 库，三种评分策略 | https://github.com/The-LLM-Data-Company/rubric |
| dr-tulu | Ai2 演化 Rubric RL 训练深度研究 agent，8B 模型匹配 OpenAI DR | https://github.com/rlresearch/dr-tulu |

## 代码/检索评测 (Code & Retrieval)

| 仓库 | 简介 | 地址 |
|------|------|------|
| human-eval | OpenAI HumanEval，164 道 Python 代码生成题，pass@k 指标 | https://github.com/openai/human-eval |
| beir | TU Darmstadt BEIR，17 数据集信息检索评测基准，零样本迁移 | https://github.com/beir-cellar/beir |
| ToolBench | OpenBMB 工具调用评测基准，大规模 API 调用能力评测 | https://github.com/OpenBMB/ToolBench |

## 模型训练与服务 (Training & Serving)

| 仓库 | 简介 | 地址 |
|------|------|------|
| EasyLM | JAX/Flax LLM 训练框架，支持数百 TPU/GPU 扩展 | https://github.com/young-geng/EasyLM |
| miles | Radixark 企业级 RL 训练框架，大规模模型强化学习 | https://github.com/radixark/miles |
| training_extensions | OpenVINO 训练扩展，边缘平台模型训练优化 | https://github.com/open-edge-platform/training_extensions |
| transformerlab-app | Transformer Lab 桌面应用，LLM 实验/微调/评测一体化 | https://github.com/transformerlab/transformerlab-app |

## Agent 与对话 (Agent & Dialog)

| 仓库 | 简介 | 地址 |
|------|------|------|
| fast-agent | evalstate MCP Agent 框架，快速构建 AI Agent 应用 | https://github.com/evalstate/fast-agent |
| ParlAI | Meta 对话 AI 研究平台，统一对话任务训练与评测 | https://github.com/facebookresearch/ParlAI |

## 其他资源 (Others)

| 仓库 | 简介 | 地址 |
|------|------|------|
| LLMZoo | FreedomIntelligence LLM 模型动物园，多语言开源模型集合 | https://github.com/FreedomIntelligence/LLMZoo |
| Awesome-LLM-Long-Context-Modeling | 长上下文建模论文/博客集合，持续更新 | https://github.com/Xnhyacinth/Awesome-LLM-Long-Context-Modeling |
