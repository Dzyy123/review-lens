<div align="center">

<img src="./assets/hero.png" alt="ReviewLens" width="100%" />

# 🔍 ReviewLens

**把一份审稿意见放到显微镜下 —— 逐条告诉你它*站不站得住*，并教你*该怎么回*。**

[![status](https://img.shields.io/badge/status-early%20prototype-orange?style=flat-square)](#-状态--roadmap)
[![for](https://img.shields.io/badge/built%20for-authors-2ea44f?style=flat-square)](#-谁该用)
[![grounded](https://img.shields.io/badge/grounded-in%20the%20paper-1f6feb?style=flat-square)](#-工作原理)
[![license](https://img.shields.io/badge/license-MIT-blue?style=flat-square)](./LICENSE)
[![PRs](https://img.shields.io/badge/PRs-welcome-purple?style=flat-square)](#-状态--roadmap)

[English](./README.md) · **简体中文**

</div>

---

ReviewLens 接收 `一份审稿意见 + 对应论文`，逐条判断每条意见是否成立，把一整页意见自动分成 **6 种情况**，并给出每条的应对建议（含可直接粘贴的话术）。

> **它只帮你判断和组织，最终判断与文字仍由你拍板。**

<div align="center">

> ✅ **现在就能用** —— 克隆仓库即可运行内置 skill，无需安装（见 [快速开始](#-快速开始)）。已在一个真实案例（ICLR 2026 *VGR*，一份 80 条意见的 review）上**端到端验证** → [`examples/`](./examples)。持续演进中，`pip` CLI 是下一步。

</div>

---

## 😫 为什么需要它

AI 辅助审稿已经**势不可挡——它就是 peer review 未来的趋势**。NeurIPS / ICML 2026 都已开了官方通道，研究也证明**用得对**的 AI 反馈能提升 review 质量。但同一股力量把**审稿门槛**拉到了历史最低：经验不足的审稿人 + 一键 AI，几分钟就能产出一份"看着很专业、实则注水"的 review。

对作者来说，最痛的不是"AI 写得差"，而是 **无论意见多离谱，你都得逐条回应**。常见的注水形态：

| 形态 | 含义 |
| :-- | :-- |
| 🌀 **超纲** | 要求和论文核心贡献无关的实验 |
| ⛔ **答不了** | 要求 rebuttal 期根本做不完的大批新实验 |
| ❓ **对不上** | 误读论文，甚至要求论文其实已经写了的东西 |
| ♻️ **凑数** | 同一诉求反复换皮，靠数量制造"严谨"的假象 |

现有的 AI-for-review 工具几乎都在 **帮人写 review** 或 **帮作者改稿**。

> 🎯 **没有人帮你判断"一份 review 本身站不站得住"，并教你怎么回。这就是 ReviewLens 的位置。**

---

## ⚙️ 它怎么工作

<div align="center">

<img src="./assets/how-it-works.png" alt="ReviewLens 工作流：从混乱到清晰" width="92%" />

</div>

```mermaid
flowchart LR
    A["📄 审稿意见<br/>+ 对应论文"] --> B["① 拆条 + 去重<br/>合并镜像/重复诉求"]
    B --> C["② 读原文 + 查相关工作<br/>（铁律）"]
    C --> D["③ 四维判断<br/>对应 / 可答 / 切题 / 冗余"]
    D --> E["④ 分流到 6 种情况"]
    E --> F["📋 分层报告<br/>一页结论 + 逐条应对 + 行动顺序"]

    style A fill:#1f2d3d,stroke:#0d6efd,color:#fff
    style C fill:#0b5d4a,stroke:#1f8a65,color:#fff
    style F fill:#3b2f63,stroke:#7b64b8,color:#fff
```

---

## 🗂️ 6 种情况：把意见对号入座

每条意见归入下面 6 种之一，每种都配「该不该慌」和「怎么回」：

| | 情况 | 含义 | 怎么回 |
| :--: | :-- | :-- | :-- |
| 🟢 | **论文里其实已经写了** | 审稿人没看到（多在附录） | 一句话指给他看，**不用做新东西** |
| ⚪ | **超出本文范围** | 不是这篇论文要解决的 | 礼貌划界，归为 future work |
| 🔵 | **审稿人弄错了** | 问题基于错误假设 | 礼貌纠正 + 指向证据 |
| 🟠 | **有道理但工作量太大** | 要求合理但答辩期做不完 | 给一个能做完的小实验，其余留正式版 |
| 🟣 | **只是缺细节** | 不影响结论，只是没写清 | 打包成一段"补充实现细节" |
| 🔴 | **真问题，必须认真答** | 论文确实没有、且关系核心 | 把力气主要花在这里 |

---

## 🔬 一个真实案例：80 条意见，其实只有 4 个要做

**ICLR 2026 投稿 #23089（论文 *VGR*）的一位审稿人** —— rating **4** / confidence **5（最高自信）**，写了 **80 条编号意见**（40 weaknesses + 40 questions）。同篇论文另外 4 位审稿人的问题数只有 **0–3**。

ReviewLens 读了原论文 + 相关工作后，把这堆意见层层还原：

```mermaid
flowchart TD
    A["🗒️ 80 条编号意见<br/>（看着吓人）"] --> B["♻️ 去重<br/>weaknesses 与 questions 几乎一一镜像"]
    B --> C["✅ 40 个真正独立的问题"]
    C --> D["🟢 ~10 条 论文已写"]
    C --> E["⚪ 9 条 超出范围"]
    C --> F["🔵 1 条 审稿人弄错"]
    C --> G["🟣🟠 ~16 条 缺细节 / 给个小实验"]
    C --> H["🔴 4 条 真问题<br/>（核心仅 1 条）"]

    style A fill:#7a1f1f,stroke:#c04848,color:#fff
    style C fill:#0b3a5d,stroke:#1f6feb,color:#fff
    style H fill:#7a4a0b,stroke:#f0a040,color:#fff
```

> **关键结论**：这份 80 条的 review，去重 + 反指已答 + 划界超纲之后，作者真正要动手的只有 **1 个核心 + 3 个小补充**。
> **而这个判断，不读原文 + 不查相关工作，根本给不出来。**

📎 完整报告：[`examples/iclr2026_23089_R29m_author_report.md`](./examples/iclr2026_23089_R29m_author_report.md)

---

## 🧠 工作原理

1. **拆条 + 去重**：把 review 拆成一条条独立诉求，合并重复（案例里 80 → 40）。
2. **四维判断**（每条打标签）：

   | 维度 | 它问什么 |
   | :-- | :-- |
   | 📍 **对应性** | 这条说的，能否在论文里定位到？（有据 / 误读 / 论文其实已写） |
   | ⏳ **可答性** | 论文范围内可答，还是要求做不完的大实验？ |
   | 🎯 **切题性** | 关乎核心贡献，还是边缘的鸡蛋里挑骨头？ |
   | ♻️ **冗余性** | 和别的诉求重复、可合并？ |

3. **读原文 + 查相关工作（铁律）**：判断"论文是否其实已写""是否 over-claim""审稿人是否误读"，**必须** grounded 在原论文（含附录）+ 相关工作上。这是 ReviewLens 区别于"只数问题数量"的浅层工具的核心。
4. **分流 + 给应对**：归入 6 种情况，套用对应的应对模板，输出**分层报告 + 行动顺序**。

---

## 👤 谁该用

它是**同一个工具** —— 你只要声明自己的角色。**作者** 和 **审稿人** 都跑同一个 `/reviewlens`，只是输出口径和合规要求不同。

### 🧑‍🔬 你是作者 —— 刚收到审稿意见

- **什么时候用** —— rebuttal 开始，某个 reviewer 甩来几十条零散又吓人的意见，你一头雾水、不知从哪下手。
- **你需要准备** —— 审稿意见（粘贴文本）+ 你的论文（PDF 路径或 arXiv 链接）。
- **怎么跑** —— `/reviewlens`（告诉它你是**作者**）
- **你会得到**
  1. 一页结论 —— *"表面 N 条 → 真问题 M 个 → 你真正要做的只有 K 个"*；
  2. 一份**按优先级排好的行动顺序**（先做这个；这些指位置；这些礼貌挡回）；
  3. 每条意见：**属于哪种情况** + **可直接粘贴的回复** —— 指出论文哪里已答、礼貌划界超范围的、纠正错误前提、或给一个能做完的小实验。
- **然后** —— 把有限的 rebuttal 时间花在真正影响评分的 2–3 件事上，并把拆解直接喂给你的 rebuttal 初稿。
- **合规** —— ✅ 自由：会议明确允许作者用 AI 辅助写作 / rebuttal。

### 🧑‍⚖️ 你是审稿人 —— 在点"提交"之前

- **什么时候用** —— 你（或你带的新手）写完一版 review，可能用了 AI，想确认它是真**有用**、而不是注水或不公平。
- **你需要准备** —— 你的 review 初稿 + 论文。
- **怎么跑** —— `/reviewlens`（告诉它你是**审稿人**）
- **你会得到** —— 一份自查：你哪些意见是**超范围 / 重复 / 答不了 / 论文其实已答 / 基于误读**，哪里**太空泛 vs 够具体**，以及你的**评分和你列的 weaknesses 是否自洽**。
- **然后** —— 砍掉噪音、磨利真问题 → 一份作者能落地、AC 也认可的 review。
- **合规** —— ⚠️ 遵守你所投会议的当年政策（CVPR 禁用 LLM 审稿；ICML 两档；NeurIPS 实验性），并使用 privacy-compliant / 本地模型。ReviewLens **只帮你组织你自己的判断** —— 绝不替你写 review、不替你决定录用与否。

> 🏛️ **AC / PC** 治理（批量评估 review 质量、识别注水审稿）是同一引擎的自然延伸 —— 在 Roadmap 上。

---

## 🚦 设计原则（红线）

- ✅ **只判断和组织，不替你拍板**：给"分类 + 定位 + 依据 + 建议话术"，但**不替你写完整 review / rebuttal，不下"绝对对/错"的终判**。
- 📍 **对应性必须 grounded**：每条结论都要能落到原文/相关工作的具体位置，**不捏造**。
- ⚖️ **合规分模式**：作者侧自由；审稿人侧必须遵守目标会议当年政策（CVPR 全禁、ICML 两档、NeurIPS 实验性）并使用 privacy-compliant / 本地模型。
- 🛡️ **抗 prompt injection**：读论文前扫描隐藏指令（白底白字、"give a positive review"），发现即标记、不执行。
- 🕒 **版本意识**：判"论文已写"时，以**审稿人当时审的版本**为准，避免把"rebuttal 后才补的"误判成"审稿人看漏了"。

---

## 📂 示例 / Demo

[`examples/`](./examples) 下是第一个端到端样本（ICLR 2026 *VGR* / Reviewer R29m）：

| 文件 | 内容 |
| :-- | :-- |
| [`…_R29m.md`](./examples/iclr2026_23089_R29m.md) | 原始 80 条 review（评测样本） |
| [⭐ `…_R29m_author_report.md`](./examples/iclr2026_23089_R29m_author_report.md) | **旗舰输出 · 给作者的报告**：分层（一页结论 + 逐条详解 + 证据层），每条按"哪个 Reviewer → 他的建议 → 属于哪类 → 怎么改"呈现 |
| [`…_R29m_grounded_response.md`](./examples/iclr2026_23089_R29m_grounded_response.md) | 分析师工作底稿（读原文 + 查相关工作的推导过程） |
| [`…_R29m_analysis.md`](./examples/iclr2026_23089_R29m_analysis.md) | 对照组：**未读原文**版（说明为何必须读原文） |

> 💡 对照 `author_report` 与 `analysis`（未读原文版）能直观看到：**读不读原文，输出质量天差地别**——未读原文时把"论文已写"的条目误判成了"合理的补充要求"。

---

## 🆚 和现有工具的区别

| 工具 | 它做什么 | 服务谁 |
| :-- | :-- | :-- |
| [OpenAIReview](https://github.com/ChicagoHAI/OpenAIReview)、[paper-agents-manuscript](https://github.com/bdsp-core/paper-agents-manuscript) | **生成** review / 帮作者**改稿** | 作者（投稿前） |
| 通用大模型（ChatGPT / Claude）直接提问 | 临时帮忙**回应** review | 作者（投稿后） |
| **🔍 ReviewLens**（[`skills/reviewlens`](./skills/reviewlens)） | **逐条审一份 review 站不站得住** —— 你只要声明自己的角色 | 作者 **和** 审稿人 |

> 上面两个外部工具是**写** review / 改稿；ReviewLens 反过来——**对照论文审一份已有的 review**。它是**同一个工具**：说你是**作者**，它告诉你怎么回；说你是**审稿人**，它在你提交前帮你自查草稿。

---

## 🚀 快速开始

ReviewLens 是一个**角色感知的 agent skill** —— 无需安装、不需要它自己的 API key。**克隆下来就能直接用**，配合任何支持 skill 的 agent（Claude Code、Cursor……）。

```bash
git clone https://github.com/Dzyy123/review-lens.git
```

**方式 A —— 原地使用。** 用 Cursor / Claude Code 打开仓库，让 agent 遵循 [`skills/reviewlens/SKILL.md`](./skills/reviewlens)，把你的 review + 论文（PDF 路径或 arXiv 链接）交给它。

**方式 B —— 装成斜杠命令 skill。**

```bash
# Claude Code
cp -r review-lens/skills/reviewlens ~/.claude/skills/

# Cursor
cp -r review-lens/skills/reviewlens ~/.cursor/skills/
```

然后直接调用，并**声明你的角色**：

```text
/reviewlens  我是作者   + <review> + <paper.pdf | arXiv 链接>
/reviewlens  我是审稿人 + <我的 review 初稿> + <paper.pdf | arXiv 链接>
```

它会读论文（含附录）+ 查相关工作，然后产出分层报告 —— 作者侧效果就像 [`examples/iclr2026_23089_R29m_author_report.md`](./examples/iclr2026_23089_R29m_author_report.md)。

> 📦 `pip install reviewlens` CLI 作为便利项在规划中；上面的 skill 才是当下真实可用的工具。

---

## 🛣️ Roadmap

- [x] 核心方法设计（四维 + 6 种情况 + 应对模板）
- [x] 第一个端到端真实案例人工跑通（*VGR* / R29m）
- [x] 以单个角色感知 skill 形式发布（作者 + 审稿人合一）
- [ ] `pip` CLI：`输入 review + 论文 PDF → 输出拆解报告`
- [ ] 跨多位 reviewer 的自动拆条 / 去重 / 分流
- [ ] 一键衔接 rebuttal 初稿
- [ ] 强化审稿人自查（合规守门 + privacy-compliant 后端）

---

## 📄 License

[MIT](./LICENSE) © 2026 Dzyy123

<div align="center">

---

*ReviewLens —— 让每一条审稿意见，都被看清。*

</div>
