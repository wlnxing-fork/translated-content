---
title: 掌握弹性物件的包装
slug: Web/CSS/CSS_flexible_box_layout/Mastering_wrapping_of_flex_items
---

Flexbox 被设计为一维的布局工具，这意味着在处理元素布局方式时只能设计为行或者设计为列——两者不能兼顾。然而 Flex 拥有一种将 flex 元素包围在新的一行内的特性，创建一个新的行如果设置了 {{cssxref("flex-direction")}} 为 `row` 以及创建新的一列如果设置了 `flex-direction` 为 `column`。在这篇教程中，我将解释这个特性的内部工作原理，以及它设计的目的是什么和在什么情况下要使用 [CSS 网格布局](/zh-CN/docs/Web/CSS/CSS_grid_layout)而不是弹性盒子。

## 把物件包装起来

{{cssxref("flex-wrap")}} 属性的初始值是`nowrap`。这就意味着如果你有一组的对其容器而言太宽的弹性物件，它们就会溢出。如果你想要一旦它们变得太宽就换行，你必须给 `flex-wrap` 属性添加`wrap`值，或者，用`row wrap` 或 `column wrap` 值作用于{{cssxref("flex-flow")}}的速写。

物件接着就会在容器内换行。在接下来的例子里，我有 10 个`160px`的`flex-basis`物件，它们都具备伸展和收缩能力。一旦第一行达到了没有足够空间放置额外 160 像素物件的点，一个新的弹性行就会被建立，一直这样直到所有的物件被放置。因为物件可以伸展，它们会扩展大于 160 像素从而完整地填充一行。如果在最后一行只有 1 个物件，它就将拉长了充满整行。

```html live-sample___row-wrap
<div class="box">
  <div>One</div>
  <div>Two</div>
  <div>Three</div>
  <div>Four</div>
  <div>Five</div>
  <div>Six</div>
  <div>Seven</div>
  <div>Eight</div>
  <div>Nine</div>
  <div>Ten</div>
</div>
```

```css live-sample___row-wrap
.box {
  width: 500px;
  border: 2px dotted rgb(96 139 168);
  display: flex;
  flex-wrap: wrap;
}

.box > * {
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
  flex: 1 1 160px;
}
```

{{EmbedLiveSample("row-wrap")}}

我们可以看到相同的事情也发生在列上。容器会需要有一个高度让物件可以开始换行并且制造新的列，并且物件会拉伸高度来填满每一个列。

```html live-sample___column-wrap
<div class="box">
  <div>One</div>
  <div>Two</div>
  <div>Three</div>
  <div>Four</div>
  <div>Five</div>
  <div>Six</div>
  <div>Seven</div>
  <div>Eight</div>
  <div>Nine</div>
  <div>Ten</div>
</div>
```

```css live-sample___column-wrap
.box {
  border: 2px dotted rgb(96 139 168);
  height: 300px;
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
}
.box > * {
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
  flex: 1 1 80px;
}
```

{{EmbedLiveSample("column-wrap", "", "320px")}}

## 包装和弹性方向

当结合 `flex-direction` 属性，包装就如你所期待的方式工作。如果 `flex-direction` 被设置成 row-reverse 那么物件就会从容器的底边开始并且以行的反向顺序堆叠自身。

```html live-sample___row-reverse-wrap
<div class="box">
  <div>One</div>
  <div>Two</div>
  <div>Three</div>
  <div>Four</div>
  <div>Five</div>
  <div>Six</div>
  <div>Seven</div>
  <div>Eight</div>
  <div>Nine</div>
  <div>Ten</div>
</div>
```

```css live-sample___row-reverse-wrap
.box {
  border: 2px dotted rgb(96 139 168);
  display: flex;
  flex-wrap: wrap;
  flex-direction: row-reverse;
  width: 500px;
}
.box > * {
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
  flex: 1 1 160px;
}
```

{{EmbedLiveSample("row-reverse-wrap")}}

注意反向只能发生在行内、行的方向。我们从右面开始然后跳到第二行再从右边开始。我们没有在两个方向上都反向，从容器底部上来的反向！

## 一维布局介绍

正如我们上面例子中所见到的，如果我们的物件被允许伸展和收缩，当最后一行或一列有较少的物件时，那么这些物件就会伸展从而填满可用空间。

弹性盒子中并没有方法告诉一行里的物件和上一行里的物件对齐——每个弹性行表现得就像一个新的弹性容器。它在主要坐标轴上处理空间分布。如果只有一个物件，并且这个物件允许伸展，他就会填充坐标轴，就好像你有一个单物件的弹性容器。

