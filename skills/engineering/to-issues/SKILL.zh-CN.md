---
name: to-issues
description: 使用“探照弹式（tracer-bullet）垂直切片（vertical slices）”把计划/规格（spec）/PRD 拆成可独立领取（independently-grabbable）的项目问题（issues），并发布到问题跟踪器（issue tracker）。当用户想把计划转成 issues、创建实现工单（tickets），或把工作拆解成 issues 时使用。
---

# To Issues（拆成 Issues）

使用垂直切片（vertical slices，tracer bullets）把计划拆成可独立领取（independently-grabbable）的 issues。

问题跟踪器（issue tracker）与分诊标签词汇（triage label vocabulary）应当已经提供给你；如果没有，运行 `/setup-matt-pocock-skills`。

## 流程（Process）

### 1. 收集上下文（Gather context）

基于当前对话上下文里已有的信息工作。如果用户通过参数传入了某个 issue 引用（编号、URL 或路径），就从问题跟踪器抓取并读取该 issue 的完整正文与评论。

### 2. 探索代码库（可选）（Explore the codebase）

如果你还没探索过代码库（codebase），先探索以理解当前代码状态。issue 标题与描述应使用项目的领域术语表（domain glossary）词汇，并尊重你要触及区域的 ADR（Architecture Decision Record）。

### 3. 起草垂直切片（Draft vertical slices）

把计划拆成 **探照弹（tracer bullet）** issues。每个 issue 都是一条很薄的垂直切片：从端到端（end-to-end）贯穿所有集成层（integration layers），而不是只切某一层的“水平切片”。

切片可以是 HITL（Human-In-The-Loop）或 AFK（Away From Keyboard）。HITL 切片需要人类交互，例如架构决策或设计评审。AFK 切片可以在无人交互的情况下实现并合并。能用 AFK 的尽量用 AFK，少用 HITL。

<vertical-slice-rules>
- 每个切片都交付一条窄但完整的“贯穿每一层”的路径（schema、API、UI、tests）
- 一个切片完成后应能独立演示或验证
- 宁可切成更多“薄切片”，也不要少量“厚切片”
</vertical-slice-rules>

### 4. 追问用户（Quiz the user）

把拆解结果用编号列表展示。对每个切片展示：

- **Title**：简短描述性名称
- **Type**：HITL / AFK
- **Blocked by**：依赖哪些其他切片（如有）
- **User stories covered**：覆盖哪些用户故事（如果源材料里有）

询问用户：

- 粒度是否合适？（太粗 / 太细）
- 依赖关系是否正确？
- 是否需要合并或进一步拆分？
- HITL 与 AFK 的标注是否合理？

迭代，直到用户批准该拆解。

### 5. 发布 issues 到问题跟踪器（Publish the issues）

对每个已批准的切片，在问题跟踪器里创建一个新 issue。使用下面的 issue 正文模板。这些 issues 视为已准备好交给 AFK 智能体，因此除非另有指示，应使用正确的分诊标签（triage label）发布。

按依赖顺序发布（先 blockers），这样你可以在 “Blocked by” 字段里引用真实的 issue 标识符（identifiers）。

<issue-template>
## Parent

父 issue 的引用（如果来源就是一个已有 issue；否则省略本节）。

## What to build

对该垂直切片的简洁描述。描述端到端行为（end-to-end behavior），不要按层罗列实现步骤。

避免具体文件路径或代码片段——它们会很快过期。例外：如果原型（prototype）产出的某段片段能比文字更精确地表达一个决策（状态机（state machine）、reducer（reducer）、schema（schema）、类型形状（type shape）），可以把它内联在这里，并简要注明来源于原型。只保留“承载决策”的部分——不是可运行 demo，只要关键片段。

## Acceptance criteria

- [ ] Criterion 1
- [ ] Criterion 2
- [ ] Criterion 3

## Blocked by

- 对阻塞工单（blocking ticket）的引用（如有）

如果没有阻塞项，则写 “None - can start immediately”。

</issue-template>

不要关闭或修改任何父 issue。
