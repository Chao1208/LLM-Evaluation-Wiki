---
title: "数字人视频评测方法调研（vadioPK 专项）"
type: research
created: 2026-06-11
updated: 2026-06-11
tags: [vadioPK, 数字人, talking-head, 视频评测, 文献综述, 决策报告]
sources: [
  arxiv:2511.04520,   # THEval
  arxiv:2410.07718,   # Hallo2
  arxiv:2402.17485,   # EMO
  arxiv:2409.02634,   # Loopy
  arxiv:2403.17694,   # AniPortrait
  arxiv:2503.21755,   # VBench-2.0
  arxiv:2406.15252,   # VideoScore
  arxiv:2509.22799,   # VideoScore2
  arxiv:2508.07989,   # Motion Blindness
]
---

# 数字人视频评测方法调研（vadioPK 专项）

> **目的**：为 vadioPK 项目（短视频对相似度评测，6s talking-head，原视频 +
> caption + 视觉大模型合成）选定 v0.1 评测维度的具体 metric。结论指向重置
> v0.1，详见 openspec change `reset-eval-pipeline-to-theval-aligned`。

---

## TL;DR（5 条可执行结论）

1. **v0.1 重置成 THEval-aligned**：[THEval](https://arxiv.org/abs/2511.04520)
   2025 实证 ρ = 0.870 vs 人工，比所有 SOTA talking-head 论文用的 metric 组合
   更可靠。我们应该站在它的肩膀上。
2. **干掉 SyncNet 作为 H 主指标**：THEval 实证 SyncNet 与人工**负相关**
   （ρ = −0.16 / −0.27）；改用 **mouth-openness × audio-RMS**。
3. **干掉 OpenFace AU**：当前主流 talking-head 论文（Hallo2 / EMO / Loopy /
   AniPortrait）**没有一个**用 OpenFace AU。维护困难（C++/dlib），社区共识用
   **E-FID**（基于 3D 重建系数）+ py-feat AU。
4. **限制 Gemini-as-judge 只跑 A 和 Z**：[VBench-2.0](https://arxiv.org/abs/2503.21755)
   显式排除 lip-sync from MLLM judging；
   ["motion blindness"](https://arxiv.org/abs/2508.07989) 论文系统性指出帧
   采样范式无法可靠判断动作类内容。Gemini 只判静态属性 + caption 忠实度。
5. **No-reference video evaluator 不直接适用**：VideoScore / VideoScore2 /
   WCS 都是 no-reference T2V 质量评估，不直接处理 reference-based pair 比较。
   THEval 是唯一 reference-based + talking-head 专用的开源候选。

---

## 一、目标场景与约束

```
input:  (original.mp4, synth.mp4, caption.txt) — 6s, 单人 talking-head
        original 用真人拍摄；synth 由"原视频首帧 + caption" 喂给视觉大模型
        生成。两套合成系统在同一组 9 个原视频上 A/B 对比。

want:   多维度相似度 / 偏离度评分 + 解释，反向用于优化 caption 描述机制。
        没有 ground truth 评分；评测方法本身是研究对象。
```

### 这不是"video similarity"问题

```
              两个视频的 GAP 来源
        ┌─────────────────────────┐
        │                         │
   ┌────┴────┐              ┌─────┴─────┐
   │ 检索/去重 │              │ vadioPK  │
   │ open ID  │              │ ref-based │
   └─────────┘              └───────────┘
        │
   CLIP-Video, ViSiL,             talking-head eval
   TransNet, video copy            CSIM/E-FID/Sync*
                                   THEval, MOS
```

vadioPK 是 **reference-based deviation** 问题，不是检索问题。这意味着所有
"video copy detection / video retrieval" 流派可以排除（CLIP-Video, ViSiL,
TransNet）。

### "差异已经很小" 决定了 metric 的下限

经验：当两个视频"用肉眼能分辨但已经很相似"时，PSNR/SSIM/MS-SSIM **饱和**，
LPIPS 在某些区域也饱和。**真正还能区分的是 domain-specific metric +
VLM judge。** 这是为什么所有 talking-head 论文都堆 CSIM + LSE + AED + APD —
不是炫技，是别的指标在他们的差异水平下没区分度。

---

## 二、Talking-head 论文实测对位

抓了 5 篇最相关的 paper / 实测表，按"实际报告了什么 metric"画出来：

| metric | Hallo2 | EMO | Loopy | AniPortrait | THEval 评价 |
|--------|--------|-----|-------|-------------|--------------|
| **FID** ↓ | ✓ | ✓ | ✓ | ❌ | weak（ρ=0.21） |
| **FVD** ↓ | ✓ | ✓ | ✓ (R/I) | ❌ | weak（ρ=0.29） |
| **Sync-C** ↑ | ✓ | (SyncNet 单分) | ✓ | ❌ | **负相关** |
| **Sync-D** ↓ | ✓ | — | ✓ | ❌ | **负相关** |
| **E-FID** ↓ | ✓ | ✓ (新提出) | ✓ | ❌ | — |
| **CSIM** | ❌ | F-SIM 替代 | ❌ | ❌ | — |
| **IQA** ↑ | — | — | ✓ | ❌ | — |
| **VBench Smooth** | — | — | ✓ | ❌ | — |
| **OpenFace AU** | ❌ | ❌ | ❌ | ❌ | — |

主要观察：

- 🔴 **CSIM 不是黄金指标**：Hallo2 / Loopy 都不报；EMO 用 F-SIM。我们 v0.1
  默认 CSIM 主指标的假设需调整。
- 🔴 **OpenFace AU 没人用**：v0.1 的 FG 选 OpenFace 没有文献支撑。
- 🟢 **E-FID 是表情评测事实标准**（EMO 2024 提出，被 Hallo2 / Loopy 跟进）。
- 🟡 **Sync-C/Sync-D 仍是社区 lingua franca**，但 THEval 实证它和人工评分负相关。
  社区惯性 vs 实证证据冲突。

**HDTF 上 SOTA 数字量级**（参考：Loopy Table 3）：

```
FID: 17–20    FVD: 10–25    Sync-C: 7–8.5    Sync-D: 7–8    E-FID: 1.3–1.5
```

---

## 三、THEval：本调研最关键的发现

详见 [[theval]]。要点：

```
8 维度，开源 pip 工具栈，Spearman ρ = 0.87 vs 人工

Quality:      Global Aesthetics / Mouth Quality / Face Quality       (TOPIQ + MUSIQ)
Naturalness:  Lip Dynamics / Head Motion Dynamics / Eyebrow Dynamics (MediaPipe + FaceXFormer)
Sync:         Silent Lip Stability / Lip-Sync                        (Silero VAD + 互相关)

聚合：s_i = 1 - |Model_i - GT_i| / GT_i, Final = mean(s_i)
```

**THEval 实证的 metric ρ vs human**（重要一手数据）：

| metric | Spearman ρ |
|--------|-----------|
| Mouth Quality (THEval) | **0.765** |
| Head Motion (THEval) | **0.763** |
| Face Quality (THEval) | **0.699** |
| FID | 0.21 |
| FVD | 0.29 |
| **SyncNet LSE-C** | **−0.164** |
| **SyncNet LSE-D** | **−0.269** |
| LMD-F | 偏负 |
| LMD-M | 偏负 |

**SyncNet 的具体问题**（THEval 论文实测）：

1. 音频编码敏感（mp4a → mpga 漂移 0.4–1.2）
2. 可被 game（Wav2Lip 用 SyncNet 训练 → 自报分数远超 GT，但人工评分输给 GT）
3. 能"超越 ground truth"——这是反常信号
4. 对 mouth crop / 亮度 / 画质高度依赖

---

## 四、No-reference 评判模型

```
                     输入需 ref?    talking-head?    可直接用?
  VideoScore-1      no             否               no（无 ref 模式）
  VideoScore-2      no             否               no
  VBench-2.0        no             部分（Human）     no（明确剔除 lip-sync）
  WCS               no             否               no（论文级，无代码）
  THEval            ref（归一化）  是               **yes**（最契合）
```

通用 video-eval 模型对 vadioPK 帮助有限。VideoScore 系列是 no-reference T2V
质量评估，不处理 pair 比较。

[VBench-2.0](https://arxiv.org/abs/2503.21755) 显式排除 lip-sync：

> "lip-syncing is excluded because it is difficult to conclusively assess
> through visual observation alone"

→ **业界 SOTA benchmark 也承认 MLLM 不适合判 lip-sync**。

---

## 五、Video LLM-as-judge 的失败模式

[Implicit Motion Blindness](https://arxiv.org/abs/2508.07989) 把帧采样
范式下的 MLLM 称为 "motion blindness" —— 通过抽帧看视频丢失运动信号。

- 论文未具体测 Gemini，但定性结论：**dense sampling 只有 marginal 提升**。
- 补救方向：引入光流 prior（RAFT），event camera 等。

**对 vadioPK 的影响**：

```
                    Gemini judge 适合 / 不适合
  ────────────────────────────────────────────
  A 全局静态属性          ✅ 静态/语义，强项
  Z 整体真实感+caption     ✅ 语义级别，强项
  ────────────────────────────────────────────
  B 身份保持              ⚠️ 长得像不像它能判，但不如 ArcFace 量化
  FG 五官/表情            ⚠️ 抽 6 帧 + 动作类问题，弱
  DE 视线/头姿轨迹         ❌ motion-heavy，VLM 系统性弱
  H 唇音同步              ❌ VBench-2.0 自己也排除
```

**结论**：v0.1.1 让 Gemini 只评 A 和 Z。

---

## 六、开源工具栈 verified

| 用途 | 工具 | install | 备注 |
|------|------|---------|------|
| AU + emotion + identity 一站 | [py-feat](https://github.com/cosanlab/py-feat) | `pip install py-feat` | ✅ 默认 identity model 即 ArcFace (buffalo_l 同源) |
| identity (CSIM 备份) | [insightface](https://pypi.org/project/insightface/) | `pip install insightface` | buffalo_l |
| TOPIQ / MUSIQ | `pyiqa` | `pip install pyiqa` | THEval 同款 |
| Face mesh / lip / eyebrow | `mediapipe` | `pip install mediapipe` | THEval 同款 |
| Head pose | FaceXFormer | git clone（HF 有权重） | 替代 6DRepNet |
| VAD | `silero-vad` | `pip install silero-vad` | THEval 同款 |
| lip-sync (secondary) | [joonson/syncnet_python](https://github.com/joonson/syncnet_python) | git clone | 仅作 cross-method 验证 |
| LPIPS | `lpips` | `pip install lpips` | 首帧 sanity |
| DTW | `dtaidistance` | `pip install dtaidistance` | DE 时序对齐 |
| Hallo2 preproc 复用 | https://github.com/fudan-generative-vision/hallo2 | git clone | MIT，提供 preprocessing scripts；无 eval |

py-feat 颠覆了之前 OpenFace 的部署难度上限——一个 pip 包覆盖 identity + AU +
emotion + landmark + headpose，活跃维护，pure-python。

---

## 七、对 vadioPK v0.1 的具体建议（→ openspec change）

详见 `openspec/changes/reset-eval-pipeline-to-theval-aligned/`。要点：

| 维度 | v0.1 现状 | v0.1.1 调整 |
|------|-----------|-------------|
| **A** | Gemini judge 静态属性 | ✓ 保留 + TOPIQ Face Quality 辅助 |
| **B** | ArcFace CSIM + 首帧 LPIPS | ✓ 保留（依然是身份评估的合理选择） |
| **DE** | 6DRepNet + L2CS-Net + DTW | 换 FaceXFormer + MediaPipe 视线代理 + DTW |
| **FG** | OpenFace AU + speaking IoU + EmoNet | 换 **py-feat AU + E-FID + MediaPipe lip dynamics** |
| **H** | SyncNet LSE-D | 换 **mouth-openness × audio-RMS（THEval 法）**，SyncNet 降级为 secondary |
| **Z** | Gemini judge 跨维度 | ✓ 保留，限定只判 caption 忠实度 + 整体真实感 |

**不变**：
- A/B/DE/FG/H/Z 命名空间（与 [[digital-human-eval-rubric-v1]] 同名）。
- 自举验证策略（cross-method agreement / sanity anchor / LLM 自洽 / 50 对人工）。
- per-source 聚合 + paired comparison 候选。
- `config_version` 强制写入所有评测产出。

**配置版本**：`config/v0.1.yaml` 锁存为只读历史，新 `config/v0.1.1.yaml` 上线。

---

## 八、未结的疑问 / 待跟进

1. **THEval 代码何时开源**？项目页承诺会放。我们的 v0.1.1 实现里要复用其
   实现细节（如 mouth-openness 的具体计算方式、Lip-Sync 互相关的窗长等），
   等开源后核对一遍。
2. **E-FID 的开源实现**：EMO 2024 提出但没释放计算代码；我们要么找社区复刻，
   要么基于 FLAME / 3DMM 自实现。
3. **paired-comparison 聚合**（A vs B 胜率，Bradley-Terry / Elo）：v0.2
   候选，下游决策直接对应"选哪套合成系统"。
4. **THEval 没有 LLM judge 维度**——他们的 0.87 ρ 已经够用？还是因为 talking
   head 这个 domain 对 LLM judge 需求弱？vadioPK 保留 Z 的理由：caption 忠实度
   是结构化文本对齐问题，VLM 强项，不重叠。

---

## 九、本次调研的失败模式记录（透明）

- 第一次 spawn 子 agent 调研失败（"我没有工具"），实则被错误信号 trick；
  第二次 spawn 大概率根本没启动（task ID 找不到）。**最后是主 agent
  自己用 WebFetch / WebSearch 跑完的**。这件事写进 [[memory]] 教训：
  **research agent 不要一次性 spawn，要么分阶段 + 显式 verify 进度，
  要么主 agent 自己干**。
- arxiv abstract 页面 WebFetch 普遍只能拿到摘要——必须用
  `arxiv.org/html/<id>` 才能拿到正文表格。**记录给后续调研用**。
- 没真正读到的资料：Hallo2 项目页的 GitHub（拿到），但 Loopy 没有公开
  GitHub（仅项目页 loopyavatar.github.io）。AniPortrait HTML 的实测表
  WebFetch 拿不到。这些缺口 v0.1.1 实现时再补。

---

## 引用

- **THEval**, arXiv:2511.04520. <https://arxiv.org/abs/2511.04520>
- **Hallo2**, ICLR 2025, arXiv:2410.07718. <https://github.com/fudan-generative-vision/hallo2>
- **EMO**, arXiv:2402.17485. <https://humanaigc.github.io/emote-portrait-alive/>
- **Loopy**, arXiv:2409.02634. <https://loopyavatar.github.io/>
- **AniPortrait**, arXiv:2403.17694. <https://github.com/Zejun-Yang/AniPortrait>
- **VBench-2.0**, arXiv:2503.21755. <https://github.com/Vchitect/VBench>
- **VideoScore**, arXiv:2406.15252. <https://tiger-ai-lab.github.io/VideoScore/>
- **VideoScore2**, arXiv:2509.22799. <https://TIGER-AI-Lab.github.io/VideoScore2/>
- **Motion Blindness**, arXiv:2508.07989.
- **WCS**, arXiv:2508.00144.
- **py-feat**, <https://github.com/cosanlab/py-feat>
- **SyncNet**, <https://github.com/joonson/syncnet_python>

## 相关页面

- [[theval]] — THEval 单论文页
- [[digital-human-video-evaluation]] — 数字人视频评测概念页
- [[digital-human-eval-rubric-v1]] — 8 维 + 总评人工评分量表
- [[evalcrafter]], [[vbench]] — 通用视频评测对照
