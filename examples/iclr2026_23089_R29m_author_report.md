# 审稿意见诊断报告 · 给作者（含证据层）

**论文**：VGR（ICLR 2026 投稿 #23089）　|　**来源**：https://openreview.net/forum?id=kDhAiaGzrn
**本报告由 ReviewLens 生成。**

> **证据来源**：本报告的每条判断都基于 (a) **VGR 原文正文 + 附录 A–E**；(b) **相关工作** CogCoM、Chain-of-Spot、Chain-of-Focus、ZoomEye；(c) 推理。
> 每条回复都尽量**用论文的实际设计 + 领域惯例**来论证，而不只是"我们写过"——后者是最弱的证据。最终判断与文字由你拍板。

---

## Layer 1 · 一页结论

### 一句话安心
80 条意见，去重后只有 **40 个问题**；其中一大半要么**论文已写**、要么**是另一条独立研究线（不该由本文回答）**。真正要动手认真补的只有 **1 个核心 + 3 个小补充**。

### 数字速览
| 表面条数 | 去重后 | 论文已写 | 超出范围 | 审稿人弄错 | 真问题 | 缺细节/给小实验 |
| :--: | :--: | :--: | :--: | :--: | :--: | :--: |
| 80 | 40 | ~10 | 9 | 1 | 4（核心 1） | ~16 |

### Reviewer 一览
| Reviewer | 评分 | 自信 | 条数 | 倾向 |
| --- | :--: | :--: | :--: | --- |
| **R29m（本报告对象）** | **4** | **5（最高）** | **80** | 断崖式 outlier，疑似 AI 批量生成 |
| qwzK | 6 | 3 | 0 | 真正问到核心争议（novelty vs CogCoM/CoF） |
| ysjS | 6 | 4 | 3 | 正常 |
| CQgV | 8 | 3 | 1 | 正面 |
| joFM | 4 | 4 | 0 | 偏负但简短 |

### 行动顺序
1. **🔴 只认真做 1 件**：#12（"减少语言偏见"没量化）—— 补轻量"遮图测准确率"实验。
2. **🟠 顺手补 3 个小的**：#3（β 对比）、#7（数据子集贡献）、#35（训练算力）。
3. **🟢 一句话化解 ~10 条**：论文已写，指位置（#2/#5/#8/#9/#17/#24/#28/#33/#38/#39）。
4. **🔵 纠正 1 条**：#22（他以为没做 13B，表 11 做了）。
5. **⚪ 礼貌挡回 9 条**：超出范围——**每条都用论文设计/数据/baseline 举证 + 指出是另一条独立研究线**（#10/#11/#15/#16/#19/#26/#32/#36/#40）。

> 核心论点（贯穿全篇）：VGR 是**"在全开源 LLaVA-NeXT-7B 上、不引入额外数据的受控公平研究"**（第 5.1 节原话："to ensure a fair comparison, all datasets constructed are derived from the original SFT data of LLaVA-Next without introducing any additional data"）。**这条定位本身，就是大量"超范围"意见最有力的反驳依据。**

---

## Layer 2 · 逐条详解（40 条，按原编号；weaknesses 与 questions 已合并）

> 每条五段：① Reviewer ② 建议 ③ 判断 ④ 证据（📄论文 / 🔬别人做过·标准 / 🎯是否超范围）⑤ 怎么回。

### #1 ｜ 没对比不同大小的冷启动模型
- **① Reviewer**：R29m
- **② 建议**：用 72B 生成初始数据，没说明为何不用 13B/34B。
- **③ 判断**：【有理但太大】
- **④ 证据**：
  - 📄 论文：冷启动只是数据 pipeline 第一步（Sec 4.1），且 4.3 已证明**用更小的 14B 标注模型即可把通过率从 14% 提到 40%、提速 3.2×**，说明冷启动模型规模并非性能瓶颈。
  - 🔬 别人做过吗：用强 MLLM 做数据冷启动是当前标准做法（如 Vision-R1 用 DeepSeek-R1 蒸馏），选最强可用模型是惯例。
- **⑤ 怎么回**：
  - 做实验：不用全做。
  - 回复（英文）：*"The 72B model is used only for cold-start generation, where output quality matters most; we then show (Sec 4.3) that a much smaller 14B annotator suffices, raising pass rate 14%→40%. A size sweep at cold-start is thus orthogonal to the method."*
  - 中文说明：用论文自己的 4.3 结论证明"冷启动规模不是关键"，化解这条。

