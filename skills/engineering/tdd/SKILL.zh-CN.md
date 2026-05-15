---
name: tdd
description: 采用红-绿-重构（red-green-refactor）循环的测试驱动开发（test-driven development）。当用户想用 TDD 构建功能或修 bug、提到“red-green-refactor”、想做集成测试（integration tests），或要求 test-first 开发时使用。
---

# 测试驱动开发（Test-Driven Development）

## 理念（Philosophy）

**核心原则（Core principle）**：测试应该通过公共接口（public interfaces）验证行为（behavior），而不是验证实现细节（implementation details）。代码内部可以彻底重写；测试不应该因此崩掉。

**好测试（Good tests）** 倾向集成风格（integration-style）：通过公共 API（public APIs）跑真实代码路径（real code paths）。它描述系统做 _什么（what）_，而不是 _怎么做（how）_。一个好测试读起来像规格（specification）——“用户可以用有效购物车结账（checkout）”能清楚说明存在什么能力。因为不关心内部结构，这类测试能在重构（refactor）后继续存活。

**坏测试（Bad tests）** 与实现耦合（coupled to implementation）。它们会 mock 内部协作者（internal collaborators）、测试私有方法（private methods），或通过外部手段验证（例如绕过接口直接查数据库）。警示信号是：你只是重构了实现，行为没变，但测试却坏了。如果你重命名一个内部函数就让测试失败，那它测的是实现，而不是行为。

示例见 [tests.zh-CN.md](tests.zh-CN.md)，mock 指南见 [mocking.zh-CN.md](mocking.zh-CN.md)。

## 反模式：水平切片（Horizontal Slices）

**不要先把所有测试写完，再写所有实现。** 这就是“水平切片（horizontal slicing）”：把 RED 当成“写完所有测试”，把 GREEN 当成“写完所有代码”。

这会产生 **垃圾测试（crap tests）**：

- 批量写出来的测试测的是 _想象的行为（imagined behavior）_，不是 _真实行为（actual behavior）_
- 你最终会去测“形状（shape）”（数据结构、函数签名），而不是用户可见行为
- 测试对真实变化不敏感——行为坏了它还过；行为没变它却挂
- 你会“跑在车灯之外（outrun your headlights）”：在理解实现前就先承诺了测试结构

**正确做法**：用探照弹（tracer bullets）做垂直切片（vertical slices）。一个测试 → 一个实现 → 重复。每个测试都根据上一轮循环学到的东西来调整。因为代码刚写完，你很清楚什么行为重要、该如何验证。

```
WRONG (horizontal):
  RED:   test1, test2, test3, test4, test5
  GREEN: impl1, impl2, impl3, impl4, impl5

RIGHT (vertical):
  RED→GREEN: test1→impl1
  RED→GREEN: test2→impl2
  RED→GREEN: test3→impl3
  ...
```

## 工作流（Workflow）

### 1. 规划（Planning）

探索代码库（codebase）时，使用项目的领域术语表（domain glossary），让测试名与接口词汇匹配项目语言，并尊重你要触及区域的 ADR（Architecture Decision Record）。

在写任何代码之前：

- [ ] 与用户确认需要哪些接口变更（interface changes）
- [ ] 与用户确认要测哪些行为（behaviors）（做优先级）
- [ ] 识别可抽取 [深模块（deep modules）](deep-modules.zh-CN.md) 的机会（小接口（small interface）、深实现（deep implementation））
- [ ] 为 [可测试性（testability）](interface-design.zh-CN.md) 设计接口
- [ ] 列出要测的行为（不是实现步骤）
- [ ] 让用户批准计划

追问：“公共接口应该长什么样？哪些行为最重要、最值得测？”

**你不可能把一切都测到。** 与用户确认到底哪些行为最关键。把测试精力集中在关键路径（critical paths）与复杂逻辑上，而不是每一个边界情况（edge case）。

### 2. 探照弹（Tracer Bullet）

只写 **一个** 测试，用来确认系统的 **一件** 事：

```
RED:   Write test for first behavior → test fails
GREEN: Write minimal code to pass → test passes
```

这就是你的探照弹（tracer bullet）——证明端到端（end-to-end）的路径能跑通。

### 3. 增量循环（Incremental Loop）

对每个剩余行为：

```
RED:   Write next test → fails
GREEN: Minimal code to pass → passes
```

规则：

- 一次只写一个测试
- 只写让当前测试通过所需的最少代码
- 不要为未来测试提前铺路
- 让测试聚焦可观测行为（observable behavior）

### 4. 重构（Refactor）

当所有测试都通过后，寻找 [重构候选（refactor candidates）](refactoring.zh-CN.md)：

- [ ] 抽取重复（duplication）
- [ ] 加深模块（把复杂度移到简单接口之后）
- [ ] 在自然的地方应用 SOLID（SOLID）原则
- [ ] 思考新代码暴露出的旧代码问题
- [ ] 每一步重构后都运行测试

**RED 的时候永远不要重构。** 先回到 GREEN。

## 每轮循环检查清单（Checklist Per Cycle）

```
[ ] Test describes behavior, not implementation
[ ] Test uses public interface only
[ ] Test would survive internal refactor
[ ] Code is minimal for this test
[ ] No speculative features added
```
