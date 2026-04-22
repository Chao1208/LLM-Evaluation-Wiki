---
title: "多模态大模型评测"
type: concept
created: 2026-04-22
updated: 2026-04-22
tags: [多模态, VLM, benchmark, 视觉语言模型, 视频生成]
sources: []
---

## 定义

多模态大模型评测（Multimodal LLM Evaluation）是指对具备处理多种模态输入（图像、视频、文本等）能力的大模型进行系统化能力测评的过程。评测范围涵盖：

- **图像理解**：物体识别、场景理解、OCR、图文关系等
- **视觉推理**：空间关系、常识推理、数学推理、逻辑推理等
- **视觉-语言对齐**：图文匹配、指令遵循、开放式问答等
- **视频生成**：视觉质量、文本-视频对齐、运动质量、时序一致性等

随着 [BLIP-2](../sources/blip2.md)、[LLaVA](../sources/llava.md) 等里程碑模型的出现，多模态评测从零散的单任务测试逐步发展为覆盖感知、认知、推理、生成等多维度的体系化评测框架。

## 评测基准全景

| 基准名称 | 发布时间 | 任务类型 | 数据规模 | 评测方式 | 核心特点 |
|---------|---------|---------|---------|---------|---------|
| [MME](../sources/mme.md) | 2023.06 | 感知 + 认知（14 子任务） | 14 子任务，Yes/No 题 | Yes/No 客观评分 | 统一格式，客观可复现 |
| [MMBench](../sources/mmbench.md) | 2023.07 | 感知 + 推理（20 种能力） | ~3,000 道单选题 | CircularEval 多轮评测 | 三级层级能力分类，消除选项位置偏好 |
| [MM-Vet](../sources/mm-vet.md) | 2023.08 (ICML 2024) | 6 种核心 VL 能力的集成 | 218 道开放式问题 | GPT-4 Judge | 评测多能力组合，关注集成能力 |
| [MathVista](../sources/mathvista.md) | 2023.10 (ICLR 2024) | 视觉数学推理（5 种任务） | 6,141 个样本 | 准确率 | 聚焦视觉 + 数学交叉，揭示人机差距 |
| [LVLM-EHub](../sources/lvlm-ehub.md) | 2023 | 多维度综合评测 | 多基准聚合 | 在线 Arena + Elo rating | 成对人类评估，实时排名 |
| [EvalCrafter](../sources/evalcrafter.md) | 2024 (CVPR) | 视频生成（4 维度） | 700 条 text prompts | 17 种指标 + 人类评估 | 首个系统化视频生成评测，分析人机相关性 |
| LLaVA-Bench | 2023 (NeurIPS) | 指令遵循（对话/描述/推理） | 60 张图像（In-the-Wild） | GPT-4 评审 | 开创多模态指令遵循评测范式 |

详细信息参见各基准的资料摘要页。

## 评测维度

多模态评测覆盖的能力维度可归纳为以下层级：

### 感知（Perception）
基础的视觉信息提取能力，包括物体识别、场景理解、OCR、颜色识别、物体计数与定位等。[MME](../sources/mme.md) 的 10 个感知子任务和 [MMBench](../sources/mmbench.md) 的粗/细粒度感知维度对此进行了系统覆盖。

### 认知（Cognition）
需要外部知识或常识参与的理解任务，如常识推理、文本翻译、名人与地标识别等。[MME](../sources/mme.md) 设计了 4 个认知子任务，[MM-Vet](../sources/mm-vet.md) 的 Knowledge 维度也属此类。

### 推理（Reasoning）
高阶的逻辑推理与数学推理能力。[MathVista](../sources/mathvista.md) 专注于视觉场景下的数学推理（几何题解、图表分析等），[MMBench](../sources/mmbench.md) 区分了属性推理、关系推理和逻辑推理。

### 生成（Generation）
包括语言生成质量和视频生成质量两个方面。[MM-Vet](../sources/mm-vet.md) 评测语言生成能力，[EvalCrafter](../sources/evalcrafter.md) 则从视觉质量、运动质量、时序一致性等维度评测视频生成。

### 集成能力（Integrated Capabilities）
现实场景往往需要多种能力的组合运用。[MM-Vet](../sources/mm-vet.md) 通过能力组合矩阵系统评测模型同时调用多种核心能力解决复杂问题的表现。

## 评测方法论

多模态评测在方法论上形成了多条互补路径：

