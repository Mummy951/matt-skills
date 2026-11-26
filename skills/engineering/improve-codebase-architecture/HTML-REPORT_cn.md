# HTML 报告格式

架构评审以单个自包含 HTML 文件的形式渲染，存放于操作系统临时目录。Tailwind 与 Mermaid 均来自 CDN。Mermaid 能够可靠地处理图形化图表；手工构建的 div 与内联 SVG 处理偏编辑性的视觉内容（体量图、剖面图）。两者混合使用：不要把一切都交给 Mermaid，否则图表会开始显得千篇一律。

## 骨架

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Architecture review for {{repo name}}</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script type="module">
      import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
      mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
    </script>
    <style>
      /* small custom layer for things Tailwind doesn't cover cleanly:
         dashed seam lines, hand-drawn-feeling arrow heads, etc. */
      .seam { stroke-dasharray: 4 4; }
      .leak { stroke: #dc2626; }
      .deep { background: linear-gradient(135deg, #0f172a, #1e293b); }
    </style>
  </head>
  <body class="bg-stone-50 text-slate-900 font-sans">
    <main class="max-w-5xl mx-auto px-6 py-12 space-y-12">
      <header>...</header>
      <section id="candidates" class="space-y-10">...</section>
      <section id="top-recommendation">...</section>
    </main>
  </body>
</html>
```

## 头部

仓库名、日期，以及一个紧凑图例：实心方框 = 模块，虚线 = 接缝，红色箭头 = 泄漏，深色粗框 = 深模块。不写介绍段落。直接进入候选方案。

## 候选方案卡片

图表承担主要表达。文字精简、朴素，并直接使用（来自 `/codebase-design` 技能的）术语表术语，不做客套铺陈。

每个候选方案是一个 `<article>`：

- **标题**：简短，点出深化方向（例如 "Collapse the Order intake pipeline"）。
- **徽章行**：推荐强度（`Strong` = 翠绿，`Worth exploring` = 琥珀，`Speculative` = 板岩灰），外加依赖类别的标签（`in-process`、`local-substitutable`、`ports & adapters`、`mock`）。
- **文件**：等宽字体列表，`font-mono text-sm`。
- **改造前 / 改造后图表**：核心部分。两栏并排。参见下文模式。
- **问题**：一句话。痛点是什么。
- **方案**：一句话。改变了什么。
- **收益**：要点列表，每条不超过 6 个词。例如 "Tests hit one interface"、"Pricing logic stops leaking"、"Delete 4 shallow wrappers"。
- **ADR 标注**（如适用）：放在琥珀色底纹框中的一行。

不写解释性段落。如果图表需要一段文字才能看懂，就重画图表。

## 图表模式

挑选适合候选方案的模式。混合使用。不要让每张图表看起来都一样。多样性本身就是目的之一。

### Mermaid 图（依赖 / 调用流程的主力）

当要点是「X 调用 Y，Y 调用 Z，看看这团乱麻」时，使用 Mermaid `flowchart` 或 `graph`。用 Tailwind 样式的卡片包裹它，避免像是空降进来的。用 classDef 把泄漏边标红、把深模块标暗。时序图很适合表达「改造前：6 次往返；改造后：1 次」。

```html
<div class="rounded-lg border border-slate-200 bg-white p-4">
  <pre class="mermaid">
    flowchart LR
      A[OrderHandler] --> B[OrderValidator]
      B --> C[OrderRepo]
      C -.leak.-> D[PricingClient]
      classDef leak stroke:#dc2626,stroke-width:2px;
      class C,D leak
  </pre>
</div>
```

### 手工搭建的方框与箭头（当 Mermaid 的布局跟你较劲时）

模块做成带边框和标签的 `<div>`。箭头做成内联 SVG 的 `<line>` 或 `<path>` 元素，在相对定位容器上绝对定位。当你希望「改造后」图表呈现为一个粗边框、内部置灰的深模块时，就用这种方式，因为 Mermaid 无法以合适的权重渲染出这种效果。

### 剖面图（适合呈现分层式浅薄）

堆叠水平条带（`h-12 border-l-4`），展示一次调用穿过的各层。改造前：6 个薄层，每层什么都没做。改造后：1 个粗条带，标注合并后的职责。

### 体量图（适合「接口与实现一样宽」）

每个模块两个矩形：一个表示接口表面积，一个表示实现。改造前：接口矩形几乎和实现矩形一样高（浅薄）。改造后：接口矩形矮，实现矩形高（深厚）。

### 调用图折叠

改造前：函数调用树渲染为嵌套方框。改造后：同一棵树折叠为一个方框，如今已属内部的调用在其中淡化显示。

## 风格指引

- 偏编辑排版风，而非企业仪表盘风。留白充足。标题可选用衬线字体（`font-serif` 搭配 stone/slate 效果不错）。
- 克制用色：一个强调色（翠绿或靛蓝），泄漏用红色，警告用琥珀色。
- 图表高度保持在约 320px，使改造前 / 后能舒服地并排显示而无需滚动。
- 图内模块标签使用 `text-xs uppercase tracking-wider`，让它们读起来像示意图，而不是 UI。
- 仅有的脚本是 Tailwind CDN 与 Mermaid ESM 导入。除此之外报告是静态的：没有应用代码，除 Mermaid 自身渲染外没有任何交互。

## 首选推荐部分

一张更大的卡片。候选方案名称、一句理由，以及指向其卡片的锚点链接。仅此而已。

## 语气

朴素的文字，简洁，但架构上的名词与动词直接取自 `/codebase-design` 技能。简洁不是跑题的借口。

**严格使用：** module、interface、implementation、depth、deep、shallow、seam、adapter、leverage、locality。

**绝不用以下词替代：** component、service、unit（指代模块时）· API、signature（指代接口时）· boundary（指代接缝时）· layer、wrapper（指代模块时，本意是模块）。

**契合该风格的表述：**

- "Order intake module is shallow: interface nearly matches the implementation."（订单录入模块很浅：接口几乎等同于实现。）
- "Pricing leaks across the seam."（定价逻辑渗出接缝、外溢到别处。）
- "Deepen: one interface, one place to test."（深化：一个接口，一处测试点。）
- "Two adapters justify the seam: HTTP in prod, in-memory in tests."（两个适配器足以让接缝成立：生产用 HTTP，测试用内存实现。）

**收益要点**用术语表术语命名收益：*"locality: bugs concentrate in one module"*、*"leverage: one interface, N call sites"*、*"interface shrinks; implementation absorbs the wrappers"*。不要写 *"easier to maintain"* 或 *"cleaner code"*，因为这些词不在术语表里，也配不上它们的位置。

不躲闪、不铺垫、不写「值得注意的是……」。一句话能写成要点就写成要点。一个要点能删掉就删掉。如果某个术语不在 `/codebase-design` 术语表中，先去挑一个表里有的，再考虑创造新词。
