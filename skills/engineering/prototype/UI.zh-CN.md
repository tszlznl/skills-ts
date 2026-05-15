# UI Prototype（界面原型）

在同一个路由（route）上生成 **多个差异巨大的 UI（User Interface）变体**，并通过一个悬浮底部栏切换。用户在浏览器里来回切换变体，挑一个（或从每个里偷点元素），然后把其他的都扔掉。

如果问题是逻辑/状态（logic/state），而不是“长什么样”——那是错分支。用 [LOGIC.md](LOGIC.md)。

## 什么时候这种形态是对的（When this is the right shape）

- “这个页面应该长什么样？”
- “我想在确定这个仪表盘（dashboard）之前先看看几个选项。”
- “给设置页（settings screen）换个布局试试。”
- 任何用户本来会花一天时间在脑内纠结三张模糊草图的场景。

## 两种子形态——强烈优先 A（Two sub-shapes）

UI 原型更容易判断的前提是：它 **贴着应用其他部分**——真实 header、真实 sidebar、真实数据、真实密度（density）。单独一条一次性路由就是个真空：每个变体在隔离环境下看起来都“还行”。只要有合理的宿主页面（host page），就默认用子形态 A。只有当原型确实找不到附近的“家”时，才用子形态 B。

### 子形态 A —— 在现有页面上调整（优先）（Sub-shape A）

路由（route）已经存在。变体渲染在 **同一路由** 上，通过 `?variant=` URL 查询参数（search param）进行分流。现有的数据获取（data fetching）、参数（params）、认证（auth）保持不变——只替换渲染子树（rendering subtree）。这是默认选项；除非有明确理由，否则就选它。

如果原型要验证的东西还没有独立页面，但 *自然会嵌在某个现有页面里*（仪表盘新增一个区域、设置页新增一张卡片、现有流程中新增一步）——这仍然属于子形态 A：把变体挂到宿主页面里。

### 子形态 B —— 新页面（最后手段）（Sub-shape B）

只有当被原型验证的东西确实没有任何现有页面可容纳时才用——例如全新的顶级界面（top-level surface），或无法合理嵌入任何地方的流程（flow）。

按项目已有路由约定创建一条 **一次性路由（throwaway route）**——不要发明新的顶层结构。命名要明显是原型（例如路径或文件名包含 `prototype`）。同样使用 `?variant=` 模式。

在选择子形态 B 前先做一次 sanity-check：真的没有任何现有页面可以嵌进去吗？空路由会掩盖设计问题，而被真实内容填满的页面会更早暴露这些问题。

两种子形态的悬浮底部栏完全相同。

## 流程（Process）

### 1. 写清问题并选择 N（State the question and pick N）

默认做 **3 个变体（variants）**。超过 5 个时，“差异巨大”会变成噪音——最多 5 个。

用一行写下计划，放在原型位置或文件顶部注释里：

> “Three variants of the settings page, switchable via `?variant=`, on the existing `/settings` route.”

无论用户在线与否，这都能作为对齐锚点。

### 2. 生成差异巨大的变体（Generate radically different variants）

为每个变体起草草案。每个变体都必须遵守：

- 页面目标（purpose）与可用数据（data it has access to）
- 项目的组件库/样式系统（component library / styling system）（TailwindCSS（TailwindCSS）、shadcn（shadcn）、MUI（MUI）、纯 CSS（plain CSS）等）
- 清晰的导出组件名（exported component name），例如 `VariantA`、`VariantB`、`VariantC`

变体必须在结构上不同（structurally different）——布局不同、信息层级不同、主操作（primary affordance）不同，而不只是颜色不同。三张“稍微调过的卡片网格”不是 UI 原型，那是壁纸。如果两个草案太像，重做其中一个，并明确写“不要用卡片网格（do not use a card grid）”。

### 3. 把它们串起来（Wire them together）

在该路由上创建一个切换器组件：

```tsx
// pseudo-code — adapt to the project's framework
const variant = searchParams.get('variant') ?? 'A';
return (
  <>
    {variant === 'A' && <VariantA {...data} />}
    {variant === 'B' && <VariantB {...data} />}
    {variant === 'C' && <VariantC {...data} />}
    <PrototypeSwitcher variants={['A','B','C']} current={variant} />
  </>
);
```

对子形态 A（现有页面）：把现有数据获取（data fetching）留在 switcher 之上；每个变体只替换渲染子树（rendered subtree）。

对子形态 B（新页面）：在 `/prototype/<name>` 这条一次性路由下挂同一个 switcher。

### 4. 构建悬浮切换条（Build the floating switcher）

在屏幕底部居中做一个固定定位（fixed-position）的小条，包含三部分：

- **左箭头（Left arrow）** — 切到上一个变体（循环）。
- **变体标签（Variant label）** — 显示当前变体 key，以及（如果变体导出名称）名称，例如 `B — Sidebar layout`。
- **右箭头（Right arrow）** — 切到下一个变体（循环）。

行为（Behaviour）：

- 点击箭头会更新 URL 查询参数。使用框架路由能力（例如 Next 的 `router.replace`、React Router 的 `navigate`），保证可分享并且刷新后仍稳定（reload-stable）。
- 键盘：`←` 与 `→` 也能切换。不要在 `<input>`、`<textarea>` 或 `[contenteditable]` 获得焦点时拦截方向键。
- 视觉上要与页面本身区分开（例如高对比胶囊形（pill）+ 轻阴影），让人一眼看出它不是被评审设计的一部分。
- 在生产构建（production builds）中隐藏：用 `process.env.NODE_ENV !== 'production'` 或等价判断进行 gating，避免一次不小心合并原型后把切换条发到真实用户那里。

把 switcher 做成一个共享组件，便于两种子形态复用。放到项目共享 UI 所在位置。

### 5. 交付给用户（Hand it over）

把 URL（以及 `?variant=` 的 keys）告诉用户。用户会在方便时切换浏览。最有价值的反馈往往是：“我想要 B 的 header + C 的 sidebar”——那才是他们真正想要的设计。

### 6. 记录结论并清理（Capture the answer and clean up）

一旦某个变体胜出，就记录选了哪一个、为什么（commit message、ADR、issue；若 AFK 运行且用户尚未回复，就在原型旁写 `NOTES.md`）。然后：

- **子形态 A** — 删除落败变体与 switcher；把胜出方案折叠进现有页面。
- **子形态 B** — 把胜出变体升级为真实路由；删除一次性路由与 switcher。

不要把变体组件或 switcher 留在仓库里。它们会很快腐烂并迷惑下一位读者。

## 反模式（Anti-patterns）

- **变体只在颜色或文案上不同。** 这只是 tweak，不是原型。真正的变体应该在结构上“意见不合”。
- **变体之间共享过多代码。** 共享一个 `<Header>` 没问题；共享一个 `<Layout>` 就打破了目的。每个变体都应该能自由丢弃布局。
- **把变体接到真实写操作（real mutations）上。** 只读原型（read-only prototypes）没问题。若变体需要写入，指向 stub（stub）——问题是“它长什么样”，不是“后端能不能跑”。
- **直接把原型升级为生产代码。** 变体代码是在原型约束下写的（无测试、最少错误处理）。折叠进真实代码时要按生产标准重写。