### Yes/No 客观评测（MME）
将所有问题统一为 Yes/No 二选一格式，实现完全自动化评分。优点是客观可复现，缺点是区分度有限（随机基线 50%）且无法评估开放式生成能力。（来源：[MME](../sources/mme.md)）

### CircularEval 策略（MMBench）
对单选题进行多轮评测，每轮将正确答案循环移至不同选项位置，全部轮次正确才算通过。有效消除模型的选项位置偏好，代价是增加推理次数。（来源：[MMBench](../sources/mmbench.md)）

### GPT-4 Judge（MM-Vet / LLaVA-Bench）
使用 GPT-4 作为自动评判者，对开放式回答进行评分。与人类评判具有较高一致性，但引入了对特定模型的依赖，且可能对某些生成风格存在偏好。这是 [LLM-as-Judge](llm-as-judge.md) 方法在多模态领域的重要扩展。（来源：[MM-Vet](../sources/mm-vet.md)、[LLaVA](../sources/llava.md)）

### 在线 Arena 人类评估（LVLM-EHub）
采用 Elo rating 系统进行成对人类盲评，提供最接近真实用户偏好的评测结果，但规模和成本受限。（来源：[LVLM-EHub](../sources/lvlm-ehub.md)）

### 人机相关性分析（EvalCrafter）
在视频生成评测中，系统分析 17 种自动指标与人类评估之间的 Pearson/Spearman 相关系数，揭示哪些自动指标最能代表人类判断。（来源：[EvalCrafter](../sources/evalcrafter.md)）

## 评测工具

[VLMEvalKit](../sources/vlmevalkit.md)（ACM Multimedia 2024）是当前多模态评测的统一开源工具包，采用模块化架构：

- **模型适配器**：为 GPT-4V、Gemini、LLaVA、InternVL 等模型提供统一推理接口
- **数据集处理器**：标准化处理 MME、MMBench、MM-Vet、MathVista 等基准的数据格式
- **评测模块**：实现各基准对应的评分逻辑和指标计算

统一框架消除了不同实现带来的评测偏差，提高了结果的可复现性和公平性。详见 [VLMEvalKit 资料页](../sources/vlmevalkit.md)。

## 代表性模型

### BLIP-2：Q-Former 架构的开创
[BLIP-2](../sources/blip2.md)（Salesforce, ICML 2023）通过轻量级 Querying Transformer（Q-Former，188M 参数）桥接冻结的图像编码器和冻结的 LLM，以 54 倍少的可训练参数在 VQAv2 上超越 Flamingo80B 达 8.7%。Q-Former 的模块化设计思想影响了后续多模态评测框架的设计，BLIP-2 也成为 MME、MMBench 等基准的重要基线模型。

### LLaVA：视觉指令微调的开创
[LLaVA](../sources/llava.md)（NeurIPS 2023）首次将指令微调从纯文本扩展到多模态图文领域，用 GPT-4 自动生成 158K 条多模态指令遵循数据。LLaVA 提出的 LLaVA-Bench 和 GPT-4 评审方法开创了多模态指令遵循评测范式，直接催生了 MM-Vet、MMBench 等后续评测基准的发展。

这两个模型分别代表了**高效架构设计**和**指令微调数据**两条技术路线，共同推动了多模态评测生态的形成。

## 相关页面

### 评测基准
- [MME](../sources/mme.md) -- Yes/No 客观评测，14 子任务
- [MMBench](../sources/mmbench.md) -- CircularEval，20 种能力维度
- [MM-Vet](../sources/mm-vet.md) -- 集成能力评测，GPT-4 Judge
- [MathVista](../sources/mathvista.md) -- 视觉数学推理
- [LVLM-EHub](../sources/lvlm-ehub.md) -- 在线 Arena 综合评测
- [EvalCrafter](../sources/evalcrafter.md) -- 视频生成评测

### 评测工具
- [VLMEvalKit](../sources/vlmevalkit.md) -- 统一评测工具包

### 代表性模型
- [BLIP-2](../sources/blip2.md) -- Q-Former 架构
- [LLaVA](../sources/llava.md) -- 视觉指令微调

### 相关概念
- [LLM-as-Judge](llm-as-judge.md) -- GPT-4 评审方法论
- [数字人视频重建质量评测方法](digital-human-video-evaluation.md) -- 视频质量评测的专项扩展
