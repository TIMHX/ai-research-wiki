---
title: Rerank 分数标定与思考模型的 JSON 污染
created: 2026-09-10
updated: 2026-09-10
type: comparison
tags: [model, benchmark, inference, comparison]
confidence: high
---

# Rerank 分数标定与思考模型的 JSON 污染

两个在检索系统选型时反复踩到、但模型卡片上都不写的行为差异。数据来自 2026-09-10 在一个中文记忆库（OpenViking）上的实测；配置落地过程记在 engineering-wiki 的
`troubleshooting/openviking-model-tuning-20260910.md`。检索栈本身属于
[[hermes-agent-v2026.4.30]] 这类 agent 系统的组成部分。

## 一、rerank 模型的分数标定差异极大

同一条中文事实型 query，同一批 3 条文档（1 条相关 + 2 条主题无关）：

| 模型 | 相关项 | 噪音 1 | 噪音 2 | 标定特征 |
|---|---|---|---|---|
| `gte-rerank-v2`（百炼） | 0.748 | 0.006 | 0.006 | 双峰，噪音塌到接近 0 |
| `qwen3-rerank`（百炼） | 0.748 | 0.278 | 0.218 | 相关项同分，但噪音抬得很高 |
| `jina-reranker-v2-base-multilingual` | 0.385 | 0.043 | 0.025 | 双峰，但整体压缩在低区间 |
| `jina-reranker-v3` | 0.273 | **-0.152** | **-0.158** | **输出 logit，有负值** |

**四个模型的排序全部正确。** 差别不在排序能力，而在**分数标定**——
而下游几乎都用一个固定阈值来截断。

两个直接后果：

- **`jina-reranker-v3` 输出的是未经 sigmoid 的 logit**，会出现负分。任何默认
  `threshold=0.1` 的系统会把结果**全部丢弃**，表现为「配了 rerank 反而召回为空」。
  用它必须把阈值下调到 0 以下。
- **`qwen3-rerank` 的噪音项落在 0.22-0.28**，高于常见默认阈值，等于阈值过滤形同虚设；
  它只能靠 top_n 截断，不能靠阈值。

**结论：选 rerank 不能只看排序指标，要看噪音项的绝对分数落在哪。**
更新的版本号不代表更适合——v3 比 v2 新，但因为标定方式改成 logit，反而更难用。

## 二、思考模型会把 `<think>` 混进 `content`，且关法不统一

MiniMax-M3 默认把思考过程直接写在 `choices[0].message.content` 里（不是独立的
`reasoning_content` 字段），会污染下游的 JSON 解析。实测四种关法：

| 参数 | M3 | M2.7 |
|---|---|---|
| `reasoning_effort: "minimal"` | ❌ 照样输出 | — |
| `enable_thinking: false` | ❌ 照样输出 | — |
| `response_format: {"type":"json_object"}` | ❌ 照样输出 | — |
| `"thinking": {"type": "disabled"}` | ✅ 干净，273→204 token | ❌ **无效** |

**同一家的两个模型，同一个参数，行为不一致**——M2.7 完全无视它，因此不能用于任何
需要产出结构化输出的角色。

对照组：百炼的 `qwen3-max` / `qwen-plus` / `qwen3-vl-plus` / `qwen-vl-max` 默认就不输出思考，
同一抽取任务约 50 token，比 M3 关闭思考后的 204 token 还省 4 倍。

**结论：把思考模型放进「抽取/改写/规划」这类要求结构化输出的位置前，
必须先实测思考能否关掉，以及关掉后的 token 开销**——这项开销在按量计费下是 4 倍量级的差异。

## 关联

见 [[hermes-agent-v2026.4.30]]（同一套 agent 栈里的模型 provider 配置）、
[[mamba-vs-transformer]]（同为「排序/指标正确不等于工程可用」的对比类页面）。
测量来源与最终选型记在 engineering-wiki `troubleshooting/openviking-model-tuning-20260910.md`。
