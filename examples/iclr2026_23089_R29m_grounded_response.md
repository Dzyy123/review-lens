# ReviewLens — Grounded 逐条应对（读了 VGR 原文 + 相关工作之后）

> 这是 ReviewLens 的**旗舰输出模式**：每条 review = `类型 + 论文实情 + 怎么回复 + 建议怎么做`。
> 与上一版 `_analysis.md`（未读原文，只做切题/可答/冗余）相比，**读原文后出现关键反转**：R29m 的一大批"缺失"其实论文正文/附录已写——这是 reviewer 漏读，不是论文缺陷。这正是"对应性/Grounding"维度的价值，也是它必须读原文的原因。

## 关键反转：~9 条「论文已答」（reviewer 漏读，多在附录）

| # | reviewer 说"缺失" | 论文其实写在哪 |
| --- | --- | --- |
| 2 | Doubao1.5-VL 参数/prompt | **附录 D.1**：默认参数、max 8192；prompts 见 Table 17 |
| 8 | replay 触发机制 | **Sec 3**：special signal token，推理时 monitor 输出、解析坐标 |
| 9 | 训练超参 | **附录 A**：lr 1e-5/2e-5、warmup 0.03、cosine、zero decay、1 epoch |
| 17 | reject sampling 阈值/ANLS | **附录 D.1**：阈值=3（0–5）、ANLS 用于 closed-ended |
| 22 | **7B vs 13B** | **Sec 5.1 + Table 11**：13B 已做（MMStar 44.6） → reviewer **错误前提** |
| 24 | region selection 准确率 | **附录 B.3**（RefCOCO IoU, Table 13）+ **B.5**（valid bbox 97.5–98.9%） |
| 28 | replay 延迟开销 | **附录 B.5 Table 15** + **A.2 Table 10**（VGR 7.5s vs ZoomEye 48.5s） |
| 33 | GIoU bbox 归一化 | **Sec 5.3 + 附录 Table 16**：归一化到 [0,1]，且有 abs vs norm 对比 |
| 38 | 14B 标注模型泛化 | **附录 A.1 Table 9**：cold-start vs annotator data 对比 |

> R29m 的 **confidence = 5（最高）**，却把论文**正文 + 附录明确写了**的 13B、触发机制、延迟、bbox 归一化都标成 "missing/not provided"。这是强烈的"**没读附录 / AI 生成的通用 checklist**"信号 —— 也是 ReviewLens 判定 review 质量的硬证据。

## 整体判定

**VGR 是否 over-claimed？—— 不严重，但有一处 claim 强于证据。**
- ✅ "30% token 超 baseline"：Table 2 支撑（864 vs 2880 token，多数 benchmark 更高）。
- ✅ novelty "visual memory replay"：相对 CogCoM / Chain-of-Spot / ZoomEye 的 "grounding-then-crop"，VGR "预编码一次 + 检索 token" 有效率优势（附录 A.2 实证：7.5s vs 48.5s）。delta 中等但真实。
- ⚠️ **最弱：减 "language bias" 这个 claim**——标题/abstract 反复强调，但**全文无直接量化指标**，只有 Table 5 的间接证据。这正是 #12 命中的真缺口。

**R29m 这份 review 的质量？—— 量大但避重就轻。**
- 80 编号 = **40 独立诉求 ×2**（50% 镜像冗余）
- 40 中：**~9 条论文已答（漏读）**、**~9 条 out-of-scope**、**~14 条要求超纲新实验**；**真正成立且切题的核心 gap 仅 ~3–4 条**（#12 language-bias 量化最关键，其余 #7 子集 ablation、#3 β、#35 训练算力均为 minor）。
- **讽刺对比**：真正切中核心争议（novelty vs CogCoM / Chain-of-Focus）的是 **Reviewer qwzK（给 6 分）**；而给 4 分、轰 80 问的 R29m **恰恰没碰这个最该问的点**。

## 6 种应对模式（新增 POINT）

- `POINT` —（新）论文已答，**直接指向正文/附录**，零成本，最高性价比
- `CORRECT` — 基于错误前提，礼貌纠正 + 指向证据
- `ANSWER` — 真缺口且切核心，认真补证据
- `MINIMAL` — 真缺口但 minor，给最小回应/future work
- `DETAIL` — 可复现细节，打包进一个附录段
- `REFRAME` — out-of-scope，礼貌划界

## 逐条样板（代表性 9 条，覆盖全部模式）

### [#22] 7B vs 13B 的影响 — `CORRECT`
- **类型**：错误前提（Grounding：reviewer 漏读）· 切题=次要 · 可答=易
- **论文实情**：Sec 5.1 写明 "7B and 13B versions"；Table 11 报告 13B（MMStar 44.6 / ChartQA 71.7 / DocVQA 78.6）。
- **怎么回复**：礼貌纠正前提并指向 Table 11。
- **话术**：*"We respectfully refer the reviewer to Sec 5.1 and Table 11, where 13B results are reported (e.g., 44.6 on MMStar vs 41.7 for 7B). The parameter-size trade-off is therefore already analyzed."*
- **建议怎么做**：无需新实验；正文加一句话显式指引 Table 11，降低再次被漏读的概率。

