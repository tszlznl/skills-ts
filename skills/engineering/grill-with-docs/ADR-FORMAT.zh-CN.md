# ADR 格式（ADR Format）

ADR（Architecture Decision Record）存放在 `docs/adr/` 下，并使用递增编号：`0001-slug.md`、`0002-slug.md` 等。

惰性创建 `docs/adr/` 目录——只有在第一个 ADR 需要写时才创建。

## 模板（Template）

```md
# {决策的简短标题（Short title of the decision）}

{1-3 句话：上下文是什么、我们做了什么决定、以及为什么。}
```

就这样。一个 ADR 可以只有一段话。价值在于记录“做过这个决定”以及“为什么”——而不是把章节填满。

## 可选章节（Optional sections）

只有在它们能带来真实价值时才加入。多数 ADR 不需要它们。

- **Status** frontmatter（`proposed | accepted | deprecated | superseded by ADR-NNNN`）——当决策被重新审视时有用
- **Considered Options**——只有当被拒绝的备选方案值得被记住时才写
- **Consequences**——只有当需要点出不明显的下游影响时才写

## 编号（Numbering）

扫描 `docs/adr/`，找到当前最大的编号，然后加一。

## 什么时候要提议写 ADR（When to offer an ADR）

必须同时满足以下三点：

1. **难以回退（Hard to reverse）** —— 以后改主意的成本显著
2. **没有上下文会很奇怪（Surprising without context）** —— 未来读者看到代码会问“为什么要这么做？”
3. **真实权衡的结果（The result of a real trade-off）** —— 有真实可选方案，你基于具体理由选了其中之一

如果一个决定很容易回退，就别写——反正你很快会改回来。如果它不让人意外，就不会有人好奇原因。如果压根没有替代方案，那除“我们做了显然的事”之外也没什么可记录的。

### 什么算（What qualifies）

- **架构形态（Architectural shape）**：“我们使用 monorepo（monorepo）。” “写模型（write model）使用事件溯源（event-sourced），读模型（read model）投影（projected）到 Postgres（Postgres）。”
- **上下文之间的集成模式（Integration patterns between contexts）**：“Ordering 和 Billing 通过领域事件（domain events）通信，而不是同步 HTTP（HTTP）。”
- **带锁定效应的技术选择（Technology choices that carry lock-in）**：数据库、消息总线（message bus）、认证提供方（auth provider）、部署目标（deployment target）。不是每个库（library）都要写——只记录那些要花一个季度才能换掉的。
- **边界与范围决策（Boundary and scope decisions）**：“Customer 数据归 Customer 上下文所有；其他上下文只能通过 ID 引用。” 明确写下“不做什么”和写下“做什么”同样有价值。
- **刻意偏离显然路径（Deliberate deviations from the obvious path）**：“我们使用手写 SQL（SQL）而不是 ORM（Object-Relational Mapping），因为 X。” 任何一个理性读者会默认相反做法的点都值得记录。它能阻止下一位工程师去“修复”一个其实是刻意选择的东西。
- **代码里看不出来的约束（Constraints not visible in the code）**：“由于合规（compliance）要求，我们不能用 AWS（AWS）。” “响应时间必须低于 200ms，因为合作方 API（API）合同要求。”
- **被拒绝但拒绝理由不明显的备选方案（Rejected alternatives when the rejection is non-obvious）**：如果你考虑过 GraphQL（GraphQL）并因细微原因选了 REST（REST），就写下来——否则半年后有人还会再提 GraphQL。