### #2 ｜ 没给数据过滤工具的参数
- **① Reviewer**：R29m
- **② 建议**：Doubao1.5-VL 的参数/验证标准/prompt 没给。
- **③ 判断**：【论文已写】
- **④ 证据**：📄 **附录 D.1 明确写了**：用官方默认参数、max 8192；三个验证 prompt 全列在 Table 17。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"These are specified in Appendix D.1 (default settings, max length 8192) with the exact prompts in Table 17; we add a pointer."* 中文：指位置。

### #3 ｜ β=2 没做对比实验
- **① Reviewer**：R29m
- **② 建议**：检测损失 β=2 无 ablation。
- **③ 判断**：【真问题（小）】
- **④ 证据**：
  - 📄 论文：Sec 3 写 "β=2 following common practices"，确实未单独 ablate。
  - 🔬 别人做过吗：**是标准做法**——L1 + GIoU 加权是 DETR 系检测的通行配置，β=2 是常见默认，不是论文随意取的。
- **⑤ 怎么回**：
  - 做实验：补一个小的（MMStar/ChartQA 上 β∈{1,2,4}）。
  - 回复（英文）：*"β=2 follows the established DETR-family convention for L1+GIoU weighting. To confirm robustness, we add β∈{1,2,4} on MMStar/ChartQA (results: …)."*
  - 中文：先说这是标准做法，再补小实验显严谨。

### #4 ｜ 和 ZoomEye 比的数据集不够
- **① Reviewer**：R29m
- **② 建议**：和 ZoomEye 只比了 V*Bench、HR-Bench。
- **③ 判断**：【论文已写】（大部分）
- **④ 证据**：📄 **附录 A.2（Table 10）**已有 ZoomEye 对比，含关键的**速度差**（VGR 7.5s vs ZoomEye 48.5s）。🔬 ZoomEye 是 training-free 树搜索，本就慢；效率正是 VGR 的卖点，已对比到位。
- **⑤ 怎么回**：基本不用，可加 1 个 benchmark。回复：*"A detailed ZoomEye comparison incl. inference time is in Appendix A.2 (VGR 7.5s vs 48.5s); we add TextVQA to broaden coverage."* 中文：指向 A.2 + 小补。

### #5 ｜ 没试更多视觉/语言主干
- **① Reviewer**：R29m
- **② 建议**：没试 InternViT-6B、LLaMA-3 等。
- **③ 判断**：【论文已写】
- **④ 证据**：📄 **表 3 已测 3 种组合**（Qwen2.5+SigLIP、Qwen2.5+InternViT、Vicuna+CLIP），全部提升。🔬 用 2–3 个主干验证通用性是领域惯例，论文已达标。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"Table 3 reports three encoder/LLM combinations (incl. InternViT), all showing consistent gains, evidencing backbone-agnostic generality."* 中文：指向表 3。

### #6 ｜ 裁剪上限 64 的依据
- **① Reviewer**：R29m
- **② 建议**：测试时最多 64 张的依据不清。
- **③ 判断**：【论文已写】（部分）
- **④ 证据**：📄 表 8 已给 20 vs 64 的 scaling 对比；5.4 说明**多数图达不到该分辨率**，64 是经验上界。🎯 否。
- **⑤ 怎么回**：可选补一点。回复：*"Table 8 shows the 20→64 scaling; 64 is an empirical upper bound rarely reached by our data. We add an intermediate point."* 中文：指向表 8。

### #7 ｜ 没分析各数据子集的贡献
- **① Reviewer**：R29m
- **② 建议**：没分析 AI2D/GQA/ChartQA 各自贡献。
- **③ 判断**：【真问题（小）】
- **④ 证据**：📄 表 1 有数据分布，但无 per-subset ablation。🔬 per-subset 贡献分析是合理但非必备的加分项。
- **⑤ 怎么回**：补最小的（对两个最大子集 OCRVQA/GQA 做 leave-one-out）。回复：*"We add a leave-one-domain-out ablation on the two largest subsets (OCRVQA, GQA) as a representative probe; a full per-subset sweep is left to camera-ready."* 中文：代表性小实验。

### #8 ｜ 没说清何时触发"回看"
- **① Reviewer**：R29m
- **② 建议**：推理时怎么决定何时检索视觉记忆。
- **③ 判断**：【论文已写】
- **④ 证据**：📄 **第 3 节写了**：模型生成 replay-signal 特殊 token，推理时监控输出、检测到该 token 即解析其前坐标并检索（"VGR monitors the model output and, upon detecting signal token, parses the preceding content to extract the region coordinates"）。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"The trigger is described in Sec 3: VGR emits a learned replay-signal token and we parse the preceding coordinates to retrieve features. We add an illustrative inference trace (Fig. 8)."* 中文：指第 3 节 + 补示例图。

