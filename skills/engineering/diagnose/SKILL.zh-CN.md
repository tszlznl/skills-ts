---
name: diagnose
description: 面向棘手 bug 与性能回归（performance regressions）的纪律化诊断循环：复现（reproduce）→ 最小化（minimise）→ 假设（hypothesise）→ 埋点/观测（instrument）→ 修复（fix）→ 回归测试（regression-test）。当用户说“diagnose this”/“debug this”、报告 bug、表示某处坏了/抛错/失败，或描述性能回归时使用。
---

# Diagnose（诊断）

一套用于处理棘手 bug 的纪律（discipline）。只有在明确能给出理由时才跳过某个阶段。

在探索代码库（codebase）时，先使用项目的领域术语表（domain glossary）建立相关模块（modules）的清晰心智模型（mental model），并查看你要改动区域的 ADR（Architecture Decision Record）。

## 阶段 1 —— 建立反馈回路（feedback loop）

**这一步就是这个技能的核心。** 其他一切都是机械执行。只要你能为该 bug 建立一个快速、确定性（deterministic）、智能体可运行的“通过/失败”信号（pass/fail signal），你就能找到根因——二分（bisection）、假设验证（hypothesis-testing）、埋点（instrumentation）都只是消耗这个信号而已。如果你没有这个信号，再怎么盯着代码看也救不了你。

在这一步投入远超比例的精力。**要强势。要有创造力。拒绝放弃。**

### 构造方式——大致按这个顺序尝试

1. **失败的测试（failing test）**：在能触达 bug 的切面（seam）上写单测/集成测试/e2e。
2. **Curl / HTTP 脚本**：对正在运行的开发服务器（dev server）发请求。
3. **CLI 调用**：给定固定输入（fixture input），将 stdout 与已知正确快照（known-good snapshot）做 diff。
4. **无头浏览器脚本（headless browser script）**（Playwright（Playwright）/Puppeteer（Puppeteer））：驱动 UI，断言 DOM/console/network。
5. **回放已捕获的 trace（trace）**：把真实网络请求/负载（payload）/事件日志（event log）保存到磁盘，再在隔离环境里回放该代码路径。
6. **一次性测试桩（throwaway harness）**：启动系统的最小子集（一个服务、mock 掉依赖），用一次函数调用触发 bug 路径。
7. **性质测试/模糊测试循环（property/fuzz loop）**：如果 bug 是“有时候输出不对”，跑 1000 个随机输入，寻找失败模式（failure mode）。
8. **二分测试桩（bisection harness）**：如果 bug 出现在两个已知状态之间（commit、dataset、version），就把“在状态 X 启动并检查”的流程自动化，以便 `git bisect run`。
9. **差分循环（differential loop）**：让同一输入在旧版本 vs 新版本（或两套配置）上运行，并对比输出。
10. **HITL bash 脚本（Human-In-The-Loop）**：最后手段。如果必须让人点击，就用 `scripts/hitl-loop.template.sh` 去驱动 _人_，让回路仍然结构化。捕获的输出再反馈给你。

建立正确的反馈回路，bug 就已经修了 90%。

### 迭代反馈回路本身

把回路当成产品（product）。一旦你有了 _一个_ 回路，就继续问：

- 能不能更快？（缓存初始化、跳过无关启动、缩小测试范围）
- 能不能让信号更尖锐？（断言具体症状，而不是“没崩溃”）
- 能不能更确定性？（固定时间、固定 RNG（Random Number Generator）种子、隔离文件系统、冻结网络）

一个 30 秒且易抖（flaky）的回路，几乎不比没有回路强多少。一个 2 秒的确定性回路，是调试超能力（debugging superpower）。

### 非确定性 bug（Non-deterministic bugs）

目标不是“完美复现”，而是 **更高的复现率（higher reproduction rate）**。把触发流程循环 100 次，并行化、加压、缩窄时间窗口、注入 sleep。一个 50% 抖动的 bug 是可调的；1% 不是——继续把复现率抬高到可调为止。

### 当你确实无法建立回路

停下来，并明确说出来。列出你尝试过的东西。向用户请求：（a）访问能复现它的环境；（b）一份捕获的产物（HAR 文件（HAR file）、日志 dump（log dump）、core dump（core dump）、带时间戳的录屏）；或（c）允许添加临时的生产环境埋点（temporary production instrumentation）。在没有回路前，**不要**进入“假设”阶段。