如果你想在二维方向上布局，那么你很可能想要网格布局。我们可以比较我们上面的行换行例子和 CSS 网格版本的布局来看一下区别。一下实时的例子使用 CSS 网格布局来构建一个布局，它具有至少 160 像素的列的布局，在所有列之间分配额外的空间。

```html live-sample___grid-example
<div class="box">
  <div>One</div>
  <div>Two</div>
  <div>Three</div>
  <div>Four</div>
  <div>Five</div>
  <div>Six</div>
  <div>Seven</div>
  <div>Eight</div>
  <div>Nine</div>
  <div>Ten</div>
</div>
```

```css live-sample___grid-example
.box {
  border: 2px dotted rgb(96 139 168);
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
  width: 500px;
}

.box > * {
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
}
```

{{EmbedLiveSample("grid-example")}}

这就是一维和二维布局的区别。在一维的方式里就像弹性盒子，我们仅仅控制行或者列。在二维布局里就像网格，我们同时控制两个。如果你想按行分布空间，使用弹性盒子。如果不是，使用网格。

## 基于弹性盒子的网格系统如何工作？

基本上基于弹性盒子的网格系统，是通过将弹性盒子带回到我们所熟悉的基于浮层布局的世界，来工作的。如果你对弹性物件设置了百分比的宽度——`flex-basis` 或是通过对物件增加宽度同时让 `flex-basis` 的值保持为`auto`——你就能获得二维布局的印象。你可以在下面的例子中看到这样的运作。

这里我有设置 `flex-grow` 和 `flex-shrink` 为 `0` 来使固定弹性物件，并且接着使用百分比来控制弹性，正如我们在浮动布局里所使用的那样。

```html live-sample___flex-grid
<div class="box">
  <div>One</div>
  <div>Two</div>
  <div>Three</div>
  <div>Four</div>
  <div>Five</div>
  <div>Six</div>
  <div>Seven</div>
  <div>Eight</div>
  <div>Nine</div>
  <div>Ten</div>
</div>
```

```css live-sample___flex-grid
* {
  box-sizing: border-box;
}

.box {
  width: 500px;
  border: 2px dotted rgb(96 139 168);
  display: flex;
  flex-wrap: wrap;
}

.box > * {
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
  flex: 0 0 33.3333%;
}
```

{{EmbedLiveSample("flex-grid")}}

如果你需要弹性物件在坐标轴上对齐，用这样的方法来控制宽度就能达到这个效果。在大多数的情况下，向弹性物件增加款宽度的做法演示了你可能会比将组件切换成网格布局提供的更好体验。

## 在物件之间建立间隔

