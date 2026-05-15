---
name: to-prd
description: 将当前对话上下文（conversation context）整理成 PRD（Product Requirements Document），并发布到项目的问题跟踪器（issue tracker）。当用户想基于当前上下文创建 PRD 时使用。
---

这个技能会结合当前对话上下文与对代码库（codebase）的理解，产出一份 PRD。不要访谈用户（interview）——只综合你已经知道的信息。

问题跟踪器（issue tracker）与分诊标签词汇（triage label vocabulary）应当已经提供给你；如果没有，运行 `/setup-matt-pocock-skills`。

## 流程（Process）

1. 如果你还没做过，先探索仓库以理解代码库当前状态。PRD 全程使用项目的领域术语表（domain glossary）词汇，并尊重你要触及区域的 ADR（Architecture Decision Record）。

2. 勾勒完成实现所需构建或修改的主要模块（modules）。积极寻找机会抽取可被隔离测试（tested in isolation）的深模块（deep modules）。

深模块（deep module）（相对浅模块（shallow module））指：它用一个简单、可测试、且很少变化的接口（interface）封装了大量功能。

与用户确认这些模块是否符合预期。并确认用户希望哪些模块写测试（tests）。

3. 使用下方模板编写 PRD，然后发布到项目的问题跟踪器。直接应用 `ready-for-agent` 分诊标签（triage label）——无需额外分诊（triage）。

<prd-template>

## 问题陈述（Problem Statement）

从用户视角描述用户正在面对的问题。

## 解决方案（Solution）

从用户视角描述问题的解决方案。

## 用户故事（User Stories）

写一份很长的编号用户故事列表。每条用户故事采用以下格式：

1. 作为一个 <actor>，我想要 <feature>，从而 <benefit>

<user-story-example>
1. 作为一名手机银行用户，我想在账户列表里看到余额，以便更好地决定自己的支出
</user-story-example>

这份用户故事列表必须非常全面，覆盖该功能的所有方面。

## 实现决策（Implementation Decisions）

列出已做出的实现决策（implementation decisions）。可包括：

- 将要构建/修改的模块（modules）
- 将要修改的模块接口（interfaces）
- 开发者提供的技术澄清（technical clarifications）
- 架构决策（architectural decisions）
- Schema 变更（schema changes）
- API 契约（API contracts）
- 具体交互（specific interactions）

不要包含具体文件路径或代码片段。它们会很快过期。

例外：如果原型（prototype）产出的某段片段能比文字更精确地表达一个决策（状态机（state machine）、reducer（reducer）、schema（schema）、类型形状（type shape）），可以把它内联到对应决策处，并简要注明来源于原型。只保留“承载决策”的部分——不是可运行 demo，只要关键片段。

## 测试决策（Testing Decisions）

列出已做出的测试决策（testing decisions），包括：

- 什么才算好测试（good test）（只测外部行为（external behavior），不要测实现细节（implementation details））
- 哪些模块要被测试
- 测试的“先例（prior art）”（即代码库里类似测试的写法）

## 超出范围（Out of Scope）

描述本 PRD 不包含哪些内容。

## 其他说明（Further Notes）

关于该功能的其他补充说明。

</prd-template>