### #9 ｜ 训练超参数没给全
- **① Reviewer**：R29m
- **② 建议**：缺 batch size/epoch/weight decay/lr schedule。
- **③ 判断**：【论文已写】
- **④ 证据**：📄 **附录 A 已列**：lr 1e-5/2e-5、warmup 0.03、cosine、zero decay、1 epoch、ViT lr=1/10。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"Full hyperparameters are in Appendix A; we will consolidate them with #2/#17/#35 into one Implementation-Details table."* 中文：指附录 A + 打包。

### #10 ｜ 没测低分辨率图
- **① Reviewer**：R29m
- **② 建议**：没评估 224×224、112×112 低分辨率表现。（原文："performance on low-resolution images … is not evaluated"）
- **③ 判断**：【超出范围】
- **④ 证据**：
  - 📄 论文：VGR 的设计目标明确是 **"comprehensive understanding of image details"（abstract）**，整套架构——AnyRes crop 从 4 扩到 16、expand-then-compress、visual memory pool——都是**为高分辨率细节服务**（Sec 3）；评测也专挑 **V*Bench、HR-Bench 8K** 这类高分辨率任务。
  - 🔬 别人做过吗：低分辨率/退化鲁棒性是**另一条研究线**（有 ImageNet-C 等专门 benchmark）；VGR 的近邻工作 CogCoM、Chain-of-Spot、ZoomEye 同样**不在 112/224 低分辨率上评测**。
  - 🎯 是否超范围：**是**。要求一个"为高分辨率细粒度而生"的方法去证明低分辨率表现，与它要解决的问题正交，甚至自相矛盾。
- **⑤ 怎么回**：
  - 做实验：不用。
  - 回复（英文）：*"VGR is explicitly designed for fine-grained reasoning on high-resolution inputs—our architecture (AnyRes expanded 4→16 crops, visual memory pool) and benchmarks (V\*Bench, HR-Bench 8K) all target this regime. Sub-336px robustness is a distinct research line (cf. corruption-robustness benchmarks) not addressed by comparable region-grounding methods (CogCoM, Chain-of-Spot) either; we note it as future work."*
  - 中文说明：用论文的设计目标 + 评测选择举证"低分辨率与本文目标相悖"，并指出同行也不测。

### #11 ｜ 没和 GPT-4V 等商业模型比
- **① Reviewer**：R29m
- **② 建议**：只比开源模型，没和 GPT-4V/Gemini/Claude 比。
- **③ 判断**：【超出范围】
- **④ 证据**：
  - 📄 论文：VGR 刻意采用**受控公平设定**——第 5.1 节原话 *"to ensure a fair comparison, all datasets constructed are derived from the original SFT data of LLaVA-Next without introducing any additional data"*，baseline 选 *"a well-known and fully open-sourced baseline"*。整个实验是"同数据、同 baseline"的对照。
  - 🔬 别人做过吗：与闭源商业模型对比**不是该子领域的标准主对比**；CogCoM、Chain-of-Spot、ZoomEye 等同类开源 grounding 方法都以**开源 baseline 为主对比**。
  - 🎯 是否超范围：**是**。GPT-4V/Gemini 在规模、训练数据、访问方式上不可控，纳入会**直接破坏论文刻意维持的公平对照**，反而削弱结论的可信度。
- **⑤ 怎么回**：
  - 做实验：不用。
  - 回复（英文）：*"Our contribution is a controlled, reproducible study: we add VGR's data on top of the open LLaVA-NeXT-7B baseline without introducing extra data (Sec 5.1), isolating the effect of the method itself. Closed commercial systems differ uncontrollably in scale, data and access; benchmarking against them would break this controlled comparison—which is why comparable open grounding methods (CogCoM, Chain-of-Spot, ZoomEye) also compare against open baselines."*
  - 中文说明：用"公平对照是论文的方法论根基"举证，纳入闭源反而有害；并指出同行惯例。

### #12 ｜ 没量化"语言偏见"减少了多少 🔴 核心
- **① Reviewer**：R29m
- **② 建议**：反复说"减少语言偏见"却无指标量化。
- **③ 判断**：【真问题（核心）】—— 唯一动摇核心卖点的意见。
- **④ 证据**：
  - 📄 论文：标题/abstract/intro 都打"language bias"这张牌，但全文只有 **Table 5（纯文本 CoT 反而掉点）的间接证据**，确无直接指标。这是真短板。
  - 🔬 别人做过吗：语言偏见/视觉依赖度有可借鉴的量化思路（如图像消融下的性能落差），实现成本低。
