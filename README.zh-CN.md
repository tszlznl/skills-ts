<p>
  <a href="https://www.aihero.dev/s/skills-newsletter">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skills-repo-dark_2x.png">
      <source media="(prefers-color-scheme: light)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png">
      <img alt="Skills（技能）" src="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png" width="369">
    </picture>
  </a>
</p>

# 面向真实工程师的技能（Skills）

[![skills.sh](https://skills.sh/b/mattpocock/skills)](https://skills.sh/mattpocock/skills)

我每天都会用到的一套智能体技能（agent skills）：用来做真实的工程开发，而不是“氛围编程（vibe coding）”。

开发真正的应用很难。像 GSD（Getting Stuff Done）、BMAD、Spec-Kit 这类方法试图通过“接管流程”来帮你。但它们在提供帮助的同时，也会夺走你的控制权，让流程里的 bug 更难定位和修复。

这些技能（skills）被设计为：小巧、易改、可组合。它们适用于任何模型（model）。它们基于数十年的工程经验积累。随便折腾它们，把它们改成你自己的风格。玩得开心。

如果你想跟进这些技能的变更，以及我新增的技能，可以加入我的通讯订阅（newsletter），和大约 60,000 名开发者一起：

[订阅通讯（Newsletter）](https://www.aihero.dev/s/skills-newsletter)

## 快速开始（30 秒安装）

1. 运行 skills.sh 安装器：

```bash
npx skills@latest add mattpocock/skills
```

2. 选择你想要的技能，以及要把它们安装到哪些编码智能体（coding agents）上。**请务必选择 `/setup-matt-pocock-skills`**。

3. 在你的智能体里运行 `/setup-matt-pocock-skills`。它会：
   - 询问你想使用哪种问题跟踪器（issue tracker）：GitHub（GitHub）、Linear（Linear）或本地文件（local files）
   - 询问你在分诊（triage）问题时会用到哪些标签（labels）（`/triage` 会用到这些标签）
   - 询问你希望把我们生成的文档保存到哪里

4. 搞定——你就可以开始了。

## 为什么要做这套技能

我做这些技能，是为了修复我在 Claude Code（Claude Code）、Codex（Codex）以及其他编码智能体里看到的一些常见失败模式（failure modes）。

### #1：智能体没做出我想要的东西

> “没有人能完全知道自己到底想要什么”
>
> David Thomas & Andrew Hunt，《程序员修炼之道（The Pragmatic Programmer）》

**问题**：软件开发里最常见的失败模式是“目标不对齐（misalignment）”。你以为开发者理解了你的需求；但当你看到成品时才发现，它根本没理解你。

在 AI 时代也是一样：你和智能体之间存在沟通鸿沟（communication gap）。解决办法是做一次 **“拷问式沟通（grilling session）”**：让智能体围绕你要构建的东西，向你提出更细、更具体的问题。

**解决办法**：使用：

- [`/grill-me`](./skills/productivity/grill-me/SKILL.zh-CN.md) - 面向非代码场景
- [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.zh-CN.md) - 与 [`/grill-me`](./skills/productivity/grill-me/SKILL.zh-CN.md) 类似，但提供更多增强（见下文）

这是我最受欢迎的两项技能。它们能在你开始动手前，帮你与智能体对齐，并把你要做的改动想得更深。你每次要做改动时都可以用它们。

### #2：智能体太啰嗦

> 有了统一语言（ubiquitous language），开发者之间的对话，以及代码的表达，都源自同一个领域模型（domain model）。
>
> Eric Evans，《领域驱动设计（Domain-Driven Design）》

**问题**：项目启动阶段，开发者与业务相关方（领域专家，domain experts）往往在使用两套不同的语言。

我和智能体之间也有同样的张力：智能体通常被直接丢进一个项目里，让它边做边理解项目术语（jargon）。因此它常常会用 20 个词表达 1 个词就能说明的问题。

**解决办法**：建立一份共享语言（shared language）文档，帮助智能体解码项目里的术语。

<details>
<summary>
示例
</summary>

这是我在 `course-video-manager` 仓库里的一个示例 [`CONTEXT.md`](https://github.com/mattpocock/course-video-manager/blob/076a5a7a182db0fe1e62971dd7a68bcadf010f1c/CONTEXT.md)。哪一种更容易读懂？

- **之前**：“当课程（course）的某个章节（section）里的一节课（lesson）被‘实化（real）’（也就是在文件系统里占了一个位置）时会出现问题”
- **之后**：“物化级联（materialization cascade）出了问题”

这种简洁会在一次次会话里不断复利。

</details>

这套能力已经集成在 [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.zh-CN.md) 里：它是一次拷问式沟通（grilling session），并且能帮你和 AI 建立共享语言（shared language），同时用 ADR（Architecture Decision Record）记录那些难以口头解释的决策。

很难用语言解释它到底有多强。它可能是这个仓库里最酷的一招。试一试你就知道了。

> [!TIP]
> 共享语言（shared language）除了减少啰嗦之外，还有很多好处：
>
> - **变量、函数与文件命名更一致**：都基于共享语言
> - 因此 **代码库更容易被智能体导航**（navigate）
> - 智能体也会 **花更少 token 在思考上**，因为它能使用更简洁的语言

### #3：代码跑不起来

> “永远走小步、走稳步。反馈速度就是你的速度上限。永远不要接一个太大的任务。”
>
> David Thomas & Andrew Hunt，《程序员修炼之道（The Pragmatic Programmer）》

**问题**：假设你和智能体已经对齐了要做什么。那如果智能体 _依然_ 产出一堆垃圾怎么办？

你需要去看你的反馈回路（feedback loops）。如果没有对智能体产出的代码进行实际运行反馈（run feedback），智能体就像蒙眼飞行。

**解决办法**：你需要一整套常见的反馈回路：静态类型（static types）、浏览器访问（browser access）以及自动化测试（automated tests）。

在自动化测试上，红-绿-重构（red-green-refactor）循环非常关键：让智能体先写一个失败的测试，然后修复代码让测试通过。这样能为智能体提供稳定的反馈强度，通常能显著提升产出质量。

我做了一个可以插到任何项目里的 **[`/tdd`](./skills/engineering/tdd/SKILL.zh-CN.md) 技能（skill）**。它鼓励红-绿-重构，并给智能体大量关于好测试与坏测试的指导。

在调试（debugging）方面，我也做了一个 **[`/diagnose`](./skills/engineering/diagnose/SKILL.zh-CN.md)** 技能，把最佳调试实践封装成一个简单循环。

### #4：我们造出了一个泥球（Ball Of Mud）

> “每天都要投资在系统设计上（design）。”
>
> Kent Beck，《极限编程解析（Extreme Programming Explained）》

> “最好的模块（modules）是深的（deep）。它们通过一个简单的接口（interface）暴露大量功能。”
>
> John Ousterhout，《软件设计哲学（A Philosophy Of Software Design）》

**问题**：大多数用智能体构建的应用会变得复杂、难改。因为智能体能极大加速编码，它也会加速软件熵（software entropy）。代码库的复杂度会以前所未有的速度增长。

**解决办法**：对 AI 驱动的开发（AI-powered development）采取一种更激进的新方式：认真对待代码设计（design of the code）。

这被构建进这套技能的每一层：

- [`/to-prd`](./skills/engineering/to-prd/SKILL.zh-CN.md) 会在创建 PRD（Product Requirements Document）之前，围绕你要改动的模块（modules）对你提问
- [`/zoom-out`](./skills/engineering/zoom-out/SKILL.zh-CN.md) 会让智能体把不熟悉的代码放在整体系统语境下解释

更关键的是，[`/improve-codebase-architecture`](./skills/engineering/improve-codebase-architecture/SKILL.zh-CN.md) 能帮你拯救已经变成泥球（ball of mud）的代码库。我建议你每隔几天就在自己的代码库上跑一次。

### 总结

软件工程基本功（software engineering fundamentals）比以往任何时候都更重要。这套技能是我把这些基本功浓缩成可重复实践的一次努力，希望能帮你交付你职业生涯里最好的应用。玩得开心。

## 参考

### Engineering（工程）

我每天用于代码工作的技能。

- **[diagnose](./skills/engineering/diagnose/SKILL.zh-CN.md)** — 面向棘手 bug 与性能回归（performance regressions）的纪律化诊断循环：复现（reproduce）→ 最小化（minimise）→ 假设（hypothesise）→ 埋点/观测（instrument）→ 修复（fix）→ 回归测试（regression-test）。
- **[grill-with-docs](./skills/engineering/grill-with-docs/SKILL.zh-CN.md)** — 以现有领域模型（domain model）为基准拷问你的计划，打磨术语，并内联更新 `CONTEXT.md` 与 ADR（Architecture Decision Record）。
- **[triage](./skills/engineering/triage/SKILL.zh-CN.md)** — 通过一个“分诊角色（triage role）”状态机，对问题（issues）进行分诊。
- **[improve-codebase-architecture](./skills/engineering/improve-codebase-architecture/SKILL.zh-CN.md)** — 基于 `CONTEXT.md` 中的领域语言（domain language）以及 `docs/adr/` 里的决策，寻找代码库中“加深（deepening）”的机会。
- **[setup-matt-pocock-skills](./skills/engineering/setup-matt-pocock-skills/SKILL.zh-CN.md)** — 为每个仓库（repo）搭建配置（问题跟踪器、分诊标签词汇、领域文档布局），供其他工程技能使用。在使用 `to-issues`、`to-prd`、`triage`、`diagnose`、`tdd`、`improve-codebase-architecture` 或 `zoom-out` 之前，每个仓库只需运行一次。
- **[tdd](./skills/engineering/tdd/SKILL.zh-CN.md)** — 采用红-绿-重构（red-green-refactor）循环的测试驱动开发（test-driven development）。一次只构建一个垂直切片（vertical slice）的特性，或修复一个垂直切片的 bug。
- **[to-issues](./skills/engineering/to-issues/SKILL.zh-CN.md)** — 把任何计划（plan）、规格（spec）或 PRD 拆成可独立领取（independently-grabbable）的 GitHub（GitHub）Issue（issue），采用垂直切片（vertical slices）。
- **[to-prd](./skills/engineering/to-prd/SKILL.zh-CN.md)** — 把当前对话上下文（conversation context）整理成 PRD，并作为 GitHub（GitHub）Issue 提交。不做访谈（interview）——只综合你已经讨论过的内容。
- **[zoom-out](./skills/engineering/zoom-out/SKILL.zh-CN.md)** — 让智能体“拉远视角（zoom out）”，为不熟悉的代码区域提供更广的上下文或更高层的视角。
- **[prototype](./skills/engineering/prototype/SKILL.zh-CN.md)** — 构建一次性原型（throwaway prototype）来补全设计：可以是用于状态/业务逻辑问题的可运行终端应用（terminal app），也可以是多个差异巨大的 UI（User Interface）方案，通过一个路由（route）进行切换。

### Productivity（效率）

通用工作流工具，不局限于代码。

- **[caveman](./skills/productivity/caveman/SKILL.zh-CN.md)** — 超压缩沟通模式。通过删掉填充语（filler）来减少约 75% 的 token 消耗，同时保持完整技术准确性（technical accuracy）。
- **[grill-me](./skills/productivity/grill-me/SKILL.zh-CN.md)** — 围绕你的计划或设计进行高强度访谈（interview），直到决策树（decision tree）的每个分支都被明确。
- **[handoff](./skills/productivity/handoff/SKILL.zh-CN.md)** — 把当前对话压缩成一份交接文档（handoff document），便于另一个智能体继续工作。
- **[write-a-skill](./skills/productivity/write-a-skill/SKILL.zh-CN.md)** — 用正确结构、渐进式披露（progressive disclosure）与捆绑资源（bundled resources）创建新技能。

### Misc（杂项）

我会保留但很少用到的工具。

- **[git-guardrails-claude-code](./skills/misc/git-guardrails-claude-code/SKILL.zh-CN.md)** — 为 Claude Code（Claude Code）配置钩子（hooks），在执行危险的 git 命令（push、reset --hard、clean 等）前进行拦截。
- **[migrate-to-shoehorn](./skills/misc/migrate-to-shoehorn/SKILL.zh-CN.md)** — 把测试文件从 `as` 类型断言迁移到 @total-typescript/shoehorn。
- **[scaffold-exercises](./skills/misc/scaffold-exercises/SKILL.zh-CN.md)** — 创建练习目录结构（sections、problems、solutions、explainers）。
- **[setup-pre-commit](./skills/misc/setup-pre-commit/SKILL.zh-CN.md)** — 用 lint-staged（lint-staged）、Prettier（Prettier）、类型检查（type checking）与测试（tests）配置 Husky（Husky）pre-commit 钩子（hooks）。
