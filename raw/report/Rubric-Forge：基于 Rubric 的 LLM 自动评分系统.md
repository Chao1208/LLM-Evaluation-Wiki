---
title: "Rubric-Forge：基于 Rubric 的 LLM 自动评分系统"
source: "https://ku.baidu-int.com/knowledge/HFVrC7hq1Q/pKzJfZczuc/sQz_0bCfVR/Ff9P7Y1W008YTN"
author:
published:
created: 2026-04-09
description: "如流知识库"
tags:
  - "clippings"
---


## 一、系统工作流

![](https://rte.weiyun.baidu.com/wiki/attach/image/api/imageDownloadAddress?attachId=4abf0b20e95e466e8fc97e58f7d41544&docGuid=Ff9P7Y1W008YTN&sign=eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIiwiYXBwSWQiOjEsInVpZCI6InpmbENRZjQ5Z0kiLCJkb2NJZCI6IkZmOVA3WTFXMDA4WVROIn0..caoZxFzHYkUlIq6H.WjfzplQ92P86b5HknM-uoeKO6nfr4LKPEo_ngQEAKHHcMoOkYD9nk7XRu-hFHDkgfVzAFKFg1brq4G4eh33z9sGoPG2bq8DMZkrw8xOcHkm9t4G_QIhf9EHIEPpEiSseqjwT1WQg2NTEQpqqeqa3hZaM0SJPLR9za0n9nupKoZg5UBvNEyJM_3O0_3O32GGXqrIqaOudaqpIVjRw9EF286W5jg.Iijz3kBwVYbUz8dr-QBRnw&x-bce-process=image/resize,m_lfit,w_960/ignore-error,i_1) ![](https://rte.weiyun.baidu.com/wiki/attach/image/api/imageDownloadAddress?attachId=098513acb93943808ef36c64b026a062&docGuid=Ff9P7Y1W008YTN&sign=eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIiwiYXBwSWQiOjEsInVpZCI6InpmbENRZjQ5Z0kiLCJkb2NJZCI6IkZmOVA3WTFXMDA4WVROIn0..caoZxFzHYkUlIq6H.WjfzplQ92P86b5HknM-uoeKO6nfr4LKPEo_ngQEAKHHcMoOkYD9nk7XRu-hFHDkgfVzAFKFg1brq4G4eh33z9sGoPG2bq8DMZkrw8xOcHkm9t4G_QIhf9EHIEPpEiSseqjwT1WQg2NTEQpqqeqa3hZaM0SJPLR9za0n9nupKoZg5UBvNEyJM_3O0_3O32GGXqrIqaOudaqpIVjRw9EF286W5jg.Iijz3kBwVYbUz8dr-QBRnw&x-bce-process=image/resize,m_lfit,w_960/ignore-error,i_1) ![](https://rte.weiyun.baidu.com/wiki/attach/image/api/imageDownloadAddress?attachId=6a3a1b0651c149a19fbd583674eca8db&docGuid=Ff9P7Y1W008YTN&sign=eyJhbGciOiJkaXIiLCJlbmMiOiJBMjU2R0NNIiwiYXBwSWQiOjEsInVpZCI6InpmbENRZjQ5Z0kiLCJkb2NJZCI6IkZmOVA3WTFXMDA4WVROIn0..caoZxFzHYkUlIq6H.WjfzplQ92P86b5HknM-uoeKO6nfr4LKPEo_ngQEAKHHcMoOkYD9nk7XRu-hFHDkgfVzAFKFg1brq4G4eh33z9sGoPG2bq8DMZkrw8xOcHkm9t4G_QIhf9EHIEPpEiSseqjwT1WQg2NTEQpqqeqa3hZaM0SJPLR9za0n9nupKoZg5UBvNEyJM_3O0_3O32GGXqrIqaOudaqpIVjRw9EF286W5jg.Iijz3kBwVYbUz8dr-QBRnw&x-bce-process=image/resize,m_lfit,w_960/ignore-error,i_1)

**核心设计：**

- •
	每个 query 拥有独立的评分标准（criteria），包含维度、权重、pass/fail条件、veto标签

- •
	评分模型（Sonnet 4.6）逐条判定 pass/fail 并给出 evidence，再由规则层加权聚合

- •
	veto 机制：关键标准（如核心任务未完成）一票否决，直接判0

## 二、与基线版本（M3工作流）的 Per-Batch 一致率对比

<table><colgroup><col width="253"> <col width="107"> <col width="112"> <col width="74"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p>Batch</p></td><td colspan="1" rowspan="1"><p>基线版本</p></td><td colspan="1" rowspan="1"><p>Rubric-Forge</p></td><td colspan="1" rowspan="1"><p>提升</p></td></tr><tr><td colspan="1" rowspan="1"><p>1223_lora环境_392</p></td><td colspan="1" rowspan="1"><p>73.98%</p></td><td colspan="1" rowspan="1"><p><strong>90.3%</strong></p></td><td colspan="1" rowspan="1"><p>+16.3</p></td></tr><tr><td colspan="1" rowspan="1"><p>mtp_392_2批跑结果</p></td><td colspan="1" rowspan="1"><p>75.26%</p></td><td colspan="1" rowspan="1"><p><strong>89.5%</strong></p></td><td colspan="1" rowspan="1"><p>+14.2</p></td></tr><tr><td colspan="1" rowspan="1"><p>批跑结果手百int4精度对齐_392</p></td><td colspan="1" rowspan="1"><p>73.47%</p></td><td colspan="1" rowspan="1"><p><strong>90.8%</strong></p></td><td colspan="1" rowspan="1"><p>+17.3</p></td></tr><tr><td colspan="1" rowspan="1"><p>mtp_392批跑结果</p></td><td colspan="1" rowspan="1"><p>69.64%</p></td><td colspan="1" rowspan="1"><p><strong>88.8%</strong></p></td><td colspan="1" rowspan="1"><p>+19.2</p></td></tr><tr><td colspan="1" rowspan="1"><p>xianshang_int8-392</p></td><td colspan="1" rowspan="1"><p>71.17%</p></td><td colspan="1" rowspan="1"><p><strong>87.2%</strong></p></td><td colspan="1" rowspan="1"><p>+16.0</p></td></tr><tr><td colspan="1" rowspan="1"><p>op_670b_v1_nuoermosi_392</p></td><td colspan="1" rowspan="1"><p>63.27%</p></td><td colspan="1" rowspan="1"><p><strong>89.8%</strong></p></td><td colspan="1" rowspan="1"><p>+26.5</p></td></tr><tr><td colspan="1" rowspan="1"><p><strong>白盒均值</strong></p></td><td colspan="1" rowspan="1"><p><strong>~71.1%</strong></p></td><td colspan="1" rowspan="1"><p><strong>~89.4%</strong></p></td><td colspan="1" rowspan="1"><p><strong>+18.3</strong></p></td></tr><tr><td colspan="1" rowspan="1"></td><td colspan="1" rowspan="1"></td><td colspan="1" rowspan="1"></td><td colspan="1" rowspan="1"></td></tr><tr><td colspan="1" rowspan="1"><p>392_小助手_1219（黑盒）</p></td><td colspan="1" rowspan="1"><p>67.60%</p></td><td colspan="1" rowspan="1"><p><strong>83.9%</strong></p></td><td colspan="1" rowspan="1"><p>+16.3</p></td></tr><tr><td colspan="1" rowspan="1"><p>1222_线上版本_392（黑盒）</p></td><td colspan="1" rowspan="1"><p>65.31%</p></td><td colspan="1" rowspan="1"><p><strong>85.0%</strong></p></td><td colspan="1" rowspan="1"><p>+19.7</p></td></tr><tr><td colspan="1" rowspan="1"><p>ALLcomplete_【392】批跑（黑盒）</p></td><td colspan="1" rowspan="1"><p>70.66%</p></td><td colspan="1" rowspan="1"><p><strong>89.3%</strong></p></td><td colspan="1" rowspan="1"><p>+18.6</p></td></tr></tbody></table>

## 三、本次自进化迭代调优记录

<table><colgroup><col width="109"> <col width="214"> <col width="80"> <col width="74"> <col width="74"> <col width="74"> <col width="74"> <col width="74"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p>版本</p></td><td colspan="1" rowspan="1"><p>说明</p></td><td colspan="1" rowspan="1"><p>评测条数</p></td><td colspan="1" rowspan="1"><p>一致率</p></td><td colspan="1" rowspan="1"><p>过松</p></td><td colspan="1" rowspan="1"><p>过严</p></td><td colspan="1" rowspan="1"><p>训练集</p></td><td colspan="1" rowspan="1"><p>验证集</p></td></tr><tr><td colspan="1" rowspan="1"><p>v2 (基线)</p></td><td colspan="1" rowspan="1"><p>初版种子rubric</p></td><td colspan="1" rowspan="1"><p>1,878</p></td><td colspan="1" rowspan="1"><p>79.4%</p></td><td colspan="1" rowspan="1"><p>118</p></td><td colspan="1" rowspan="1"><p>268</p></td><td colspan="1" rowspan="1"><p>79.4%</p></td><td colspan="1" rowspan="1"><p>—</p></td></tr><tr><td colspan="1" rowspan="1"><p>v3</p></td><td colspan="1" rowspan="1"><p>首轮bad case修复</p></td><td colspan="1" rowspan="1"><p>1,878</p></td><td colspan="1" rowspan="1"><p>85.6%</p></td><td colspan="1" rowspan="1"><p>142</p></td><td colspan="1" rowspan="1"><p>128</p></td><td colspan="1" rowspan="1"><p>85.6%</p></td><td colspan="1" rowspan="1"><p>—</p></td></tr><tr><td colspan="1" rowspan="1"><p>v4</p></td><td colspan="1" rowspan="1"><p>criteria持续优化</p></td><td colspan="1" rowspan="1"><p>1,878</p></td><td colspan="1" rowspan="1"><p>86.8%</p></td><td colspan="1" rowspan="1"><p>108</p></td><td colspan="1" rowspan="1"><p>140</p></td><td colspan="1" rowspan="1"><p>86.8%</p></td><td colspan="1" rowspan="1"><p>—</p></td></tr><tr><td colspan="1" rowspan="1"><p>v5</p></td><td colspan="1" rowspan="1"><p>权重调整 + veto机制</p></td><td colspan="1" rowspan="1"><p>1,878</p></td><td colspan="1" rowspan="1"><p>88.9%</p></td><td colspan="1" rowspan="1"><p>105</p></td><td colspan="1" rowspan="1"><p>103</p></td><td colspan="1" rowspan="1"><p>88.9%</p></td><td colspan="1" rowspan="1"><p>—</p></td></tr><tr><td colspan="1" rowspan="1"><p>v6</p></td><td colspan="1" rowspan="1"><p>criteria精修</p></td><td colspan="1" rowspan="1"><p>1,878</p></td><td colspan="1" rowspan="1"><p>89.2%</p></td><td colspan="1" rowspan="1"><p>111</p></td><td colspan="1" rowspan="1"><p>91</p></td><td colspan="1" rowspan="1"><p>89.2%</p></td><td colspan="1" rowspan="1"><p>—</p></td></tr><tr><td colspan="1" rowspan="1"><p>v7</p></td><td colspan="1" rowspan="1"><p>大幅criteria改写</p></td><td colspan="1" rowspan="1"><p>1,878</p></td><td colspan="1" rowspan="1"><p>90.5%</p></td><td colspan="1" rowspan="1"><p>112</p></td><td colspan="1" rowspan="1"><p>67</p></td><td colspan="1" rowspan="1"><p>90.5%</p></td><td colspan="1" rowspan="1"><p>—</p></td></tr><tr><td colspan="1" rowspan="1"><p>v8</p></td><td colspan="1" rowspan="1"><p>微调</p></td><td colspan="1" rowspan="1"><p>1,878</p></td><td colspan="1" rowspan="1"><p>90.4%</p></td><td colspan="1" rowspan="1"><p>115</p></td><td colspan="1" rowspan="1"><p>66</p></td><td colspan="1" rowspan="1"><p>90.4%</p></td><td colspan="1" rowspan="1"><p>—</p></td></tr><tr><td colspan="1" rowspan="1"><p>v9</p></td><td colspan="1" rowspan="1"><p>精修收敛</p></td><td colspan="1" rowspan="1"><p>1,874</p></td><td colspan="1" rowspan="1"><p>90.9%</p></td><td colspan="1" rowspan="1"><p>108</p></td><td colspan="1" rowspan="1"><p>63</p></td><td colspan="1" rowspan="1"><p>90.9%</p></td><td colspan="1" rowspan="1"><p>—</p></td></tr><tr><td colspan="1" rowspan="1"><p>v10</p></td><td colspan="1" rowspan="1"><p>引入验证集(6 batch全量)</p></td><td colspan="1" rowspan="1"><p>2,352</p></td><td colspan="1" rowspan="1"><p>89.1%</p></td><td colspan="1" rowspan="1"><p>139</p></td><td colspan="1" rowspan="1"><p>118</p></td><td colspan="1" rowspan="1"><p>89.5%</p></td><td colspan="1" rowspan="1"><p>87.3%</p></td></tr><tr><td colspan="1" rowspan="1"><p>v6e</p></td><td colspan="1" rowspan="1"><p>注入校准提示词 + GT修正</p></td><td colspan="1" rowspan="1"><p>2,352</p></td><td colspan="1" rowspan="1"><p>89.4%</p></td><td colspan="1" rowspan="1"><p>145</p></td><td colspan="1" rowspan="1"><p>105</p></td><td colspan="1" rowspan="1"><p>89.8%</p></td><td colspan="1" rowspan="1"><p>87.6%</p></td></tr><tr><td colspan="1" rowspan="1"><p><strong>最终版</strong></p></td><td colspan="1" rowspan="1"><p><strong>targeted rubric修复</strong></p></td><td colspan="1" rowspan="1"><p><strong>2,352</strong></p></td><td colspan="1" rowspan="1"><p><strong>89.4%</strong></p></td><td colspan="1" rowspan="1"><p><strong>136</strong></p></td><td colspan="1" rowspan="1"><p><strong>113</strong></p></td><td colspan="1" rowspan="1"><p><strong>89.9%</strong></p></td><td colspan="1" rowspan="1"><p><strong>87.6%</strong></p></td></tr></tbody></table>

## 四、最终结果

### 4.1 白盒集（参与调优）

<table><colgroup><col width="234"> <col width="74"> <col width="103"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p>Batch</p></td><td colspan="1" rowspan="1"><p>条数</p></td><td colspan="1" rowspan="1"><p>一致率</p></td></tr><tr><td colspan="1" rowspan="1"><p>批跑结果手百int4精度对齐_392</p></td><td colspan="1" rowspan="1"><p>392</p></td><td colspan="1" rowspan="1"><p>90.8%</p></td></tr><tr><td colspan="1" rowspan="1"><p>1223_lora环境_392</p></td><td colspan="1" rowspan="1"><p>392</p></td><td colspan="1" rowspan="1"><p>90.3%</p></td></tr><tr><td colspan="1" rowspan="1"><p>op_670b_v1_nuoermosi_392</p></td><td colspan="1" rowspan="1"><p>392</p></td><td colspan="1" rowspan="1"><p>89.8%</p></td></tr><tr><td colspan="1" rowspan="1"><p>mtp_392_2批跑结果</p></td><td colspan="1" rowspan="1"><p>392</p></td><td colspan="1" rowspan="1"><p>89.5%</p></td></tr><tr><td colspan="1" rowspan="1"><p>mtp_392批跑结果</p></td><td colspan="1" rowspan="1"><p>392</p></td><td colspan="1" rowspan="1"><p>88.8%</p></td></tr><tr><td colspan="1" rowspan="1"><p>xianshang_int8-392</p></td><td colspan="1" rowspan="1"><p>392</p></td><td colspan="1" rowspan="1"><p>87.2%</p></td></tr><tr><td colspan="1" rowspan="1"><p><strong>白盒整体</strong></p></td><td colspan="1" rowspan="1"><p><strong>2,352</strong></p></td><td colspan="1" rowspan="1"><p><strong>89.4%</strong></p></td></tr></tbody></table>

### 4.2 黑盒集（独立验证，未参与调优）

<table><colgroup><col width="186"> <col width="74"> <col width="74"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p>Batch</p></td><td colspan="1" rowspan="1"><p>条数</p></td><td colspan="1" rowspan="1"><p>一致率</p></td></tr><tr><td colspan="1" rowspan="1"><p>ALLcomplete_【392】批跑</p></td><td colspan="1" rowspan="1"><p>392</p></td><td colspan="1" rowspan="1"><p>89.3%</p></td></tr><tr><td colspan="1" rowspan="1"><p>1222_线上版本_392</p></td><td colspan="1" rowspan="1"><p>100</p></td><td colspan="1" rowspan="1"><p>85.0%</p></td></tr><tr><td colspan="1" rowspan="1"><p>392_小助手_1219</p></td><td colspan="1" rowspan="1"><p>392</p></td><td colspan="1" rowspan="1"><p>83.9%</p></td></tr><tr><td colspan="1" rowspan="1"><p>sft-v13-18</p></td><td colspan="1" rowspan="1"><p>392</p></td><td colspan="1" rowspan="1"><p>82.4%</p></td></tr><tr><td colspan="1" rowspan="1"><p><strong>黑盒整体</strong></p></td><td colspan="1" rowspan="1"><p><strong>1,276</strong></p></td><td colspan="1" rowspan="1"><p><strong>85.2%</strong></p></td></tr></tbody></table>

### 4.3 总览

<table><colgroup><col width="80"> <col width="74"> <col width="74"> <col width="74"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p>指标</p></td><td colspan="1" rowspan="1"><p>白盒集</p></td><td colspan="1" rowspan="1"><p>黑盒集</p></td><td colspan="1" rowspan="1"><p>合计</p></td></tr><tr><td colspan="1" rowspan="1"><p>评测条数</p></td><td colspan="1" rowspan="1"><p>2,352</p></td><td colspan="1" rowspan="1"><p>1,276</p></td><td colspan="1" rowspan="1"><p>3,628</p></td></tr><tr><td colspan="1" rowspan="1"><p><strong>一致率</strong></p></td><td colspan="1" rowspan="1"><p><strong>89.4%</strong></p></td><td colspan="1" rowspan="1"><p><strong>85.2%</strong></p></td><td colspan="1" rowspan="1"><p><strong>87.9%</strong></p></td></tr><tr><td colspan="1" rowspan="1"><p>过松</p></td><td colspan="1" rowspan="1"><p>136</p></td><td colspan="1" rowspan="1"><p>119</p></td><td colspan="1" rowspan="1"><p>255</p></td></tr><tr><td colspan="1" rowspan="1"><p>过严</p></td><td colspan="1" rowspan="1"><p>113</p></td><td colspan="1" rowspan="1"><p>70</p></td><td colspan="1" rowspan="1"><p>183</p></td></tr></tbody></table>

## 五、Rubric 规格

<table><colgroup><col width="201"> <col width="300"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p>项目</p></td><td colspan="1" rowspan="1"><p>数值</p></td></tr><tr><td colspan="1" rowspan="1"><p>覆盖 Query 数</p></td><td colspan="1" rowspan="1"><p>392</p></td></tr><tr><td colspan="1" rowspan="1"><p>总 Criteria 数</p></td><td colspan="1" rowspan="1"><p>1,729</p></td></tr><tr><td colspan="1" rowspan="1"><p>平均 Criteria / Query</p></td><td colspan="1" rowspan="1"><p>4.4</p></td></tr><tr><td colspan="1" rowspan="1"><p>Veto Criteria 数</p></td><td colspan="1" rowspan="1"><p>33</p></td></tr><tr><td colspan="1" rowspan="1"><p>评分模型</p></td><td colspan="1" rowspan="1"><p>Claude Sonnet 4.6</p></td></tr><tr><td colspan="1" rowspan="1"><p>聚合策略</p></td><td colspan="1" rowspan="1"><p>加权求和(threshold=0.6) + veto一票否决</p></td></tr></tbody></table>

**Criteria 维度分布：**

<table><colgroup><col width="175"> <col width="74"> <col width="74"></colgroup><tbody><tr><td colspan="1" rowspan="1"><p>维度</p></td><td colspan="1" rowspan="1"><p>数量</p></td><td colspan="1" rowspan="1"><p>占比</p></td></tr><tr><td colspan="1" rowspan="1"><p>instruction_following</p></td><td colspan="1" rowspan="1"><p>1,140</p></td><td colspan="1" rowspan="1"><p>65.9%</p></td></tr><tr><td colspan="1" rowspan="1"><p>reasoning</p></td><td colspan="1" rowspan="1"><p>224</p></td><td colspan="1" rowspan="1"><p>13.0%</p></td></tr><tr><td colspan="1" rowspan="1"><p>emotion</p></td><td colspan="1" rowspan="1"><p>166</p></td><td colspan="1" rowspan="1"><p>9.6%</p></td></tr><tr><td colspan="1" rowspan="1"><p>style</p></td><td colspan="1" rowspan="1"><p>100</p></td><td colspan="1" rowspan="1"><p>5.8%</p></td></tr><tr><td colspan="1" rowspan="1"><p>conversation</p></td><td colspan="1" rowspan="1"><p>50</p></td><td colspan="1" rowspan="1"><p>2.9%</p></td></tr><tr><td colspan="1" rowspan="1"><p>safety</p></td><td colspan="1" rowspan="1"><p>22</p></td><td colspan="1" rowspan="1"><p>1.3%</p></td></tr><tr><td colspan="1" rowspan="1"><p>其他</p></td><td colspan="1" rowspan="1"><p>27</p></td><td colspan="1" rowspan="1"><p>1.6%</p></td></tr></tbody></table>

## 六、关键发现与后续优化方向

### 6.1 关键发现

1. 1.
	**Rubric 不需要长，关键在准** ：迭代过程中发现，即使每 query 仅 2~3 条 criteria（冷启动阶段的"偷懒生成"），配合校准提示词经过迭代后也能达到 ~90% 一致率。说明评分效果主要取决于 criteria 是否精准命中了人工关注的区分点，而非数量堆砌。

1. 2.
	**系统自适应人工偏好** ：工作流通过反复对比 GT=1 和 GT=0 的回复差异，自动发现人工真正关注的判分维度。例如某些 query 人工并不关注"情感回应"维度，系统会在迭代中逐步降低该维度的权重或移除相关 criteria，最终收敛到与人工判断一致的标准。

1. 3.
	**对标注质量的反向监控** ：在迭代过程中发现部分 query 的 GT 标注可能存在不一致（如 q345 的 GT=1 和 GT=0 回复内容几乎相同），以及不同 batch 间对 RAG 遵循的宽严尺度不统一。工作流在定位 bad case 时自然暴露了这些标注问题。

1. 4.
	**维度发现能力** ：每个 query 的 criteria 维度分布反映了该 query 考察模型的重点——如某 query 的 criteria 集中在 reasoning 和 instruction\_following，说明该 query 主要考察模型的推理和指令遵循能力。这为测试集的结构化分析提供了数据基础。

### 6.2 后续优化方向

1. 1.
	将 rubric 生成从主流程中剥离，形成独立的前置模块。当前嵌入式生成存在"偷懒"倾向（criteria 过简或遗漏关键维度），独立模块可以更充分地分析每个 query 的 GT=1/GT=0 对比信息，生成更高质量的种子 rubric。

1. 2.
	探索在保持一致率的前提下，将 criteria 数量压缩到 2~3 条/query 的最小集合。减少 LLM 评分的 token 消耗，同时降低 criteria 间的干扰和冲突，提升评分稳定性。

1. 3.
	更换烂一点的评分模型，以此提升rubrics的粒度和质量。

1. 4.
	rubrics生成的粒度和方向没有贯穿整个流程，导致超前期和后期修改的rubrics有的不符合规范
