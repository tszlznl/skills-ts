# Deepening（加深）

在考虑依赖（dependencies）的前提下，如何安全地把一簇浅模块（shallow modules）加深（deepen）为深模块（deep ones）。假设你使用 [LANGUAGE.zh-CN.md](LANGUAGE.zh-CN.md) 中的词汇：**module**、**interface**、**seam**、**adapter**。

## 依赖分类（Dependency categories）

评估某个“加深候选项（candidate）”时，对它的依赖进行分类。类别决定了加深后 module 如何跨 seam 被测试。

### 1. 进程内（In-process）

纯计算（pure computation）、内存状态（in-memory state）、无 I/O。永远可以加深——合并 modules，并直接通过新的 interface 测试。不需要 adapter。

### 2. 本地可替换（Local-substitutable）

依赖存在本地测试替身（test stand-ins）（例如 Postgres（Postgres）用 PGLite（PGLite），文件系统用 in-memory filesystem）。若替身存在则可加深。测试套件里跑替身，并用它来测试加深后的 module。seam 是内部的；不要在 module 的对外 interface 上暴露 port。

### 3. 远程但自有（Ports & Adapters）

你自己的服务跨网络边界（network boundary）（微服务（microservices）、内部 API（internal APIs））。在 seam 上定义一个 **port（port）**（interface）。深模块拥有逻辑；传输层（transport）以 **adapter（adapter）** 方式注入。测试使用内存 adapter。生产使用 HTTP（HTTP）/gRPC（gRPC）/队列（queue）adapter。

推荐表述：*“在 seam 上定义一个 port，实现一个用于生产的 HTTP adapter 与一个用于测试的内存 adapter，这样即便部署跨网络，逻辑仍集中在一个深模块里。”*

### 4. 真外部依赖（True external）（Mock）

你无法控制的第三方服务（third-party services）（Stripe（Stripe）、Twilio（Twilio）等）。加深后的 module 把外部依赖作为注入的 port；测试提供 mock adapter。

## Seam 纪律（Seam discipline）

- **一个 adapter 意味着假想 seam；两个 adapter 意味着真实 seam。** 不要引入 port，除非至少有两个 adapter 合理（通常是生产 + 测试）。只有一个 adapter 的 seam 只是多了一层间接（indirection）。
- **内部 seams vs 外部 seams。** 深模块既可以有内部 seams（实现私有，供自身测试使用），也可以在 interface 处有外部 seam。不要因为测试使用了内部 seam，就把它们暴露到对外 interface 上。

## 测试策略：替换，而不是叠加（Testing strategy）

- 一旦深模块 interface 上有测试，旧的浅模块单测就变成浪费——删掉它们。
- 在深模块的 interface 上写新测试。**interface 就是测试面（test surface）。**
- 测试通过 interface 断言可观测结果（observable outcomes），不要断言内部状态。
- 测试应能经受内部重构（internal refactors）——描述行为（behaviour），不描述实现（implementation）。如果实现变了就必须改测试，那它测的是 interface 之外的东西。
