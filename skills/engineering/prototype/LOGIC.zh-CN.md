# Logic Prototype（逻辑原型）

一个小型交互式终端应用（terminal app），让用户手动驱动状态模型（state model）。当问题属于 **业务逻辑（business logic）、状态迁移（state transitions）或数据形状（data shape）** 时使用——这类东西在纸面上看起来合理，但只有在真实案例里推一遍才会暴露“哪里不对劲”。

## 什么时候这种形态是对的（When this is the right shape）

- “我不确定这个状态机能不能处理 X 后再 Y 的边界情况（edge case）。”
- “这个数据模型真的能表达……这种情况吗？”
- “我想在写代码前先摸清 API 应该长什么样。”
- 任何用户想 **按按钮并观察状态变化** 的场景。

如果问题是“它应该长什么样？”——那是错分支。用 [UI.md](UI.md)。

## 流程（Process）

### 1. 明确问题（State the question）

写代码前，先写下你要原型验证的状态模型（state model）以及要回答的问题。一段话即可，放在原型的 README，或文件顶部的注释里。一个回答错问题的逻辑原型纯属浪费——把问题写清楚，方便后续检查，不管用户是现在盯着看，还是 AFK（Away From Keyboard）后再回来。

### 2. 选择语言（Pick the language）

用宿主项目（host project）正在用的语言。如果项目没有明显运行时（runtime）（例如文档仓库），就问用户。

匹配项目已有工具约定——不要为了原型另加新的包管理器（package manager）或运行时。

### 3. 把逻辑隔离成可移植模块（Isolate the logic in a portable module）

把真正回答问题的那段逻辑藏在一个小而纯（pure）的接口后面，将来可以被整体提取并放进真实代码库。TUI（Terminal User Interface）外壳是一次性的；逻辑模块不应该是。

合适的形态取决于问题：

- **纯 reducer（reducer）** — `(state, action) => state`。当 action 是离散事件、state 是单一值时适合。
- **状态机（state machine）** — 显式状态与迁移。当“当前到底允许哪些 action”本身是问题的一部分时适合。
- **一组纯函数（pure functions）** 操作一个朴素数据类型（plain data type）。当没有隐式“当前状态”，只有变换时适合。
- **拥有明确方法表面的 class/module**。当逻辑确实拥有持续存在的内部状态（ongoing internal state）时适合。

选最能回答问题的形态，而不是最容易接 TUI 的形态。保持纯净：不做 I/O、不写终端代码、不用 `console.log` 做控制流。TUI 导入它并调用它；不要反向依赖。

这让原型在自身寿命之外仍然有价值。问题回答完后，被验证过的 reducer/状态机/函数集合可以被提升到真实模块里——TUI 外壳删掉。

### 4. 做一个最小 TUI 来展示状态（Build the smallest TUI that exposes the state）

做成一个 **轻量 TUI**：每一帧清屏（`console.clear()` / `print("\033[2J\033[H")` / 等价方式）并重绘整个界面。用户应始终看到一个稳定视图，而不是不断增长的滚动输出。

每帧包含两部分，按顺序：

1. **当前状态（Current state）**：美观打印且对 diff 友好（每行一个字段，或格式化 JSON）。字段名或分段标题用 **粗体（bold）**，次要上下文（时间戳、ID、派生值）用 **暗色（dim）**。原生 ANSI 转义码即可：`\x1b[1m` 粗体、`\x1b[2m` 暗色、`\x1b[0m` 重置。除非项目已引入样式库，否则无需额外依赖。
2. **快捷键（Keyboard shortcuts）**：列在底部：`[a] add user  [d] delete user  [t] tick clock  [q] quit`。按键加粗、描述变暗，或反过来——以清晰为准。

行为（Behaviour）：

1. **初始化状态（Initialise state）** — 一个内存对象/结构体。启动时渲染第一帧。
2. **一次读取一个按键（或一行输入）**，派发到 handler 去修改状态。
3. 每次动作后 **重绘（Re-render）** 整帧——不要追加输出，要替换。
4. **循环直到退出（Loop until quit）。**

整帧应能在一屏显示完。

### 5. 用一个命令可运行（Make it runnable in one command）

把运行脚本加入项目已有任务运行器（`package.json` scripts、`Makefile`、`justfile`、`pyproject.toml`）。用户应该能运行 `pnpm run <prototype-name>` 或等价命令——不用记路径。

如果宿主项目没有任务运行器，就把命令写在原型 README 的顶部。

### 6. 交付给用户（Hand it over）

把运行命令给用户。他们会自己驱动；关键时刻通常是他们说“等等，这不应该允许”或“咦，我以为 X 会不一样”——那就是 _想法_ 的 bug，这才是原型的目的。如果他们想加新动作，就加。原型会演进。

### 7. 记录答案（Capture the answer）

原型完成职责后，唯一值得保留的是对问题的答案。如果用户在线，问问它教会了他们什么。如果不在线，在原型旁放一个 `NOTES.md`，让结论能在删除前被补上（或者如果你观察了全过程，也可以由你补上）。

## 反模式（Anti-patterns）

- **不要加测试。** 需要测试的原型就不再是原型了。
- **不要接真实数据库。** 除非问题明确是持久化（persistence），否则用内存存储。
- **不要过度泛化（generalise）。** 不要做“以后也许支持 X”的假设。原型只回答一个问题。
- **不要把逻辑与 TUI 混在一起。** 如果 reducer/状态机引用了 `console.log`、prompt 或终端转义码，它就不再可移植。保持 TUI 是纯模块之上的薄壳。
- **不要把 TUI 外壳带进生产环境。** 外壳是为终端手动驱动优化的。值得保留的是背后的逻辑模块。
