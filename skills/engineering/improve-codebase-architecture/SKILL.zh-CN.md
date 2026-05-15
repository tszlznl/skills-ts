---
name: improve-codebase-architecture
description: 基于 `CONTEXT.md` 的领域语言（domain language）与 `docs/adr/` 的决策，寻找代码库中“加深（deepening）”的机会。用户想改进架构（architecture）、寻找重构机会（refactoring opportunities）、整合紧耦合模块（tightly-coupled modules），或让代码库更可测试（testable）且更易被 AI 导航（AI-navigable）时使用。
---

# Improve Codebase Architecture（改进代码库架构）

暴露架构摩擦点（architectural friction），并提出 **加深机会（deepening opportunities）**——把浅模块（shallow modules）重构为深模块（deep ones）。目标是提升可测试性（testability）与 AI 导航性（AI-navigability）。

## 术语表（Glossary）

下面这些词必须在每条建议里严格按原样使用。保持一致语言是重点——不要漂移到 “component”“service”“API”“boundary”。完整定义见 [LANGUAGE.zh-CN.md](LANGUAGE.zh-CN.md)。

- **Module（模块）** — 任何有 interface（接口）与 implementation（实现）的东西（函数、类、包、切片）。
- **Interface（接口）** — 调用方正确使用模块必须知道的一切：类型（types）、不变量（invariants）、错误模式（error modes）、调用顺序（ordering）、配置（config）等。不是只有类型签名（type signature）。
- **Implementation（实现）** — 模块内部的代码。
- **Depth（深度）** — interface 上的杠杆（leverage）：用一个小接口驱动大量行为。**Deep（深）**=高杠杆；**Shallow（浅）**=接口几乎和实现一样复杂。
- **Seam（缝/接缝）** — interface 所在的位置；一个你可以不在原地编辑就改变行为的地方。（用这个词，不要用 “boundary”。）
- **Adapter（适配器）** — 在某个 seam 上满足 interface 的具体实现。
- **Leverage（杠杆）** — 调用方从 depth 获得的收益。
- **Locality（局部性）** — 维护者从 depth 获得的收益：变更、bug、知识集中在一个位置。

关键原则（完整列表见 [LANGUAGE.zh-CN.md](LANGUAGE.zh-CN.md)）：

- **删除测试（deletion test）**：想象删掉这个 module。若复杂度消失，它只是透传；若复杂度在 N 个调用方里重新出现，它在“干活”。
- **interface 就是测试面（test surface）。**
- **一个 adapter = 假想 seam；两个 adapter = 真实 seam。**

该技能会被项目的领域模型（domain model）所 _约束_：领域语言为好的 seams 命名；ADR（Architecture Decision Record）记录技能不该重复争论的决策。

## 流程（Process）

### 1. 探索（Explore）

先读项目的领域术语表（domain glossary）以及你要触及区域的 ADR。

然后使用 Agent 工具（Agent tool）并设置 `subagent_type=Explore` 来遍历代码库。不要死板套启发式（heuristics）——要有机探索，并记录你感受到“摩擦”的位置：

- 理解一个概念需要在很多小 module 之间来回跳吗？
- 哪些 modules 很 **浅（shallow）**——interface 几乎与 implementation 一样复杂？
- 哪些纯函数被“为了可测”而抽取，但真实 bug 藏在调用方式里（缺乏 **locality**）？
- 哪些紧耦合 modules 的泄漏跨过了它们的 seams？
- 代码库哪些部分没有测试，或难以通过当前 interface 测试？

对任何你怀疑“浅”的东西应用 **删除测试（deletion test）**：删掉它会让复杂度集中，还是只是把复杂度挪地方？你要的信号是“会集中（yes, concentrates）”。

### 2. 展示候选项（Present candidates）

用编号列表列出加深机会（deepening opportunities）。对每个候选项包含：

- **Files** — 涉及哪些文件/modules
- **Problem** — 当前架构为何造成摩擦
- **Solution** — 用白话描述将会改变什么
- **Benefits** — 用 locality 与 leverage 解释收益，并说明测试会如何改善

**领域部分使用 `CONTEXT.md` 词汇，架构部分使用 [LANGUAGE.zh-CN.md](LANGUAGE.zh-CN.md) 词汇。** 如果 `CONTEXT.md` 定义了 “Order”，就说 “the Order intake module”，不要说 “FooBarHandler”，也不要说 “Order service”。

**ADR 冲突（ADR conflicts）**：如果某个候选项与已有 ADR 冲突，只有当摩擦足够真实、值得重新打开 ADR 时才提出。要清晰标注（例如 _“contradicts ADR-0007 — but worth reopening because…”_）。不要把 ADR 禁止的所有理论重构都列出来。

先不要提出 interface 方案。问用户：“你想深入探索哪一个？”

### 3. 拷问循环（Grilling loop）

当用户选定一个候选项后，进入拷问式对话（grilling conversation）。带着用户走完整个设计树（design tree）——约束、依赖、加深后 module 的形态、seam 后面放什么、哪些测试能存活。

随着决策逐渐成型（crystallize），副作用会内联发生：

- **要用 `CONTEXT.md` 里没有的概念给加深后的 module 命名？** 把该术语加到 `CONTEXT.md`——与 `/grill-with-docs` 同一纪律（见 [CONTEXT-FORMAT.zh-CN.md](../grill-with-docs/CONTEXT-FORMAT.zh-CN.md)）。若文件不存在则惰性创建。
- **在对话中把某个模糊术语打磨清楚？** 立刻更新 `CONTEXT.md`。
- **用户用“关键理由”拒绝了候选项？** 提议写 ADR，措辞：_“要不要我把这个记录成 ADR，这样未来架构评审就不会再建议同一件事？”_ 只有当未来探索者确实需要这个理由来避免重复建议时才提议——跳过临时原因（“现在不值得”）与不言自明的原因。见 [ADR-FORMAT.zh-CN.md](../grill-with-docs/ADR-FORMAT.zh-CN.md)。
- **想探索加深后 module 的不同 interface？** 见 [INTERFACE-DESIGN.zh-CN.md](INTERFACE-DESIGN.zh-CN.md)。
