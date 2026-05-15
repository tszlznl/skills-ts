---
name: qa
description: 交互式 QA（Quality Assurance）会话：用户用对话方式报告 bug/问题，智能体负责创建 GitHub（GitHub）Issue。会在后台探索代码库（codebase）以获取上下文与领域语言（domain language）。用户想报告 bug、做 QA、用对话方式提 issues，或提到“QA session”时使用。
---

# QA Session（QA 会话）

进行一次交互式 QA 会话。用户描述他们遇到的问题。你负责澄清、在后台探索代码库获取上下文，并创建耐久（durable）、以用户为中心（user-focused）、并使用项目领域语言（domain language）的 GitHub（GitHub）Issue。

## 对用户提出的每个问题（issue）

### 1. 倾听并轻度澄清（Listen and lightly clarify）

让用户用自己的话描述问题。最多问 **2–3 个短澄清问题**，聚焦：

- 他们预期什么 vs 实际发生了什么
- 复现步骤（如果不明显）
- 是稳定复现还是偶发（intermittent）

不要过度访谈（over-interview）。如果描述已经足够创建 issue，就继续下一步。

### 2. 在后台探索代码库（Explore the codebase in the background）

与用户对话同时，在后台启动一个 Agent（subagent_type=Explore）来理解相关区域。目标不是找修复方案，而是：

- 学习该区域使用的领域语言（domain language）（检查 UBIQUITOUS_LANGUAGE.md）
- 理解该功能应该做什么
- 识别用户可见行为边界（user-facing behavior boundary）

这些上下文能帮你写出更好的 issue——但 issue 本身 **不要** 引用具体文件、行号或内部实现细节。

### 3. 判断范围：单一 issue 还是需要拆分？（Assess scope）

在创建之前，判断这是一个 **单一 issue**，还是需要 **拆成多个 issues**。

需要拆分的情况：

- 修复跨多个互不依赖的区域（例如“表单校验错了 AND 成功提示没了 AND 跳转也坏了”）
- 有明显可分离关注点（concerns），不同人可以并行处理
- 用户描述的现象有多个相互独立的失败模式/症状

保持为单一 issue 的情况：

- 一个地方的一个行为错了
- 所有症状都源于同一个根行为（root behavior）

### 4. 创建 GitHub issue（File the GitHub issue(s)）

用 `gh issue create` 创建 issues。不要先让用户预览——直接创建并分享 URLs。

issues 必须 **耐久（durable）**——即便大规模重构后仍然说得通。用用户视角撰写。

#### 单一 issue（For a single issue）

用这个模板：

```
## What happened

[Describe the actual behavior the user experienced, in plain language]

## What I expected

[Describe the expected behavior]

## Steps to reproduce

1. [Concrete, numbered steps a developer can follow]
2. [Use domain terms from the codebase, not internal module names]
3. [Include relevant inputs, flags, or configuration]

## Additional context

[Any extra observations from the user or from codebase exploration that help frame the issue — e.g. "this only happens when using the Docker layer, not the filesystem layer" — use domain language but don't cite files]
```

#### 拆分（多个 issues）（For a breakdown）

按依赖顺序创建 issues（先 blockers），这样你可以引用真实 issue 编号。

每个子 issue 用这个模板：

```
## Parent issue

#<parent-issue-number> (if you created a tracking issue) or "Reported during QA session"

## What's wrong

[Describe this specific behavior problem — just this slice, not the whole report]

## What I expected

[Expected behavior for this specific slice]

## Steps to reproduce

1. [Steps specific to THIS issue]

## Blocked by

- #<issue-number> (if this issue can't be fixed until another is resolved)

Or "None — can start immediately" if no blockers.

## Additional context

[Any extra observations relevant to this slice]
```

当你创建拆分（breakdown）时：

- **宁可多写薄 issues，也不要少写厚 issues** —— 每个都应可独立修复与验证
- **诚实标注阻塞关系（blocking relationships）** —— 如果 B 确实要等 A 修完才能测试，就写出来；如果独立，就都标 “None — can start immediately”
- **按依赖顺序创建 issues** —— 便于在 “Blocked by” 里引用真实编号
- **最大化并行性（parallelism）** —— 目标是多人（或多个智能体）能同时领取不同 issues

#### 所有 issue 正文的规则（Rules for all issue bodies）

- **不要写文件路径或行号** —— 它们会过期
- **使用项目的领域语言**（如存在，检查 UBIQUITOUS_LANGUAGE.md）
- **描述行为，不描述代码** —— 说“同步服务无法应用补丁（patch）”，不要说“applyPatch() 在第 42 行抛错”
- **复现步骤是必须的** —— 如果你无法确定，问用户
- **保持简洁** —— 开发者应能在 30 秒内读完

创建完后，打印所有 issue URLs（并总结阻塞关系），然后问：“下一个问题，还是我们结束？”

### 5. 继续会话（Continue the session）

持续进行，直到用户说他们结束。每个 issue 相互独立——不要打包处理。