- **⑤ 怎么回**：
  - 做实验：**优先做**（轻量，不用重训）。拿"必须看图才能答"的子集，把图遮住/打乱，测准确率落差；VGR 落差 < 纯文本 CoT 基线 = 更依赖看图而非语言先验。
  - 回复（英文）：*"Beyond the indirect evidence in Table 5, we add a direct language-bias probe: accuracy drop under image ablation on a perception-heavy subset. VGR degrades by Δ_VGR vs Δ_baseline (Δ_VGR < Δ_baseline), directly quantifying reduced reliance on language priors."*
  - 中文说明：正面把核心 claim 补成可量化证据。

### #13 ｜ 高分辨率裁剪细节不全
- **① Reviewer**：R29m ·**② 建议**：缺 crop 重叠率/数量/位置标准。·**③ 判断**：【只缺细节】
- **④ 证据**：📄 Sec 3 给了 expand-then-compress 和 pooling 策略，但裁剪几何细节没写全。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"We will add these cropping parameters to the Implementation-Details appendix."* 中文：打包补充。

### #14 ｜ 没报标注错误率
- **① Reviewer**：R29m ·**② 建议**：没说 VGR-SFT 标注错误率与处理。·**③ 判断**：【只缺细节】
- **④ 证据**：📄 reject sampling（Sec 4.2）+ 三道验证已是质控机制，但没报最终错误率统计。🎯 否。
- **⑤ 怎么回**：不用做新实验，统计现有数据。回复：*"Our three-stage reject-sampling (Sec 4.2) filters errors; we will report the resulting pass/reject rates and error types."* 中文：指流程 + 补统计。

### #15 ｜ 没测不同硬件/CPU 速度
- **① Reviewer**：R29m
- **② 建议**：没报 A100/3090/4090/CPU 的速度与显存。
- **③ 判断**：【超出范围】（CPU 部分）
- **④ 证据**：📄 论文**已报延迟**——附录 B.5（Table 15，H20，per-task run time）+ A.2（VGR 7.5s vs ZoomEye 48.5s）。🎯 跨多硬件尤其 CPU 的全面 profiling 是**部署工程问题**，与方法贡献正交；而且 7B+ MLLM 跑 CPU 本就非现实部署场景。
- **⑤ 怎么回**：不用做。回复：*"Per-task latency is already reported in Appendix B.5 (H20) and A.2 (VGR 7.5s vs ZoomEye 48.5s). Exhaustive cross-hardware—and CPU—profiling is deployment-specific and orthogonal to the method; CPU inference is not a realistic regime for 7B-scale MLLMs."* 中文：先指已测延迟，再把跨硬件/CPU 作为部署问题挡回。

### #16 ｜ 没测多轮对话
- **① Reviewer**：R29m
- **② 建议**：只测单轮，没测多轮连续推理。
- **③ 判断**：【超出范围】
- **④ 证据**：📄 论文的问题设定就是**单轮 grounding-then-answer**（Sec 3 inference pipeline）。🔬 多轮多图是**另一设定**——CogCoM 虽支持 multi-turn multi-image，那是其不同的设计目标；VGR 与 Chain-of-Spot 都聚焦单轮 ROI 推理。🎯 是。
- **⑤ 怎么回**：不用做。回复：*"VGR addresses single-turn visual-grounded reasoning (Sec 3). Multi-turn dialogue is a different problem setting; while CogCoM targets multi-turn multi-image by design, our contribution—and comparable single-turn methods like Chain-of-Spot—focus on per-query grounding. Noted as future work."* 中文：用问题设定 + 相关工作分工举证。

### #17 ｜ 数据过滤阈值不清楚
- **① Reviewer**：R29m ·**② 建议**：ANLS/语义对齐阈值不清。·**③ 判断**：【论文已写】
- **④ 证据**：📄 **附录 D.1 写了**：分数 0–5、阈值=3；ANLS 用于 closed-ended。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"Thresholds are in Appendix D.1 (score 0–5, cutoff 3; ANLS for closed-ended); we add a pointer."* 中文：指 D.1。

### #18 ｜ 检测头结构没说
- **① Reviewer**：R29m ·**② 建议**：检测头 MLP 层数/维度/激活没写。·**③ 判断**：【只缺细节】
- **④ 证据**：📄 Sec 3 说了是 "a small MLP" 映射到 4 维框，但没给细节。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"We will specify the detection-head MLP (layers, hidden dim, activation, 4-dim output) in the appendix."* 中文：补细节。

