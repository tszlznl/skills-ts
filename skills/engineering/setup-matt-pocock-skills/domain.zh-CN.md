# 领域文档（Domain Docs）

工程类技能（engineering skills）在探索代码库（codebase）时，应如何消费/读取本仓库的领域文档（domain documentation）。

## 在探索前先读这些（Before exploring, read these）

- 仓库根目录的 **`CONTEXT.md`**，或
- 如果存在仓库根目录的 **`CONTEXT-MAP.md`** —— 它会为每个上下文（context）指向一个 `CONTEXT.md`。读取与当前话题相关的每一个。
- **`docs/adr/`** —— 读取与你将要改动区域相关的 ADR（Architecture Decision Record）。在多上下文仓库中，也要检查 `src/<context>/docs/adr/`，其中包含上下文范围内的决策（context-scoped decisions）。

如果这些文件不存在，**静默继续（proceed silently）**。不要强调缺失，也不要提前建议创建。生产者技能（producer skill）`/grill-with-docs` 会在术语或决策确实被明确时惰性创建它们。

## 文件结构（File structure）

单上下文仓库（大多数仓库）：

```
/
├── CONTEXT.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

多上下文仓库（根目录存在 `CONTEXT-MAP.md`）：

```
/
├── CONTEXT-MAP.md
├── docs/adr/                          ← 系统级决策（system-wide decisions）
└── src/
    ├── ordering/
    │   ├── CONTEXT.md
    │   └── docs/adr/                  ← 上下文级决策（context-specific decisions）
    └── billing/
        ├── CONTEXT.md
        └── docs/adr/
```

## 使用术语表（glossary）的词汇（Use the glossary's vocabulary）

当你的输出命名某个领域概念（domain concept）（issue 标题、重构提议、假设（hypothesis）、测试名（test name）等），使用 `CONTEXT.md` 中对该术语的定义。不要漂移到术语表明确避免的同义词（synonyms）。

如果你需要的概念还不在术语表里，这是一个信号——要么你在发明项目并不使用的语言（需要重新考虑），要么术语表确实有缺口（记录给 `/grill-with-docs` 处理）。

## 标记 ADR 冲突（Flag ADR conflicts）

如果你的输出与已有 ADR 冲突，明确指出，而不是悄悄覆盖：

> _Contradicts ADR-0007 (event-sourced orders) — but worth reopening because…_