### [#8] replay 触发机制 — `POINT`
- **类型**：论文已答（漏读）· 切题=**核心** · 可答=易
- **论文实情**：Sec 3 —— 模型生成 special replay-signal token，推理时 monitor 输出并解析其前的坐标，valid 则检索。
- **话术**：*"The trigger mechanism is described in Sec 3: VGR emits a learned replay-signal token; at inference we monitor the output stream and, upon this token, parse the preceding coordinates to retrieve features. We will add a pointer + an illustrative trace (Fig. 8)."*
- **建议怎么做**：不补实验，补一个 inference trace 图增强可读性。

### [#28] replay 延迟开销 — `POINT`
- **类型**：论文已答（漏读）· 切题=核心（效率是卖点）· 可答=易
- **论文实情**：附录 B.5 Table 15（replay 次数 / 生成 token / run time per task）+ A.2 Table 10（VGR 7.5s vs ZoomEye 48.5s）。
- **话术**：*"Latency is quantified in Appendix B.5 (Table 15) and A.2 (Table 10): VGR averages 7.5s/question vs ZoomEye's 48.5s. We will surface these into the main text."*

### [#12] language bias 量化 — `ANSWER`（这是真问题，砸字数）
- **类型**：真缺口（Grounding：成立）· 切题=**核心 claim** · 可答=中
- **论文实情**：全文无直接 language-bias 指标，仅 Table 5 间接（纯文本 CoT 掉点）。
- **怎么回复**：正面承接，补一个可在 rebuttal 期完成的量化。
- **话术**：*"Good point. Beyond the indirect evidence in Table 5, we add a language-bias probe: on a perception-heavy subset, we measure accuracy drop when the image is ablated/blurred for VGR vs the text-only-CoT baseline; a smaller drop indicates less language-prior reliance. Results: [...]."*
- **建议怎么做**：这是唯一真正威胁核心 claim 的点，**优先投入**；设计一个轻量 image-ablation 对照即可，无需大规模训练。

### [#11] 对比 GPT-4V/Gemini/Claude — `REFRAME`
- **类型**：out-of-scope · 切题=低 · 可答=难
- **论文实情**：VGR 定位是 LLaVA-NeXT-7B 之上的**可复现、公平对照**研究。
- **话术**：*"Our contribution is a method validated under a fair, reproducible setting on the open LLaVA-NeXT-7B baseline. Closed commercial systems differ in scale, data, and access, making a controlled comparison infeasible and orthogonal to our claim."*

### [#19] 跨域（医学/遥感）泛化 — `REFRAME`
- **类型**：out-of-scope · 切题=低 · 可答=难
- **话术**：*"Cross-domain transfer to medical/remote-sensing imagery is valuable but orthogonal to our contribution (a general visual-grounded reasoning mechanism), which we validate on standard VQA/OCR. Noted as future work."*

### [#9] 训练超参 — `POINT`+`DETAIL`
- **类型**：论文已答（漏读）· 切题=低（可复现）· 可答=易
- **论文实情**：附录 A 已列 lr / warmup 0.03 / cosine / zero decay / 1 epoch / ViT lr 1/10。
- **话术**：*"Full hyperparameters are in Appendix A. We will additionally consolidate them with #2/#17/#35 into a single Implementation-Details table for clarity."*

### [#7] VGR-SFT 各子集贡献 — `MINIMAL`
- **类型**：真缺口（成立）· 切题=次要 · 可答=难（需按子集重训）
- **论文实情**：Table 1 有数据分布，但无 per-subset ablation。
- **话术**：*"We agree a per-subset attribution is informative. Within the rebuttal window we add a leave-one-domain-out ablation on the two largest subsets (OCRVQA, GQA) as a representative probe; a full per-subset sweep is left to the camera-ready."*

### [#3] β=2 detection-loss ablation — `MINIMAL`
- **类型**：真缺口（成立，minor）· 切题=次要 · 可答=中
- **论文实情**：Sec 3 "β=2 following common practices"，无 ablation。
- **话术**：*"β=2 follows the DETR-family convention; within the rebuttal we add β∈{1,2,4} on MMStar/ChartQA to confirm robustness (results: [...])."*

## 给作者的总策略（grounded 版）

1. **先用一段 `POINT` 集中反指**："以下关切（#2/#8/#9/#17/#22/#24/#28/#33/#38）已在正文/附录回答，下面给出对应位置" —— 一次性化解 ~9 条，且专业地提示 reviewer 漏读了。
2. **字数砸 #12**（唯一威胁核心 claim 的真问题），给一个轻量但直接的 language-bias 量化。
3. **#7/#3/#35 等 minor 真缺口**：给最小证据或 camera-ready 承诺。
4. **out-of-scope（~9 条）**：礼貌但坚定 `REFRAME`，不做无关实验。
5. **给 AC 的 closing**：客观陈述"40 个独立关切中，9 项已在原文回答、若干超出本文范围、核心问题已补充实验"——让 meta-reviewer 看清这份 80 问 review 的真实结构。**只陈述事实，不评价 reviewer 动机。**

> 结论：这份 review 的 80 个编号，去冗余 + 反指已答 + 划界 out-of-scope 后，作者真正要动手的只有 **#12 + 2–3 个 minor**。读原文之前看不出这一点 —— 这就是 Grounding 维度的全部价值。
