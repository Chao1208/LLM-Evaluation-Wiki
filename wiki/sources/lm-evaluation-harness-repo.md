---
title: "lm-evaluation-harness 评测框架"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [评测框架, 开源工具, EleutherAI, 学术评测, HuggingFace, Open LLM Leaderboard]
sources: [raw/github/lm-evaluation-harness/README.md]
---

## 概述

lm-evaluation-harness 是由 EleutherAI 开发的统一语言模型评测框架，为生成式语言模型提供标准化的评测基准。该项目是 HuggingFace Open LLM Leaderboard 的评测后端，已被数百篇学术论文引用，被 NVIDIA、Cohere、BigScience、BigCode、Nous Research、Mosaic ML 等数十家机构内部使用。当前版本为 v0.4.0+。

项目 GitHub 地址为 `EleutherAI/lm-evaluation-harness`，PyPI 包名为 `lm_eval`。

## 关键特性

1. **丰富的 benchmark 覆盖**：60+ 标准学术 benchmark，数百个子任务和变体
2. **广泛的模型后端支持**：HuggingFace transformers（含 GPTQ/AutoGPTQ 量化）、GPT-NeoX、Megatron-DeepSpeed、vLLM、SGLang、NVIDIA NeMo、Megatron-LM、OpenVINO、IPEX、Habana（Intel Gaudi）、Neuron（AWS Inf2）、Windows ML（NPU/GPU/CPU）、Mamba SSM 等
3. **API 模型支持**：OpenAI（Completions & ChatCompletions）、Anthropic（含 Chat）、TextSynth、Cohere、本地 OpenAI 兼容服务器
4. **模块化安装**：v0.4.0 后基础包不再包含 `transformers`/`torch`，模型后端需单独安装（如 `pip install lm_eval[hf]`、`lm_eval[vllm]`、`lm_eval[api]`）
5. **灵活的 prompt 设计**：支持 Jinja2 prompt 模板、Promptsource 导入、自定义 prompt
6. **YAML 配置驱动的任务系统**：通过 YAML 配置文件创建和定义评测任务，支持外部定义的任务配置导入和共享
7. **CLI 重构**：子命令设计（`run`、`ls`、`validate`），支持 YAML 配置文件（`--config`）
8. **高级评测控制**：输出后处理、答案提取、多次生成、可配置 few-shot 设置
9. **多 GPU 并行**：支持数据并行（accelerate launcher）、模型并行（device_map）、混合并行模式
10. **Steering vectors 支持**：可对 HuggingFace 模型施加转向向量后评测
11. **结果可视化**：集成 Zeno 和 Weights & Biases 可视化工具
12. **结果缓存与复用**：`--use_cache` 和 `--cache_requests` 支持断点续评

## 支持的模型后端

| 后端 | 模型类型标识 | 支持的请求类型 |
|------|-------------|--------------|
| HuggingFace transformers | `hf` | generate_until, loglikelihood, loglikelihood_rolling |
| vLLM | `vllm` | generate_until, loglikelihood, loglikelihood_rolling |
| SGLang | `sglang` | generate_until, loglikelihood, loglikelihood_rolling |
| OpenAI Completions | `openai-completions` | generate_until, loglikelihood, loglikelihood_rolling |
| OpenAI ChatCompletions | `openai-chat-completions` | generate_until（无 logprobs） |
| Anthropic Chat | `anthropic-chat` | generate_until（无 logprobs） |
| NVIDIA NeMo | `nemo_lm` | generate_until, loglikelihood, loglikelihood_rolling |
| Megatron-LM | `megatron_lm` | generate_until, loglikelihood, loglikelihood_rolling |
| GGUF/GGML (llama.cpp) | `gguf`, `ggml` | generate_until, loglikelihood |
| OpenVINO | `openvino` | generate_until, loglikelihood, loglikelihood_rolling |
| Windows ML | `winml` | generate_until, loglikelihood, loglikelihood_rolling |
| Steered HF | `steered` | generate_until, loglikelihood, loglikelihood_rolling |
| 本地 API 服务器 | `local-completions` | generate_until, loglikelihood, loglikelihood_rolling |

关键区别：不提供 logprobs 的模型（如大部分 ChatCompletions API）只能运行 `generate_until` 类型任务；本地模型或提供 logprobs 的 API 可运行所有任务类型。

## 支持的基准/数据集

包括但不限于：HellaSwag、LAMBADA、ARC（Easy/Challenge）、WinoGrande、PIQA、BoolQ、OpenBookQA、[MMLU](../sources/mmlu.md)、CoT BIG-Bench-Hard、Belebele、GSM8K、Open LLM Leaderboard 任务组等。支持任务名通配符（如 `lambada_openai_mt_*`）。

v0.4.0 新增 Open LLM Leaderboard 任务集，位于 `lm_eval/tasks/leaderboard/` 目录。

## 技术架构

- **任务请求类型**：`generate_until`（开放生成）、`loglikelihood`（对数似然）、`loglikelihood_rolling`（滚动困惑度）、`multiple_choice`（选择题）
- **多 GPU 方案**：
  - 数据并行：`accelerate launch -m lm_eval`，每个 GPU 加载完整模型副本
  - 模型并行：`--model_args parallelize=True`，权重分片到多 GPU
  - 混合并行：同时使用数据并行和模型分片
  - vLLM 张量并行：`tensor_parallel_size` + `data_parallel_size`
  - SGLang：`dp_size` + `tp_size`
- **适配器评测**：支持 PEFT（LoRA）适配器评测，通过 `peft=PATH` 参数
- **量化评测**：GPTQ（GPTQModel/AutoGPTQ）、GGUF 格式、8-bit/4-bit 量化
- **MPS 后端**：支持 Apple Silicon Mac
- **批量大小自动探测**：`--batch_size auto[:N]` 自动检测最大可用 batch size

## 与评测相关的启示

1. **行业事实标准**：作为 HuggingFace Open LLM Leaderboard 的后端和数百篇论文的评测基础设施，lm-evaluation-harness 已成为 LLM 评测领域的事实标准。其评测结果具有高度的可比性和可复现性。
2. **请求类型的重要性**：区分 `generate_until`、`loglikelihood`、`multiple_choice` 等请求类型，反映了不同评测范式对模型能力的不同要求。仅支持 ChatCompletions 的 API 模型无法运行所有任务类型，这是评测架构设计的重要约束。
3. **prompt 标准化与可复现性**：使用公开可用的 prompt 模板确保论文间的可复现性和可比性。评测实现遵循优先级规则：训练方共识 > 官方实现 > 评测方共识 > 常见实现中的优选。
4. **[LLM-as-Judge](../concepts/llm-as-judge.md) 的互补性**：该框架侧重于客观指标评测（准确率、困惑度等），与 LLM-as-Judge 方法互补。`think_end_token` 参数支持剥离 CoT 推理痕迹，说明框架在适应 reasoning model 评测需求。
5. **模块化安装趋势**：将模型后端独立为可选依赖，降低基础包体积，用户按需安装。这种设计理念适合评测工具链的模块化组装。

## 相关页面

- [MMLU 基准](../sources/mmlu.md)
- [LLM-as-Judge 评测方法](../concepts/llm-as-judge.md)
- [MT-Bench & Chatbot Arena](../sources/mt-bench-chatbot-arena.md)
- [Chain-of-Thought Prompting](../sources/chain-of-thought.md)
- [OpenCompass 评测平台](../sources/opencompass-repo.md)
- [HELM 评测框架](../sources/helm-repo.md)
- [GPT-4 Technical Report](../sources/gpt4-technical-report.md)
