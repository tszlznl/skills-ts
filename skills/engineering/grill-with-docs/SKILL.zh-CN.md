---
name: grill-with-docs
description: 以现有领域模型（domain model）为基准拷问你的计划，打磨术语，并在决策逐渐成型（crystallise）时内联更新文档（CONTEXT.md、ADR）。当用户想把计划与项目语言/已记录决策进行压力测试（stress-test）时使用。
---

<what-to-do>

围绕这个计划的每个方面对我进行高强度追问，直到我们达成共享理解（shared understanding）。沿着设计决策树（design tree）的每个分支往下走，逐一解决决策之间的依赖关系。对每个问题，都给出你推荐的答案。

一次只问一个问题，并在继续之前等待我对该问题的反馈。

如果某个问题可以通过探索代码库（codebase）回答，就先去探索代码库再回答。

</what-to-do>

<supporting-info>

## 领域意识（Domain awareness）

在探索代码库（codebase）时，同时查找是否已有文档：

### 文件结构（File structure）

大多数仓库只有一个上下文（context）：

```
/
├── CONTEXT.md
├── docs/
│   └── adr/
│       ├── 0001-event-sourced-orders.md
│       └── 0002-postgres-for-write-model.md
└── src/
```

如果根目录存在 `CONTEXT-MAP.md`，则说明该仓库有多个上下文（contexts）。映射文件会指向每个上下文所在位置：

```
/
├── CONTEXT-MAP.md
├── docs/
│   └── adr/                          ← 系统级决策（system-wide decisions）
├── src/
│   ├── ordering/
│   │   ├── CONTEXT.md
│   │   └── docs/adr/                 ← 上下文级决策（context-specific decisions）
│   └── billing/
│       ├── CONTEXT.md
│       └── docs/adr/
```

惰性创建文件——只有在你确实有内容要写时才创建。如果不存在 `CONTEXT.md`，在第一个术语（term）被明确时再创建。如果不存在 `docs/adr/`，在第一个 ADR（Architecture Decision Record）需要写时再创建。

## 会话中（During the session）

### 用术语表（glossary）对齐与挑战

当用户使用的术语与 `CONTEXT.md` 里的既有语言冲突时，立刻指出来：“你的术语表把 ‘cancellation’ 定义为 X，但你现在似乎在说 Y——到底是哪一个？”

### 打磨模糊语言（fuzzy language）

当用户使用含糊或过载（overloaded）的词时，提出一个精确的规范术语（canonical term）：“你说的 ‘account’——是指 Customer 还是 User？这是两回事。”

### 讨论具体场景（concrete scenarios）

当讨论领域关系（domain relationships）时，用具体场景进行压力测试。发明一些能探测边界情况（edge cases）的场景，迫使用户明确概念之间的边界（boundaries）。

### 与代码交叉验证（Cross-reference with code）

当用户描述某件事如何工作时，检查代码是否一致。如果发现矛盾，直接抛出：“你的代码会取消整个 Order，但你刚说可以部分取消——到底哪个是对的？”

### 内联更新 `CONTEXT.md`

当某个术语被明确时，立刻更新 `CONTEXT.md`。不要攒一堆再改——要边发生边记录。格式参考 [CONTEXT-FORMAT.zh-CN.md](./CONTEXT-FORMAT.zh-CN.md)。

`CONTEXT.md` 必须完全不包含实现细节（implementation details）。不要把 `CONTEXT.md` 当成规格（spec）、草稿（scratch pad）或实现决策仓库。它是术语表（glossary），仅此而已。

### 谨慎提供 ADR（Offer ADRs sparingly）

只有同时满足以下三点时，才提出创建 ADR：

1. **难以回退（Hard to reverse）** —— 以后改主意的成本显著
2. **没有上下文会很奇怪（Surprising without context）** —— 未来读者会问“为什么要这么做？”
3. **真实权衡的结果（The result of a real trade-off）** —— 有真实可选方案，你基于具体理由选了其中之一

只要三者缺一，就跳过 ADR。格式参考 [ADR-FORMAT.zh-CN.md](./ADR-FORMAT.zh-CN.md)。

</supporting-info>
