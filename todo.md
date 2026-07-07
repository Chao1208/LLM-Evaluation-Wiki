# TODO — LLM Evaluation Wiki

> 更新：2026-07-07
> 当前专项：Rubric 数据生产（非教育领域，评测 / RL 奖励 / 微调）
> 相关调研：[`wiki/researches/rubric-data-production-survey.md`](wiki/researches/rubric-data-production-survey.md)

---

## 待办：补齐 HuggingFace 原始数据卡

本机直连 `huggingface.co` 超时，以下原始材料尚未下载。优先用镜像站，目标目录
`raw/datasets/`。**跑完把结果贴回给 Ducc，验证完整性并在调研文档补本地路径链接。**

### 方案 A：镜像站 hf-mirror.com（推荐先试）

```bash
mkdir -p /Users/lichao21/Documents/wiki-hub/LLM-Evaluation-Wiki/raw/datasets
cd /Users/lichao21/Documents/wiki-hub/LLM-Evaluation-Wiki/raw/datasets

curl -sL --connect-timeout 10 -A "Mozilla/5.0" \
  -o RaR-Science-README.md \
  "https://hf-mirror.com/datasets/ScaleAI/RaR-Science/raw/main/README.md"

curl -sL --connect-timeout 10 -A "Mozilla/5.0" \
  -o Auto-Rubric-README.md \
  "https://hf-mirror.com/datasets/agentscope-ai/Auto-Rubric/raw/main/README.md"

curl -sL --connect-timeout 10 -A "Mozilla/5.0" \
  -o healthbench-README.md \
  "https://hf-mirror.com/datasets/openai/healthbench/raw/main/README.md"

# 校验
for f in *.md; do echo "$f: $(wc -c < "$f") bytes"; head -3 "$f"; echo "---"; done
```

### 方案 B：走本地代理（镜像也不通时）

```bash
cd /Users/lichao21/Documents/wiki-hub/LLM-Evaluation-Wiki/raw/datasets
export HTTPS_PROXY=http://127.0.0.1:7890   # 换成你自己的代理地址端口
curl -sL --connect-timeout 10 -x "$HTTPS_PROXY" \
  -o RaR-Science-README.md \
  "https://huggingface.co/datasets/ScaleAI/RaR-Science/raw/main/README.md"
# 其余两个同理换 URL
```

### 方案 C：拉真实样本数据（可选，做 few-shot 种子最有用）

```bash
pip install -U "huggingface_hub[cli]"
export HF_ENDPOINT=https://hf-mirror.com        # 关键：走镜像

cd /Users/lichao21/Documents/wiki-hub/LLM-Evaluation-Wiki/raw/datasets
huggingface-cli download ScaleAI/RaR-Science --repo-type dataset --local-dir ./RaR-Science
huggingface-cli download openai/healthbench --repo-type dataset --local-dir ./healthbench
# healthbench 请求勿明文转载，仅本地研究用
```

---

## 已完成

- [x] Rubric 数据生产方法调研 → `wiki/researches/rubric-data-production-survey.md`
- [x] 下载 6 篇核心 PDF 到 `raw/`：
  - `raw/papers/2507.17746-rubrics-as-rewards.pdf`（RaR 主线）
  - `raw/papers/2510.09030-reflect-and-revise.pdf`
  - `raw/papers/2510.07743-scalable-synthetic-rubric.pdf`
  - `raw/papers/2505.08775-healthbench.pdf`
  - `raw/papers/2601.08430-rubrichub.pdf`
  - `raw/benchmarks/openrubrics-acl2026-long791.pdf`
- [x] 确认库里已有的 rubric 相关论文（无需重下）：
  - `2508.12790`（RL with Rubric Anchors）、`2603.20882`（RubricRAG）、
    `2603.01562`（RubricBench）、`2603.21362`（AdaRubric）、`2603.25133`（RubricEval）

---

## 下一步（方向已定，待拍板 4 个未知量）

1. **具体子领域**？通用写作 / 代码 / 客服 / Agent / 专业问答（医法金）
2. **种子问题来源**？业务日志工单 / 公开数据集 / 合成
3. **参考答案是否已有**？（决定 pipeline 是否要先解决 golden answer）
4. **规模量级**？几百条评测集 vs 几万条 RL 训练集

> 4 点定下后 → 起 OpenSpec 提案（proposal + 数据 schema spec + 生产 tasks），
> 并基于 RaR 附录模板改写领域专用 rubric 生成 prompt。