### #19 ｜ 没测医学/遥感等跨领域
- **① Reviewer**：R29m
- **② 建议**：只测常规 VQA/OCR，没测医学影像、遥感等专业域。（原文点名 ChestX-ray14、NWPU-RESISC45）
- **③ 判断**：【超出范围】
- **④ 证据**：
  - 📄 论文：**训练数据 Table 1 全是通用域**——AI2D(ScienceQA)、LLaVA-COCO/GQA(General VQA)、ChartQA/DVQA/DocVQA/OCRVQA(OCR)，**没有任何医学/遥感数据**；论文贡献定位是"**通用视觉 grounded 推理机制**"，不是特定域适配。
  - 🔬 别人做过吗：医学/遥感 VLM 是**有专门预训练和数据的独立研究线**；VGR 的近邻 CogCoM、Chain-of-Spot、ZoomEye 同样**只在通用 VQA/OCR/science 上评测**。
  - 🎯 是否超范围：**是**。在没有相应训练数据的前提下要求专业域泛化，等于要求一个不同的研究项目。
- **⑤ 怎么回**：
  - 做实验：不用。
  - 回复（英文）：*"VGR is trained entirely on general VQA/OCR/science data (Table 1) and contributes a general visual-grounded reasoning mechanism, not domain adaptation. Specialized domains such as medical or remote-sensing imagery constitute separate research lines with dedicated pretraining/data; comparable methods (CogCoM, Chain-of-Spot, ZoomEye) likewise evaluate only on general benchmarks. We note cross-domain transfer as future work."*
  - 中文说明：用训练数据构成 + 贡献定位 + 同行范围举证"这是另一个研究项目"。

### #20 ｜ 改写用的模型怎么选的
- **① Reviewer**：R29m ·**② 建议**：data rewriting 的 MLLM 没说怎么选、没对比。·**③ 判断**：【有理但太大】
- **④ 证据**：📄 改写只是数据清洗的一环（Sec 4.3 / D.1），用于对齐 ground-truth 和精简推理链，非性能关键路径。🔬 用强 MLLM 做数据清洗是惯例。
- **⑤ 怎么回**：不用全做。回复：*"Rewriting (Sec 4.3) is a data-cleaning step to align with ground truth and condense reasoning, not a performance-critical component; we use a strong instruction-following MLLM as is standard, and a rewriter comparison is orthogonal."* 中文：说明它非关键路径 + 选型是惯例。

### #21 ｜ pooling 尺寸没对比
- **① Reviewer**：R29m ·**② 建议**：为何 2×2 和 4×4，没和 3×3/5×5 比。·**③ 判断**：【论文已写】（部分）
- **④ 证据**：📄 **表 7 已 ablate 不同 replay/pooling 策略**，并解释为何 base/replay 用细 pooling、local 用粗 pooling（局部 crop 冗余多）。🎯 否。
- **⑤ 怎么回**：基本不用。回复：*"Table 7 ablates pooling/replay strategies and motivates the 2×2 (base/replay) vs 4×4 (local) choice by the redundancy of local crops. We can add one more stride for completeness."* 中文：指表 7。

### #22 ｜ 没分析 7B vs 13B 🔵 他弄错了
- **① Reviewer**：R29m
- **② 建议**：没深入分析 7B vs 13B 的性能/成本权衡。（原文："impact of model parameter size (7B vs 13B) … is not analyzed in depth"）
- **③ 判断**：【审稿人弄错了】
- **④ 证据**：📄 **论文其实做了 13B**：第 5.1 节明确写了 Vicuna "7B and 13B versions"；**表 11 报告 13B 结果**（MMStar 44.6 / ChartQA 71.7 / DocVQA 78.6，对比 7B 的 41.7 等）。审稿人的前提"没分析 13B"不成立。🎯 否。
- **⑤ 怎么回**：
  - 做实验：不用。
  - 回复（英文）：*"We respectfully clarify that 13B results are reported: Sec 5.1 covers both 7B and 13B, and Table 11 gives the 13B numbers (e.g., 44.6 on MMStar vs 41.7 for 7B). The parameter-size trade-off is therefore already in the paper; we will make the pointer explicit."*
  - 中文说明：礼貌纠正错误前提 + 指向表 11（最有力的回应——直接证明他看漏了）。

