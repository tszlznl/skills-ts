# Interface Design（接口设计）

当用户想为某个已选的“加深候选项（deepening candidate）”探索不同的 interface（接口）时，使用这个“并行子智能体（parallel sub-agent）”模式。灵感来自 Ousterhout（Ousterhout）的 “Design It Twice”——你的第一个想法往往不是最好的。

使用 [LANGUAGE.zh-CN.md](LANGUAGE.zh-CN.md) 里的词汇：**module**、**interface**、**seam**、**adapter**、**leverage**。

## 流程（Process）

### 1. 框定问题空间（Frame the problem space）

在启动子智能体之前，先写一段面向用户的“问题空间”解释，针对已选候选项说明：

- 任何新 interface 必须满足哪些约束（constraints）
- 它依赖哪些 dependencies，以及这些 dependencies 属于哪个类别（见 [DEEPENING.zh-CN.md](DEEPENING.zh-CN.md)）
- 一段粗略示意代码（illustrative code sketch）用来落地这些约束——不是方案提议，只是让约束更具体

把这段解释展示给用户，然后立刻进入第 2 步。用户阅读与思考时，子智能体并行工作。

### 2. 启动子智能体（Spawn sub-agents）

使用 Agent 工具（Agent tool）并行启动 3 个以上子智能体。每个子智能体必须产出一个 **差异巨大（radically different）** 的深模块 interface 设计。

给每个子智能体一个独立的技术简报（technical brief）（文件路径、耦合细节、来自 [DEEPENING.zh-CN.md](DEEPENING.zh-CN.md) 的依赖类别、seam 后面放什么）。该简报与第 1 步面向用户的“问题空间”解释独立。给每个智能体一个不同的设计约束：

- Agent 1：“最小化 interface——入口点最多 1–3 个。最大化每个入口点的 leverage。”
- Agent 2：“最大化灵活性——支持更多用例与扩展。”
- Agent 3：“为最常见调用方优化——让默认用法极其简单。”
- Agent 4（如适用）：“围绕 ports & adapters 设计，以处理跨 seam 的依赖。”

在简报里同时包含 [LANGUAGE.zh-CN.md](LANGUAGE.zh-CN.md) 词汇与 `CONTEXT.md` 词汇，让每个子智能体在架构语言与领域语言上都能一致命名。

每个子智能体输出：

1. Interface（types、methods、params——并包含 invariants、ordering、error modes）
2. 用法示例（usage example）：调用方如何使用
3. seam 后面被 implementation 隐藏了什么
4. 依赖策略与 adapters（见 [DEEPENING.zh-CN.md](DEEPENING.zh-CN.md)）
5. 权衡（trade-offs）：哪里 leverage 高，哪里薄

### 3. 展示与对比（Present and compare）

按顺序展示各设计，让用户逐个吸收，然后用文字对比。对比维度：**depth**（interface 杠杆）、**locality**（变更集中在哪里）、**seam placement**（seam 放在哪）。

对比后给出你自己的推荐：你认为哪个最强、为什么。如果不同设计的元素适合组合，提出一个混合方案（hybrid）。要有主张（be opinionated）——用户想要的是强判断，而不是菜单。
