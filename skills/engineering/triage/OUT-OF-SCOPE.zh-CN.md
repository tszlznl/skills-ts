# 超出范围（Out-of-Scope）知识库

仓库里的 `.out-of-scope/` 目录用于保存“被拒绝的功能请求（feature requests）”的长期记录。它有两个目的：

1. **组织记忆（institutional memory）** —— 记录为什么拒绝某个功能，避免在关闭 issue 后理由丢失
2. **去重（deduplication）** —— 当新的 issue 与历史拒绝相似时，技能（skill）可以直接呈现以前的决策，而不是重新争论一遍

## 目录结构（Directory structure）

```
.out-of-scope/
├── dark-mode.md
├── plugin-system.md
└── graphql-api.md
```

每个 **概念（concept）** 一个文件，而不是每个 issue 一个文件。多个请求同一件事的 issues 应该归到同一个文件里。

## 文件格式（File format）

文件应采用放松、可读的风格——更像一篇短设计文档（design document），而不是数据库条目。用段落、代码样例与例子把理由写清楚，方便第一次遇到它的人理解。

```markdown
# Dark Mode

This project does not support dark mode or user-facing theming.

## Why this is out of scope

The rendering pipeline assumes a single color palette defined in
`ThemeConfig`. Supporting multiple themes would require:

- A theme context provider wrapping the entire component tree
- Per-component theme-aware style resolution
- A persistence layer for user theme preferences

This is a significant architectural change that doesn't align with the
project's focus on content authoring. Theming is a concern for downstream
consumers who embed or redistribute the output.

```ts
// The current ThemeConfig interface is not designed for runtime switching:
interface ThemeConfig {
  colors: ColorPalette; // single palette, resolved at build time
  fonts: FontStack;
}
```

## Prior requests

- #42 — "Add dark mode support"
- #87 — "Night theme for accessibility"
- #134 — "Dark theme option"
```

### 文件命名（Naming the file）

为概念使用简短、描述性强的 kebab-case 文件名：`dark-mode.md`、`plugin-system.md`、`graphql-api.md`。名称需要足够可识别，让浏览目录的人不用打开文件也能大概知道拒绝了什么。

### 写“理由”（Writing the reason）

理由必须有内容——不是“我们不想做”，而是“为什么”。好理由通常会引用：

- 项目范围或理念（project scope / philosophy）：例如“项目聚焦 X；主题（theming）是下游关注点”
- 技术约束（technical constraints）：例如“支持它需要 Y，但这与我们的 Z 架构冲突”
- 战略决策（strategic decisions）：例如“我们选择 A 而不是 B，因为……”

理由要耐久（durable）。避免引用临时状态（“我们最近太忙”）——那不是拒绝（rejection），那是延期（deferral）。

## 什么时候检查 `.out-of-scope/`（When to check `.out-of-scope/`）

在分诊（triage）的第 1 步“收集上下文（Gather context）”中，读取 `.out-of-scope/` 下的所有文件。评估一个新 issue 时：

- 检查请求是否匹配某个已存在的 out-of-scope 概念
- 匹配依据是概念相似（concept similarity），而不是关键词：例如 “night theme” 也应匹配 `dark-mode.md`
- 如果匹配，把它呈现给维护者：“这与 `.out-of-scope/dark-mode.md` 类似——我们之前因为 [reason] 拒绝了它。你现在仍然这么认为吗？”

维护者可能会：

- **确认（Confirm）** —— 把新 issue 加到已有文件的 “Prior requests” 列表里，然后关闭
- **重新考虑（Reconsider）** —— 删除或更新 out-of-scope 文件，并让 issue 进入正常分诊流程
- **不同意（Disagree）** —— 两个 issue 相关但不相同，继续正常分诊

## 什么时候写入 `.out-of-scope/`（When to write to `.out-of-scope/`）

只有当一个 **enhancement**（不是 bug）被以 `wontfix` 拒绝时才写入。流程：

1. 维护者决定该功能请求超出范围（out of scope）
2. 检查是否已有匹配的 `.out-of-scope/` 文件
3. 如果有：把新 issue 追加到 “Prior requests” 列表里
4. 如果没有：创建新文件，写上概念名、决策、理由、以及第一个 prior request
5. 在 issue 下发表评论解释决策，并提及 `.out-of-scope/` 文件
6. 给 issue 打上 `wontfix` 标签并关闭

## 更新或移除 out-of-scope 文件（Updating or removing out-of-scope files）

如果维护者改变了对某个曾被拒绝概念的看法：

- 删除对应的 `.out-of-scope/` 文件
- 该技能不需要重开旧 issues —— 它们只是历史记录
- 触发重新考虑的新 issue 继续正常分诊流程
