# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

# LLM Evaluation Wiki — Schema

> 本文件定义了 Wiki 的结构、规范和工作流。LLM Agent 在每次会话开始时读取此文件。

## 项目概览

- **名称**: LLM-Evaluation-Wiki
- **领域**: 大模型评测（LLM Evaluation & Benchmarking）
- **描述**: 面向大模型评测工作的知识库，持续积累评测方法论、基准测试、模型对比、行业动态和评测实践经验
- **语言**: 中文为主，术语保留英文
- **创建日期**: 2026-04-05

## 架构概要

本项目是一个由 LLM Agent 驱动的知识库，不是传统的软件项目。核心操作通过四个自定义 slash command（skill）完成：

| Command | 作用 | 触发 |
|---------|------|------|
| `/wiki-init` | 初始化新 Wiki 项目 | 首次搭建 |
| `/wiki-ingest` | 摄取原始资料 → 生成摘要+更新实体/概念页 | 用户提供新资料时 |
| `/wiki-query` | 检索 Wiki 回答问题，可选存入 analyses/ | 用户提问时 |
| `/wiki-lint` | 全量健康检查（矛盾/孤立页/缺失链接等） | 定期维护 |

这四个 command 定义在 `.claude/commands/wiki-*.md`。

### 两层目录

- **根目录 `/`**: Wiki 本体 — `raw/`（只读原始资料）、`wiki/`（生成页面）、`index.md`、`log.md`
- **`project/`**: 独立研究子项目 **HealRub**（医疗 Rubric 评测），1 个主持人 Agent + 3 个专项 Agent

### Wiki 作为项目核心知识基础设施

**LLM Evaluation Wiki 是 HealRub 项目最重要的竞争优势。** Wiki 中积累了：

- **方法论知识**: LLM-as-Judge、Rubric-Based 评测、Prometheus 系列、HealthBench 等评测框架的深度摘要和对比分析
- **代码参考**: 28+ 个评测框架仓库（opencompass、lm-evaluation-harness、alpaca_eval、simple-evals 等）的架构分析
- **实战经验**: 392 语音助手评测集、Rubric-Forge 一致率优化等内部实践的详细记录
- **学术基础**: 49 篇论文的结构化摘要，覆盖评测方法论、多模态、医疗、代码、金融等领域

HealRub 的各 Agent 都应主动查阅 Wiki（通过 `index.md` 定位 → 读取相关页面）来指导设计和实验决策。Wiki 既是知识库，也是决策参考。

### project/ — HealRub 医疗 Rubric 评测项目

```
project/
  CLAUDE.md              # 主持人 Agent（协调全局）
  design/                # 系统设计文档
    system-design.md
  HealRub/               # RD Agent（研发：写代码、跑实验）
    CLAUDE.md
    data/                # 全部处理后数据 + 脚本 + 结果
      scripts/           # step0_*.py, smoke_test_*.py, judge_per_dim.py 等
      results/           # 实验结果（v0 baseline 已完成）
      Medbench/splits/   # L2BO 4-Fold + OOS
  qa/                    # QA Agent（质量保障：独占 OOS、独立验证）
    CLAUDE.md
  review/                # Review Agent（设计评审）
    CLAUDE.md
  raw-data/              # 原始数据（只读）
    prompts/             # 4 维度 system prompt
    rules/               # 标注规范文档
    evaluation-data/     # 评测 Excel + case study
  notebooks/             # Jupyter 实验 notebook
```

4 个 Agent，1 层扁平：
- **主持人**（`project/`）：全局协调、设计决策、调度 Agent
- **RD**（`HealRub/`）：写代码、跑实验、迭代 rubric
- **QA**（`qa/`）：独占 OOS 数据、独立验证、出质量报告
- **Review**（`review/`）：独立评审设计文档

运行 judge 脚本：
```bash
cd project/HealRub/data/scripts
python3 smoke_test_ray.py              # Ray 集群全量测试
python3 judge_per_dim.py --case 05_心力衰竭 --dim solution
```

### 当前 Wiki 规模

~98 个 Wiki 页面，~73 个已摄取资料源，49 篇论文 PDF。涵盖：评测方法论、Rubric 评测、多模态评测、医疗评测、代码评测、金融评测等。

## 目录结构

```
raw/                    # 原始资料（只读，不可修改）
  news/                 # 互联网新闻、公众号文章
  papers/               # 学术论文（PDF / Markdown）
  report/               # 行业报告、技术报告、白皮书
  blog/                 # 个人博客、技术博客文章
  chats/                # 聊天记录、讨论摘录
  benchmarks/           # 历史评测集合、评测记录、评测数据
  assets/               # 下载的图片和附件
wiki/                   # LLM 生成和维护的 Wiki 页面
  entities/             # 实体页：模型、机构、人物、产品、数据集
  concepts/             # 概念页：评测方法论、指标、技术概念、理论框架
  sources/              # 资料摘要：每个摄取的原始资料对应一个摘要页
  analyses/             # 分析页：查询产生的深度分析、对比报告
  benchmarks/           # 评测专页：每个评测基准/评测集的详细页面
index.md                # 全局索引：所有页面的目录与摘要
log.md                  # 操作日志：按时间记录所有摄取、查询、维护活动
```

