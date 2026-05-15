# Language（语言）

本技能输出的每条建议都必须使用这一套共享词汇（shared vocabulary）。这些词必须严格按原样使用——不要用 “component”“service”“API”“boundary” 替代。保持一致语言是重点。

## Terms（术语）

**Module（模块）**
任何同时拥有 interface（接口）与 implementation（实现）的东西。刻意对规模不敏感（scale-agnostic）——同样适用于函数、类、包，或跨层切片（tier-spanning slice）。
_Avoid_: unit, component, service.

**Interface（接口）**
调用方正确使用 module 必须知道的一切。包含类型签名（type signature），但也包括不变量（invariants）、顺序约束（ordering constraints）、错误模式（error modes）、所需配置（required configuration）与性能特征（performance characteristics）。
_Avoid_: API, signature（太窄——这些只指类型层面的表面（type-level surface））。

**Implementation（实现）**
module 内部是什么——它的代码主体（body of code）。与 **Adapter** 区分：一个东西可以是“小 adapter + 大 implementation”（例如 Postgres repo），也可以是“大 adapter + 小 implementation”（例如 in-memory fake）。当主题是 seam 时优先用 “adapter”；否则用 “implementation”。

**Depth（深度）**
interface 上的杠杆（leverage）——调用方（或测试）在每单位 interface 学习成本下能驱动/覆盖的行为量（amount of behaviour）。当大量行为藏在一个小 interface 后面时，module 是 **deep（深）** 的；当 interface 几乎与 implementation 一样复杂时，module 是 **shallow（浅）** 的。

**Seam（缝/接缝）**（来自 Michael Feathers（Michael Feathers））
一个你可以不在原地编辑就改变行为的地方。是 module 的 interface 所在的 *位置（location）*。seam 放哪本身就是设计决策，独立于 seam 后面放什么。
_Avoid_: boundary（与 DDD（Domain-Driven Design）的 bounded context（bounded context）概念过载）。

**Adapter（适配器）**
在某个 seam 上满足 interface 的具体实现。它描述的是 *角色（role）*（填哪个槽），而不是 *内容（substance）*（里面有什么）。

**Leverage（杠杆）**
调用方从 depth 获得的东西：每单位 interface 学习成本换来的更多能力（capability）。一份 implementation 能在 N 个调用点（call sites）与 M 个测试里复用并回本。

**Locality（局部性）**
维护者从 depth 获得的东西：变更、bug、知识与验证集中在一个位置，而不是分散在调用方里。修一次，处处修好。

## Principles（原则）

- **Depth 是 interface 的属性，不是 implementation 的属性。** 一个 deep module 内部可以由很多小而可 mock、可替换的部件组合而成——它们只是没有成为 interface 的一部分。一个 module 可以有 **internal seams（内部 seams）**（实现私有，供自身测试使用），也可以在 interface 处有 **external seam（外部 seam）**。
- **Deletion test（删除测试）。** 想象删掉这个 module。若复杂度消失，module 没在隐藏任何东西（只是透传）。若复杂度在 N 个调用方里重新出现，module 在“干活”。
- **Interface is the test surface（interface 就是测试面）。** 调用方与测试跨越同一个 seam。如果你想测试 *越过* interface 的东西，module 可能形态不对。
- **One adapter means a hypothetical seam. Two adapters means a real one.** 不要引入 seam，除非跨 seam 的东西确实在变化。

## Relationships（关系）

- 一个 **Module** 恰好有一个 **Interface**（它呈现给调用方与测试的表面）。
- **Depth** 是 **Module** 的属性，以其 **Interface** 为参照进行衡量。
- **Seam** 是 **Module** 的 **Interface** 所在的位置。
- **Adapter** 位于某个 **Seam** 上并满足该 **Interface**。
- **Depth** 为调用方带来 **Leverage**，为维护者带来 **Locality**。

## Rejected framings（被拒绝的框架）

- **把 depth 视作 implementation 行数 / interface 行数的比值**（Ousterhout（Ousterhout））：这会奖励“给 implementation 灌水”。这里改用“depth-as-leverage”。
- **把 “Interface” 理解为 TypeScript `interface` 关键字或类的 public methods**：太窄——此处的 interface 包含调用方必须知道的每一条事实（fact）。
- **“Boundary”**：与 DDD 的 bounded context（bounded context）过载。请说 **seam** 或 **interface**。
