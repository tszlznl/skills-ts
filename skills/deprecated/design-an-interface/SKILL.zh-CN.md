---
name: design-an-interface
description: 使用并行子智能体（parallel sub-agents）为某个模块（module）生成多个差异巨大的接口（interface）设计。当用户想设计 API、探索不同接口选项、比较模块形态，或提到“design it twice”时使用。
---

# Design an Interface（设计接口）

基于《软件设计哲学（A Philosophy of Software Design）》里的 “Design It Twice”：你的第一个想法往往不是最好的。生成多个差异巨大的设计，然后对比。

## 工作流（Workflow）

### 1. 收集需求（Gather Requirements）

在开始设计前，先理解：

- [ ] 这个模块解决什么问题？
- [ ] 调用方是谁？（其他模块、外部用户、测试）
- [ ] 关键操作有哪些？
- [ ] 有哪些约束？（性能、兼容性、既有模式）
- [ ] 哪些东西应隐藏在内部，哪些应对外暴露？

追问：“这个模块需要做什么？谁会用它？”

### 2. 生成设计（并行子智能体）（Generate Designs）

使用 Task 工具（Task tool）同时启动 3 个以上子智能体。每个必须产出一个 **差异巨大（radically different）** 的方案。

```
Prompt template for each sub-agent:

Design an interface for: [module description]

Requirements: [gathered requirements]

Constraints for this design: [assign a different constraint to each agent]
- Agent 1: "Minimize method count - aim for 1-3 methods max"
- Agent 2: "Maximize flexibility - support many use cases"
- Agent 3: "Optimize for the most common case"
- Agent 4: "Take inspiration from [specific paradigm/library]"

Output format:
1. Interface signature (types/methods)
2. Usage example (how caller uses it)
3. What this design hides internally
4. Trade-offs of this approach
```

### 3. 展示设计（Present Designs）

逐个展示每个设计，包含：

1. **Interface signature** — types、methods、params
2. **Usage examples** — 调用方在真实使用中如何用它
3. **What it hides** — 内部保留的复杂度

按顺序展示，让用户在对比前先吸收每个方案。

### 4. 对比设计（Compare Designs）

展示完后，从以下维度对比：

- **Interface simplicity**：方法更少、参数更简单
- **General-purpose vs specialized**：灵活性 vs 聚焦
- **Implementation efficiency**：这种形态是否允许高效实现？还是迫使内部结构别扭？
- **Depth**：小 interface 隐藏大量复杂度（好）vs 大 interface + 薄实现（坏）
- **Ease of correct use** vs **ease of misuse**

用文字讨论权衡（trade-offs），不要用表格。突出方案分歧最大的地方。

### 5. 综合（Synthesize）

通常最佳设计会融合多个方案的洞见。追问：

- “哪个设计最符合你的主要用例（primary use case）？”
- “其他设计里有没有值得吸收的元素？”

## 评估标准（Evaluation Criteria）

出自《软件设计哲学（A Philosophy of Software Design）》：

**Interface simplicity**：方法更少、参数更简单 = 更易学习与正确使用。

**General-purpose**：无需变更即可覆盖未来用例。但要警惕过度泛化（over-generalization）。

**Implementation efficiency**：interface 形态是否允许高效实现？还是迫使内部结构别扭？

**Depth**：小 interface 隐藏大量复杂度 = 深模块（deep module）（好）。大 interface + 薄实现 = 浅模块（shallow module）（避免）。

## 反模式（Anti-Patterns）

- 不要让子智能体产出相似设计——要强制差异巨大
- 不要跳过对比——价值在对照（contrast）
- 不要开始实现——这里只讨论 interface 形态
- 不要按实现工作量来评估