## 页面规范

### Frontmatter

每个 Wiki 页面必须以 YAML frontmatter 开头：

```yaml
---
title: "页面标题"
type: entity | concept | source | analysis | benchmark
created: YYYY-MM-DD
updated: YYYY-MM-DD
tags: [标签1, 标签2]
sources: [来源文件名1, 来源文件名2]
---
```

### 实体类型细分

entities 目录下的页面需要在 frontmatter 中额外标注 `entity_type`：

- `model` — 大模型（如 GPT-4、Claude 3.5、DeepSeek-R1、Qwen-2.5 等）
- `organization` — 机构（如 OpenAI、Anthropic、百度、阿里等）
- `person` — 人物（如研究者、行业领袖）
- `dataset` — 数据集（如 MMLU、HumanEval、C-Eval 等）
- `tool` — 评测工具和平台

### 文件命名

- 使用小写 kebab-case：`gpt-4o.md`、`mmlu-benchmark.md`
- 资料摘要与原始文件对应：`raw/papers/xxx.pdf` → `wiki/sources/xxx.md`
- 文件名不含空格和中文，用英文命名

### 交叉引用

- 使用标准 Markdown 链接：`[显示文本](../entities/gpt-4o.md)`
- 使用相对路径
- 提及有独立页面的实体或概念时，**必须**添加链接
- 每个页面底部必须有 `## 相关页面` 章节

### 内容规范

- 使用清晰简洁的中文，专业术语保留英文（如 benchmark、BLEU、F1-score）
- 使用 `##` `###` 组织内容层级
- 信息冲突时必须明确标注矛盾，注明各来源
- 始终引用来源：`（来源：[资料名](../sources/xxx.md)）`
- 评测数据使用表格呈现，便于对比

### 模型页面模板

模型实体页面建议包含以下章节：

```markdown
## 基本信息
（发布时间、开发机构、参数规模、架构等）

## 评测表现
（各基准上的得分，使用表格）

## 核心能力与特点
（模型的突出能力和不足）

## 版本演进
（各版本变化）

## 相关页面
```

### 评测基准页面模板

```markdown
## 基本信息
（创建机构、发布时间、评测领域、数据规模）

## 评测维度
（考察哪些能力）

## 方法论
（评测流程、指标计算方式）

## 各模型表现
（排行榜，使用表格）

## 局限性与争议

## 相关页面
```

## 工作流

### Ingest（摄取）工作流

当用户要求摄取一个资料时：

1. 完整阅读原始资料
2. 与用户讨论关键要点（3-5 条）
3. 在 `wiki/sources/` 创建摘要页，包含：概述、关键发现、重要数据、问题与思考
4. 识别涉及的实体和概念
5. 对每个实体/概念：
   - 页面已存在 → 更新信息，添加来源，标注矛盾
   - 页面不存在 → 创建新页面
6. 如果涉及评测数据，更新 `wiki/benchmarks/` 下的相关页面
7. 更新 `index.md`
8. 追加 `log.md` 条目
9. 报告所有变更

### Query（查询）工作流

当用户提问时：

1. 读取 `index.md` 定位相关页面
2. 阅读相关页面，必要时沿链接深入
3. 综合回答，附带引用
4. 如果答案有价值，询问是否存入 `wiki/analyses/`
5. 更新索引和日志

### Lint（健康检查）工作流

定期检查：

1. 矛盾：页面间冲突的评测数据或结论
2. 过时：被新资料取代的旧信息
3. 孤立页：没有入链的页面
4. 缺失页：被提及但没有独立页面的实体/概念
5. 缺失链接：应该链接但未链接的引用
6. 数据空白：覆盖不足的模型或评测维度
7. 生成报告，提出改进建议

## Index 格式

```markdown
# LLM Evaluation Wiki 索引

> 最后更新: YYYY-MM-DD | 总页面数: N | 总资料数: N

## 资料摘要
| 页面 | 类型 | 摘要 | 日期 |
|------|------|------|------|

## 模型
| 页面 | 机构 | 摘要 | 资料数 |
|------|------|------|--------|

## 评测基准
| 页面 | 领域 | 摘要 | 资料数 |
|------|------|------|--------|

## 机构与人物
| 页面 | 类型 | 摘要 | 资料数 |
|------|------|------|--------|

## 概念与方法论
| 页面 | 摘要 | 资料数 |
|------|------|--------|

## 分析报告
| 页面 | 问题 | 日期 |
|------|------|------|
```

## Log 格式

```markdown
# Wiki 操作日志

## [YYYY-MM-DD] ingest | 资料标题
- 资料路径: `raw/xxx/filename.ext`
- 摘要页: wiki/sources/filename.md
- 新建页面: page1.md, page2.md
- 更新页面: page3.md, page4.md

## [YYYY-MM-DD] query | 问题摘要
- 问题: "完整问题"
- 参考页面: page1.md, page2.md
- 答案保存: wiki/analyses/xxx.md（或"未保存"）

## [YYYY-MM-DD] lint | 健康检查
- 发现问题: N 个
- 已修复: N 个
- 详情: ...
```