在你拥有一个你相信的回路之前，不要进入阶段 2。

## 阶段 2 —— 复现（Reproduce）

运行回路。观察 bug 出现。

确认：

- [ ] 回路触发的是 **用户** 描述的失败模式（failure mode），而不是“刚好附近”的另一种失败。修错 bug = 修错方向。
- [ ] 失败能在多次运行中复现（或对非确定性 bug：复现率足够高，可以据此调试）。
- [ ] 已捕获精确症状（错误信息、错误输出、慢的耗时），确保后续阶段能验证修复确实命中它。

在你复现 bug 之前，不要继续。

## 阶段 3 —— 提出假设（Hypothesise）

在测试任何一个假设之前，先生成 **3–5 个按优先级排序的假设（ranked hypotheses）**。只生成单一假设会把你锚定在第一个看起来合理的点上。

每个假设必须是 **可证伪（falsifiable）** 的：写出它的预测（prediction）。

> 格式： “如果 <X> 是原因，那么 <改变 Y> 会让 bug 消失 / <改变 Z> 会让它更糟。”

如果你说不出预测，那这个假设只是“感觉（vibe）”——丢掉或把它打磨清楚。

**在开始测试前，把排序后的假设列表展示给用户。** 他们常常有领域知识（domain knowledge）能立刻重排（“我们刚上线了第 3 条相关改动”），或知道哪些假设他们已经排除。便宜的检查点，巨大的省时。不要卡住——如果用户 AFK（Away From Keyboard），就按你的排序继续。

## 阶段 4 —— 埋点/观测（Instrument）

每个探针（probe）都必须对应阶段 3 里的某条具体预测。**一次只改变一个变量。**

工具优先级：

1. 如果环境支持，优先用 **Debugger / REPL（Read-Eval-Print Loop）检查**。一个断点胜过十条日志。
2. 在能区分不同假设的边界处加 **定点日志（targeted logs）**。
3. 永远不要“全量打日志再 grep”。

**给每条调试日志加唯一前缀**，例如 `[DEBUG-a4f2]`。这样收尾清理时一次 grep 就能删干净。没前缀的日志会活下来；带前缀的日志必须死掉。

**性能分支（Perf branch）**：对性能回归（performance regressions），日志往往不对路。应该：先建立基线测量（baseline measurement）（计时桩（timing harness）、`performance.now()`、profiler（profiler）、query plan（query plan）），然后做二分。先测量，再修复。

## 阶段 5 —— 修复 + 回归测试（Fix + regression test）

**在修复之前** 先写回归测试（regression test）——但前提是存在一个 **正确的切面（correct seam）**。

正确切面指：测试能在调用点（call site）上覆盖 bug 的 **真实模式（real bug pattern）**。如果唯一可用切面太浅（例如 bug 需要多个调用者但你只能写单调用者测试；或单测无法复刻触发链路），在那里写回归测试会给你虚假的信心。

**如果不存在正确切面，这本身就是结论（finding）。** 记录它：代码库架构（codebase architecture）正在阻止你把 bug 锁死（lock down）。把它标记为下一阶段需要处理的点。

如果存在正确切面：

1. 把最小化后的复现（minimised repro）变成该切面上的一个失败测试。
2. 观察它失败。
3. 应用修复。
4. 观察它通过。
5. 用阶段 1 的反馈回路重新跑一次最初（未最小化）的场景。

## 阶段 6 —— 清理 + 复盘（Cleanup + post-mortem）

在宣告完成前必须满足：

- [ ] 最初复现不再复现（重跑阶段 1 回路）
- [ ] 回归测试通过（或已记录“缺少正确切面”）
- [ ] 所有 `[DEBUG-...]` 埋点都被移除（grep 该前缀）
- [ ] 一次性原型（throwaway prototypes）已删除（或移动到清晰标注的 debug 位置）
- [ ] 最终被证实正确的假设写进 commit / PR（Pull Request）信息里——方便下一位调试者学习

**然后再问：什么能避免这个 bug？** 如果答案涉及架构改动（没有好测试切面、调用者纠缠、隐藏耦合），把具体信息交给 `/improve-codebase-architecture` 技能。建议要在修复落地 **之后** 再给，而不是之前——你现在掌握的信息，比开始时更多。
