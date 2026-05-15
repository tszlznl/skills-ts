---
name: writing-fragments
description: 通过拷问式沟通（grilling session）从用户那里挖出写作碎片（fragments）——各种异质的写作小块（观点、片段（vignettes）、锋利句子、半成想法）——并将它们追加到一个 Markdown 文档中，作为未来文章的原材料（raw material）。用户想先发展想法再上结构，或提到“fragments”“ideate”“raw material”时使用。
---

<what-to-do>

进行一次产出碎片（fragments）的拷问式沟通（grilling session）。围绕用户想写的任何主题高强度追问。不要引入阶段（phases）、大纲（outlines）或结构（structure）——这些明确不在范围内（out of scope）。

当对话任意一方冒出碎片时，把它们追加到一个 Markdown 文件里。用户会在会话过程中编辑这个文件；每次写入前都要重新读一遍，确保保留他们的修改。

如果用户没传入路径，只问一次保存到哪里，然后在本次会话剩余过程中记住该路径。

从用户说的第一句话开始就捕获碎片，包括最初提示词（initial prompt）。

第一次写入时，只在文件顶部放一个工作标题（working title）的 H1，除此之外什么都不写——不要 metadata、不要 TOC、不要日期。

</what-to-do>

<supporting-info>

## 什么是碎片（What is a fragment）

碎片（fragment）是任何可能进入最终文章的文本片段。它必须 _作者能读懂（readable by the author）_——作者知道它什么意思——但不需要定义术语，也不需要让第一次读的人就完全理解。标准是“这是不是一段好文字”，而不是“这是不是一个自洽论证（self-contained argument）”。

碎片刻意保持异质（heterogeneous）。可作为碎片的例子：

- 一句锋利的句子：你想在某处用，但还不知道放哪。
- 一个观点 + 一句理由。
- 一个小片段（vignette）：发生过的事、一段代码、一个场景、一个类比。
- 一个半成想法：“X 的感觉有点像 Y，之后再展开。”
- 一段引语、一段对话、听到的一句话。
- 一组“凭感觉挂在一起”的相关观察。
- 一句抱怨、一段坦白、一个 punchline（punchline）。

小说家的日记是模型：多年无结构的记录，后来被挖掘成原材料。碎片就是这些“注意到的东西（noticings）”。

## 文件格式（File format）

```markdown
# Working title

A first fragment lives here.

It can be multiple paragraphs. It can include lists, code, quotes — whatever
shape the fragment naturally takes.

---

A second fragment.

---

> A quoted line that the user wants to keep around.

A reaction to it.

---

- A cluster of related observations
- That hang together by feel
- And want to be near each other
```

碎片之间用水平分割线（horizontal rule）（`\n---\n`）分隔。正文里不要标题（headings）。不要 tags。顺序就是追加顺序，没有额外排序。

## 写作节奏（Writing rhythm）

静默追加（append silently）。不要每个碎片都弹窗征求许可。可以随口提一句你加了什么（“把这个也加进去了”），但不要用保存对话框打断交流。

每次写入前：从磁盘重新读取文件。用户可能在回合间编辑、重排或删除碎片——要保留他们的修改。永远不要覆盖文件；只做追加（或在用户要求时，对某个具体碎片原位编辑）。

用户随时可能说“把最后一个删掉”“把那个写得更锋利点”“把这两个合并”。把这些当成一等指令处理。

</supporting-info>
