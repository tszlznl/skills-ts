# `CONTEXT.md` 格式（CONTEXT.md Format）

## 结构（Structure）

```md
# {上下文名称（Context Name）}

{1–2 句话：这个上下文是什么、以及为什么存在。}

## Language

**Order**:
{对该术语的简洁描述}
_Avoid_: Purchase, transaction

**Invoice**:
A request for payment sent to a customer after delivery.
_Avoid_: Bill, payment request

**Customer**:
A person or organization that places orders.
_Avoid_: Client, buyer, account

## Relationships

- An **Order** produces one or more **Invoices**
- An **Invoice** belongs to exactly one **Customer**

## Example dialogue

> **Dev:** "When a **Customer** places an **Order**, do we create the **Invoice** immediately?"
> **Domain expert:** "No — an **Invoice** is only generated once a **Fulfillment** is confirmed."

## Flagged ambiguities

- "account" was used to mean both **Customer** and **User** — resolved: these are distinct concepts.
```

## 规则（Rules）

- **要有主张（Be opinionated）。** 当一个概念有多个词可选时，选出最好的那个，并把其他词列在 “_Avoid_” 里，避免混用。
- **明确标记冲突（Flag conflicts explicitly）。** 如果某个术语被含糊使用，在 “Flagged ambiguities” 里直接点名，并给出清晰结论。
- **定义要短（Keep definitions tight）。** 最多一句话。定义“它是什么”，而不是“它做什么”。
- **展示关系（Show relationships）。** 用加粗术语名，并在明显时表达基数（cardinality）。
- **只包含该项目上下文特有的术语（Only include terms specific to this project's context）。** 通用编程概念（超时（timeouts）、错误类型（error types）、工具模式（utility patterns））即便项目大量使用也不该放进来。加新术语前先问：这是该上下文独有概念，还是通用编程概念？只收前者。
- **当自然聚类出现时按小标题分组（Group terms under subheadings）。** 如果所有术语都属于同一个紧密主题，平铺列表也可以。
- **写一段示例对话（Write an example dialogue）。** 让开发者（dev）与领域专家（domain expert）对话，展示术语如何自然交互，并澄清相关概念的边界。

## 单上下文 vs 多上下文仓库（Single vs multi-context repos）

**单上下文（大多数仓库）：** 仓库根目录只有一个 `CONTEXT.md`。

**多上下文：** 仓库根目录有一个 `CONTEXT-MAP.md`，列出各上下文、它们所在位置、以及彼此关系：

```md
# Context Map

## Contexts

- [Ordering](./src/ordering/CONTEXT.md) — receives and tracks customer orders
- [Billing](./src/billing/CONTEXT.md) — generates invoices and processes payments
- [Fulfillment](./src/fulfillment/CONTEXT.md) — manages warehouse picking and shipping

## Relationships

- **Ordering → Fulfillment**: Ordering emits `OrderPlaced` events; Fulfillment consumes them to start picking
- **Fulfillment → Billing**: Fulfillment emits `ShipmentDispatched` events; Billing consumes them to generate invoices
- **Ordering ↔ Billing**: Shared types for `CustomerId` and `Money`
```

该技能会推断应采用哪种结构：

- 如果存在 `CONTEXT-MAP.md`，读取它来定位各上下文（contexts）
- 如果只有根目录 `CONTEXT.md`，则为单上下文（single context）
- 如果两者都不存在，则在第一个术语（term）被明确时惰性创建根目录 `CONTEXT.md`

当存在多个上下文时，推断当前话题属于哪一个上下文。如果不清楚，就提问。