当包装弹性物件的时候，间隔它们的需要很可能被提出。在当下我们还没有任何针对弹性盒子的[块对齐模块](https://www.w3.org/TR/css-align-3/)间隔属性的实现。在未来我们将能够对弹性盒子简单的使用 `row-gap` 和 `column-gap` 就如我们在 CSS 网格里做的。当下你会需要使用边距来达成这个要求。

你可以从下面一个实时的例子里看到，为了建立间隔并且不在容器的边缘建立间隔，我们需要在弹性容器自身上使用负边距。弹性容器的任何一条边移入第二个包装从而使得负边距可以将包装的元素拉回来。

正是这个间隔属性的需求，一旦实现，将为我们解决问题。适当的间隙只发生在物件的内边缘。

```html live-sample___gaps
<div class="wrapper">
  <div class="box">
    <div>One</div>
    <div>Two</div>
    <div>Three</div>
    <div>Four</div>
    <div>Five</div>
    <div>Six</div>
    <div>Seven</div>
    <div>Eight</div>
    <div>Nine</div>
    <div>Ten</div>
  </div>
</div>
```

```css live-sample___gaps
.wrapper {
  border: 2px dotted rgb(96 139 168);
  width: 500px;
}
.box {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
}
.box > * {
  flex: 1 1 160px;
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
}
```

{{EmbedLiveSample("gaps", "", "220px")}}

## 折叠物件

弹性盒子标准详述了如果一个弹性物件在物件上被设置了 `visibility: collapse` 时应当发生什么。参见对于{{cssxref("visibility")}}属性的 MDN 文档。这份标准描述了如下的表现：

> “定义可见性：在一个弹性物件上的折叠导致它成为一个折叠弹性物件，在一个表行或者表列制造一个类似于 visibility:collapse 的效果：折叠弹性物件整个地从渲染中移除，但在底层保留了一个“支撑”从而保持了弹性行的跨界尺寸的稳定。因此，如果一个弹性容器仅包含一个弹性行，动态地折叠或者展开物件可能改变弹性容器的主尺寸，但是这样确保了跨界尺寸上没有影响以及不会导致页面其余的布局“晃动”。动态行包装在折叠后会被重做的，所以，包含多行的弹性容器的跨界尺寸是可能变化的。” - [折叠物件](https://www.w3.org/TR/css-flexbox-1/#visibility-collapse)

如果你想要针对弹性物件使用 JavaScript 来显示和隐藏内容的例子，这样的表现是很有用的。这个标准里的例子演示了一个这样的模式。

在下面的实时例子里，我有一个非包装的弹性容器。第三个物件相比其他的有跟多的内容被设置 `visibility: collaps` 并且因此弹性盒子会保持一个高度的结构来满足显示物件的需要。如果你从 CSS 里移除`visibility: collapse` 或是将值改变为 `visible`，你会看到物件消失了并且空间在非包装的物件之间重新分配；弹性容器的高度不应该改变。

> [!NOTE]
> 对下面的两个例子使用 Firefox 浏览器，因为 Chrome 和 Safari 会把折叠处理为隐藏。

```html hidden live-sample___visibility-collapse
<p>
  <label><input type="checkbox" /> Toggle <code>visibility</code> value</label>
</p>
```

```html live-sample___visibility-collapse
<div class="box">
  <div>One</div>
  <div>Two</div>
  <div class="collapse">Three <br />has <br />extra <br />text</div>
</div>
```

```css live-sample___visibility-collapse
.box {
  border: 2px dotted rgb(96 139 168);
  display: flex;
  width: 600px;
}
.box > * {
  flex: 1 1 200px;
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
}
.collapse {
  visibility: collapse;
}
```

```css hidden live-sample___visibility-collapse
p:has(:checked) + div .collapse {
  visibility: visible;
}
```

{{EmbedLiveSample("visibility-collapse")}}

当处理多行弹性容器时你会需要理解包装会在折叠*后*被重做。所以浏览器需要重做包装的行为来计算新的还留在行内方向里的被折叠物件的空间。

这意味着物件可能在和他们所开始的不同的行里结束。在一个物件在被显示和隐藏的场景下，它很可能导致物件结束在不同的行里。

我已经在下一个实时例子里构建了这样的表现。你可以看到基于折叠物件的位置，伸展改变了它们所在的行。如果你加入更多的内容到第二个物件里，一旦获得了足够多的内容它就改变了行。然后首行仅仅成为和单行文本一样的高度。

```html hidden live-sample___wrapped-visibility-collapse
<p>
  <label><input type="checkbox" /> Toggle <code>visibility</code> value</label>
</p>
```

```html live-sample___wrapped-visibility-collapse
<div class="box">
  <div>One</div>
  <div>Two is the width of this sentence.</div>
  <div class="collapse">Three <br />is <br />five <br />lines <br />tall.</div>
  <div>Four</div>
  <div>Five<br />Five</div>
  <div>Six</div>
  <div>Seven</div>
  <div>Eight</div>
  <div>Nine</div>
  <div>Ten</div>
  <div>Eleven is longer</div>
</div>
```

```css live-sample___wrapped-visibility-collapse
.box {
  border: 2px dotted rgb(96 139 168);
  width: 500px;
  display: flex;
  flex-wrap: wrap;
}
.box > * {
  padding: 10px;
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
  flex: 1 1 auto;
  min-width: 50px;
}
.collapse {
  visibility: collapse;
}
```

```css hidden live-sample___wrapped-visibility-collapse
p:has(:checked) + div .collapse {
  visibility: visible;
}
```

{{EmbedLiveSample("wrapped-visibility-collapse", "", "300")}}

如果这对你的布局造成了麻烦，可能就需要重新考虑这个结构，比如，将每一行放置到分开的弹性容器里从而它们不会跨行。

### `visibility: hidden` 和 `display: none` 的区别

当你为了隐藏一个物件去设置 `display: none` ，物件就被格式化的页面结构移除。在实际中的意义就是技术器忽略了它，类似转换的事情不会运行。使用 `visibility: hidden` 保持了格式化结构的框，这十分有用，它仍旧表现的像是布局中的一部分即便用户看不到它。
