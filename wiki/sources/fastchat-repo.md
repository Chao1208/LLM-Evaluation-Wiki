---
title: "FastChat 平台"
type: source
created: 2026-04-22
updated: 2026-04-22
tags: [Chatbot Arena, MT-Bench, 模型服务, LLM-as-Judge, LMSYS, Vicuna, 对战评测]
sources: [raw/github/FastChat/README.md]
---

## 概述

FastChat 是由 LMSYS（Large Model Systems Organization，UC Berkeley 为主的研究团队）开发的开源平台，用于大语言模型的训练、服务和评测。FastChat 是 [Chatbot Arena](https://lmarena.ai)（原 LMSYS Chatbot Arena）的底层平台，已服务超过 1000 万次聊天请求，覆盖 70+ LLM，收集了超过 150 万次人类投票用于编制在线 LLM Elo 排行榜。

FastChat 的核心贡献包括：
- **Vicuna** 模型系列的训练代码
- **[MT-Bench](../sources/mt-bench-chatbot-arena.md)** 多轮评测基准
- **Chatbot Arena** 众包对战评测平台
- 分布式多模型服务系统（含 Web UI 和 OpenAI 兼容 RESTful API）

GitHub 地址为 `lm-sys/FastChat`，PyPI 包名为 `fschat`。

## 关键特性

### 评测相关
1. **MT-Bench**：80 道高质量多轮开放式问题，使用 GPT-4 等强 LLM 作为 Judge 自动评分
2. **Chatbot Arena**：用户在匿名条件下让两个模型并排对话并投票选择更好的回答，通过 Elo 评级系统对模型排名
3. **LMSYS-Chat-1M**：100 万条真实 LLM 对话数据集（2023.09 发布）
4. **Chatbot Arena Conversations**：33K 对话的人类偏好数据集

### 模型训练
5. **Vicuna 训练流程**：基于 Llama 2，使用 ~125K ShareGPT 用户对话微调，支持 FSDP 分布式训练
6. **多版本 Vicuna**：7B/13B/33B，4K/16K/32K 上下文长度
7. **其他模型**：LongChat（32K 上下文）、FastChat-T5-3B

### 模型服务
8. **分布式架构**：Controller + Model Worker(s) + Gradio Web Server 三层架构
9. **OpenAI 兼容 API**：提供 RESTful API，可作为 OpenAI API 的本地替代
10. **多模型多 worker**：支持在一个 Controller 下注册多个 Model Worker，实现高吞吐或多模型服务
11. **vLLM 集成**：支持高吞吐批量推理
12. **LangChain 集成**
13. **多平台支持**：CUDA GPU、CPU（含 Intel AVX512/AMX 加速）、Apple Silicon（MPS）、Intel XPU（Arc GPU）、Ascend NPU
14. **量化支持**：8-bit 压缩、ExLlama V2、GPTQ 4-bit、AWQ 4-bit

### API 模型对接
15. **多 API 提供商**：OpenAI、Anthropic、Gemini、Mistral 等，通过 JSON 配置文件定义 API endpoint
16. **本地 Arena 部署**：可在本地部署完整的 Chatbot Arena 实例

## 支持的模型

FastChat 支持广泛的模型：LLama 2、Vicuna、Alpaca、Baize、ChatGLM、Dolly、Falcon、FastChat-T5、GPT4ALL、Guanaco、MTP、OpenAssistant、OpenChat、RedPajama、StableLM、WizardLM、xDAN-AI 等。

## 技术架构

### 服务架构
```
用户 → Gradio Web Server → Controller → Model Worker(s)
                                         ├── Worker 0 (GPU 0, Model A)
                                         ├── Worker 1 (GPU 1, Model B)
                                         └── Worker N (GPU N, Model C)
```

### Vicuna 训练超参数
| 参数 | 值 |
|------|-----|
| Global Batch Size | 128 |
| Learning Rate | 2e-5 |
| Epochs | 3 |
| Max Length | 2048 |
| Weight Decay | 0 |

基于 Stanford Alpaca 训练代码扩展，增加多轮对话支持。支持 LoRA 微调和 SkyPilot 云训练。

## 与评测相关的启示

1. **Chatbot Arena 作为评测金标准**：150 万+ 人类投票构建的 Elo 排行榜是当前最具公信力的 LLM 评测之一。[AlpacaEval](../sources/alpaca-eval-repo.md) 等自动评测工具以与 Arena 的相关性作为验证指标，说明 Arena 已成为评测方法的"校准锚点"。
2. **[MT-Bench](../sources/mt-bench-chatbot-arena.md) 的多轮评测范式**：80 题多轮评测设计，使用 [LLM-as-Judge](../concepts/llm-as-judge.md)（GPT-4）自动评分，GPT-4 Judge 与人类一致率 >80%。MT-Bench 展示了如何设计少量但高质量的评测题目来有效区分模型能力。
3. **评测平台与模型训练的一体化**：FastChat 将模型训练（Vicuna）、服务部署和评测（MT-Bench/Arena）整合在一个平台中，体现了评测驱动模型开发的理念——评测不是独立环节，而是与训练和部署紧密耦合。
4. **众包评测的规模效应**：Chatbot Arena 的成功依赖于大规模用户参与（1000 万+ 请求、150 万+ 投票）。这种众包模式难以复制，但其 Elo 评级方法论和匿名对战设计可被借鉴到小规模人工评测中。
5. **服务架构对评测的支撑**：Controller-Worker 分布式架构支持多模型并发服务，这对需要同时评测多个模型的场景至关重要。OpenAI 兼容 API 接口使得评测工具可以统一对接本地和远程模型。
6. **真实对话数据的价值**：LMSYS-Chat-1M（100 万真实对话）和 Chatbot Arena Conversations（33K 偏好对话）是研究用户真实需求和偏好的宝贵资源，可用于构建更贴近实际使用场景的评测集。

## 相关页面

- [MT-Bench & Chatbot Arena（论文摘要）](../sources/mt-bench-chatbot-arena.md)
- [LLM-as-Judge 评测方法](../concepts/llm-as-judge.md)
- [AlpacaEval 自动评估器](../sources/alpaca-eval-repo.md)
- [Rubric-Based 评测方法论](../concepts/rubric-based-evaluation.md)
- [OpenCompass 评测平台](../sources/opencompass-repo.md)
- [lm-evaluation-harness 评测框架](../sources/lm-evaluation-harness-repo.md)
- [HELM 评测框架](../sources/helm-repo.md)