### #23 ｜ 数据集任务是否平衡
- **① Reviewer**：R29m ·**② 建议**：没说各任务类型是否平衡。·**③ 判断**：【只缺细节】
- **④ 证据**：📄 **表 1 已给各数据集数量与类型**（OCR 占比偏高），但没专门讨论平衡处理。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"Table 1 gives the per-dataset composition and types; we will add a short note on task-type balance."* 中文：指表 1 + 补一句。

### #24 ｜ 没有区域选择准确率指标
- **① Reviewer**：R29m
- **② 建议**：怎么衡量选对了关键区域，没给指标。
- **③ 判断**：【论文已写】
- **④ 证据**：📄 **附录 B.3（Table 13）报告 RefCOCO/RefCOCOg/RefCOCO+ 的 IoU 与 acc@0.5**，B.5 还报告**有效框比例 97.5–98.9%**；甚至 B.2（Table 12）做了"随机错框会显著掉点"的对照，证明 grounding 质量直接影响结果。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"Region-selection quality is quantified in Appendix B.3 (RefCOCO IoU/acc@0.5) and B.5 (97.5–98.9% valid boxes); Table 12 further shows that corrupting boxes degrades performance, confirming grounding fidelity matters. We will surface these."* 中文：指 B.3/B.5/B.2，证据相当充分。

### #25 ｜ 重复样本怎么处理
- **① Reviewer**：R29m ·**② 建议**：重复样本怎么检测/处理。·**③ 判断**：【只缺细节】
- **④ 证据**：📄 数据 pipeline（附录 D.2）描述了改写时去重（"removes duplicate bounding-boxes"），但无样本级去重统计。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"Our pipeline (Appendix D.2) includes de-duplication during rewriting; we will add sample-level dedup statistics."* 中文：指 D.2 + 补统计。

### #26 ｜ 没测 few-shot
- **① Reviewer**：R29m
- **② 建议**：没测 1K/10K/50K 小数据量表现。
- **③ 判断**：【超出范围】
- **④ 证据**：📄 VGR 是 **SFT 框架**（pre-train + SFT，Sec 5.1），论文研究的是"数据构造 + 架构"，不是低数据适应。🔬 few-shot/low-data 是**另一范式**；同类 SFT grounding 方法也不在 few-shot 上评测。🎯 是。
- **⑤ 怎么回**：不用做。回复：*"VGR is an SFT framework studying data construction and architecture; few-shot/low-data adaptation is a distinct paradigm not targeted here, consistent with comparable SFT-based grounding methods. Noted as future work."* 中文：用框架范式举证。

### #27 ｜ 和 Chain-of-Spot 比得不够
- **① Reviewer**：R29m ·**② 建议**：和 Chain-of-Spot 的逻辑/复杂度/性能比得不够。·**③ 判断**：【有理但太大】
- **④ 证据**：📄 **附录 E 已定性区分**：Chain-of-Spot 是"grounding-then-crop / 重复处理裁剪图"，VGR 是"预编码一次 + 检索 token"，更高效。🔬 这正是 VGR 相对该类工作真实的 novelty delta（中等但成立）。
- **⑤ 怎么回**：补一点定性 + 可选 1 定量点。回复：*"Appendix E contrasts the mechanisms (pre-computed visual memory vs repeated cropping); we add a brief quantitative comparison on V\*Bench."* 中文：指附录 E + 小补。

### #28 ｜ 没讨论"回看"的延迟
- **① Reviewer**：R29m
- **② 建议**：visual memory replay 的额外延迟没量化。
- **③ 判断**：【论文已写】
- **④ 证据**：📄 **附录 B.5（Table 15）报告了 replay 次数 / 生成 token / run time**，A.2（Table 10）给出 VGR vs ZoomEye 的 wall-clock（7.5s vs 48.5s）。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"Latency is quantified in Appendix B.5 (replay count, generated tokens, run time) and A.2; VGR averages 7.5s/question vs ZoomEye's 48.5s. We will surface these into the main text."* 中文：指附录。

### #29 ｜ 可解释性分析不够
- **① Reviewer**：R29m ·**② 建议**：除了区域标注，没有 attention 等可解释性。·**③ 判断**：【只缺细节】
- **④ 证据**：📄 已有 case study（Fig 6/8 展示 VGR 定位区域 + 据此推理）。🎯 否（可补可视化）。
- **⑤ 怎么回**：不用做实验，补图。回复：*"Beyond the case studies (Figs 6, 8), we add region-overlay/attention visualizations to illustrate visual-linguistic integration."* 中文：补可视化。

### #30 ｜ 数据集 license 没说全
- **① Reviewer**：R29m ·**② 建议**：没列具体 license/限制。·**③ 判断**：【只缺细节】
- **④ 证据**：📄 伦理声明称数据均公开且按 license 使用，但没逐个列出。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"All datasets are public and used under their licenses (Ethics Statement); we will add a table listing each license explicitly."* 中文：补 license 表。

### #31 ｜ 预训练/微调数据怎么合的
- **① Reviewer**：R29m ·**② 建议**：558K 与 770K+VGR-SFT 怎么融合没说。·**③ 判断**：【只缺细节】
- **④ 证据**：📄 第 5.1 说沿用 LLaVA-NeXT 设置、明确 pre-train=558K、SFT=770K+158K，但融合方式（拼接/混批）没明写。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"We follow LLaVA-NeXT's two-stage setup (pre-train 558K; SFT 770K+158K); we will state the exact mixing scheme in the appendix."* 中文：指 5.1 + 补一句。

### #32 ｜ 没测遮挡/模糊等复杂场景
- **① Reviewer**：R29m
- **② 建议**：没测遮挡、模糊、低光等噪声鲁棒性。
- **③ 判断**：【超出范围】
- **④ 证据**：📄 论文针对的是"高分辨率细节理解"（abstract），benchmark 是标准 VQA/OCR，**不是 corruption robustness**。🔬 图像退化鲁棒性有**专门 benchmark（ImageNet-C、VQA-C）和研究线**。🎯 是。
- **⑤ 怎么回**：不用做。回复：*"VGR targets fine-grained understanding on standard high-resolution benchmarks; corruption robustness (occlusion/blur/low-light) is a separate axis with dedicated benchmarks (e.g., ImageNet-C) and is orthogonal to our contribution. Noted as future work."* 中文：用论文目标 + 已有专门 benchmark 举证。

### #33 ｜ 框坐标怎么归一化没说
- **① Reviewer**：R29m
- **② 建议**：GIoU loss 里 bbox 坐标怎么归一化没说清。
- **③ 判断**：【论文已写】
- **④ 证据**：📄 **第 5.3 节明确写了归一化到 [0,1]**（"boxes are represented by floating-point coordinates normalized to the [0,1] range"），附录 Table 16 还做了 **absolute vs normalized 坐标的对照实验**。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"Coordinates are normalized to [0,1] (Sec 5.3), and Table 16 even compares absolute vs normalized coordinates. We will add a pointer."* 中文：指 5.3 + 表 16（还做了对照，证据很足）。

### #34 ｜ 没度量视觉-语言相关性
- **① Reviewer**：R29m ·**② 建议**：没度量标注区域与推理链的相关性。·**③ 判断**：【有理但太大】
- **④ 证据**：📄 数据 pipeline 的 Visual Grounding Verification（Sec 4.2）已用 MLLM 检查框内容与标签对齐，间接保证相关性，但无聚合指标。
- **⑤ 怎么回**：给一个轻量度量。回复：*"Our grounding verification (Sec 4.2) already checks region-label alignment per sample; we add an aggregate relevance metric (similarity between region labels and the reasoning steps citing them)."* 中文：指已有验证 + 补聚合指标。

### #35 ｜ 没报训练用了多少算力
- **① Reviewer**：R29m ·**② 建议**：没说 GPU 数/GPU 时/总时间。·**③ 判断**：【真问题（小）】
- **④ 证据**：📄 附录提了效率（标注提速 3.2×、延迟用 H20 测），但**确实没报训练总算力**。🔬 报告 GPU-hours 是可复现性标准要求。
- **⑤ 怎么回**：不用做实验，补数字。回复：*"We will report the full training compute (GPUs, GPU-hours, wall-clock for pre-training and fine-tuning)."* 中文：直接补算力。

### #36 ｜ 没结合强化学习
- **① Reviewer**：R29m
- **② 建议**：只用 SFT，没探索结合 RL（GRPO）。
- **③ 判断**：【超出范围】
- **④ 证据**：
  - 📄 论文 **Discussion 已明确把 RL 列为 future work**（"Another avenue is integrating reinforcement learning (RL)…"）。
  - 🔬 别人做过吗：**RL-based 视觉推理本身就是一条独立且活跃的研究线**——论文 Related Work 就引了 VLM-R1、Visual-RFT、Chain-of-Focus 都用 GRPO/RL。**这恰恰证明 RL 是另一条路径，而非本文（SFT 路线）的缺口。**
  - 🎯 是否超范围：**是**，且这是最干净的一条。
- **⑤ 怎么回**：
  - 做实验：不用。
  - 回复（英文）：*"As stated in our Discussion, RL integration is explicitly future work. RL-based visual reasoning is itself a separate, active line (e.g., VLM-R1, Visual-RFT, Chain-of-Focus, all using GRPO), which underscores that it is a distinct research direction rather than a gap in our SFT-based contribution."*
  - 中文说明：用"论文已声明 future work + RL 是独立研究线（有专门工作）"双重举证。

### #37 ｜ 推理链长度的影响没测
- **① Reviewer**：R29m ·**② 建议**：没测推理链长短对性能/效率影响。·**③ 判断**：【有理但太大】
- **④ 证据**：📄 表 6c 已间接相关——对比 annotated（长）vs rewritten（短）数据，发现**短而精的数据更好**，已部分回应"长度"问题。
- **⑤ 怎么回**：可指向 6c + 补最小对照。回复：*"Table 6c already contrasts long (annotated) vs short (rewritten) reasoning data, showing concise data performs better; we add a short-vs-long chain-length probe on MMStar."* 中文：指 6c + 小补。

### #38 ｜ 14B 标注模型泛化没评
- **① Reviewer**：R29m
- **② 建议**：扩数据时 14B 标注模型在未见数据上的泛化没评。
- **③ 判断**：【论文已写】
- **④ 证据**：📄 **附录 A.1（Table 9）已对比** cold-start(72B) 数据 vs annotator(14B) 数据训练的 VGR，性能相当；并报告通过率 14%→40%、提速 3.2×。🎯 否。
- **⑤ 怎么回**：不用做。回复：*"Appendix A.1 (Table 9) compares VGR trained on 72B-cold-start vs 14B-annotator data (comparable performance) and reports the 14%→40% pass-rate gain; we add a pointer."* 中文：指 A.1。

### #39 ｜ 减 70% token 会不会丢信息
- **① Reviewer**：R29m
- **② 建议**：减 70% 视觉 token 在密集物体/复杂布局会不会丢信息。
- **③ 判断**：【论文已写】（核心实验）
- **④ 证据**：📄 **表 2 正是核心证据**——VGR 用 30% token 仍超 baseline（ChartQA +12.9 等），说明"聚焦关键区域 > 堆 token"；表 7 进一步显示 token 配置的权衡。不过"密集场景"专项确可补。🎯 否。
- **⑤ 怎么回**：可选补一个密集场景对照。回复：*"Table 2 shows VGR surpasses the baseline with only 30% of tokens (e.g., +12.9 on ChartQA), evidencing that focusing on key regions beats adding tokens; to address dense scenes specifically, we add a comparison on a detail-rich subset."* 中文：先指核心结果，再针对密集场景小补。

### #40 ｜ 没测多语言
- **① Reviewer**：R29m
- **② 建议**：只用英文，没测多语言。
- **③ 判断**：【超出范围】
- **④ 证据**：📄 论文所有训练数据（Table 1）与 benchmark（MMStar/ChartQA/…）**均为英文**；贡献是视觉 grounding 机制，与语言覆盖正交。🔬 多语言 MLLM 是**专门方向**；同类 grounding 工作也英文为主。🎯 是。
- **⑤ 怎么回**：不用做。回复：*"All training data and benchmarks are English (Table 1); multilingual evaluation is a separate direction orthogonal to our visual-grounding contribution, consistent with comparable works. Noted as future work."* 中文：用数据/评测语言 + 同行范围举证。

---

## 给 AC 的 closing（建议放 rebuttal 结尾，只讲事实）

> *"We thank Reviewer R29m for the detailed list. The 80 numbered items correspond to 40 distinct concerns (weaknesses and questions mirror each other). Of these, ~10 are already addressed in the paper or appendix (we now add explicit pointers—e.g., 13B results in Table 11, latency in Appendix B.5, coordinate normalization in Sec 5.3); 9 concern separate research lines outside the scope of a controlled study on the open LLaVA-NeXT-7B baseline (low-resolution, closed-model comparison, cross-domain/medical, multilingual, RL—the latter explicitly noted as future work and pursued by dedicated works such as VLM-R1/Chain-of-Focus); and the remaining—most importantly the quantification of language-bias reduction (#12)—are addressed with new targeted experiments. We have responded to every distinct concern."*

中文意思：客观陈述 80→40 的结构、各类占比与举证依据（哪些已在论文、哪些是独立研究线、核心问题已补实验）——**只讲事实，不评价 reviewer 动机**，把判断留给 AC。

---

> 本报告只做诊断与建议；每条的最终判断与回复文字，请你自己拍板。
