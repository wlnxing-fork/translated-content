---
title: 控制弹性元素在主轴上的比例
slug: Web/CSS/CSS_flexible_box_layout/Controlling_ratios_of_flex_items_along_the_main_axis
---

在这篇指南中我们将探索应用于弹性（flex）元素的三个属性，它们能使我们在主轴方向上控制弹性元素的尺寸和伸缩性——{{cssxref("flex-grow")}}、{{cssxref("flex-shrink")}} 和 {{cssxref("flex-basis")}}。充分了解这些属性如何与伸张和缩小的元素一起工作是掌握弹性盒子（flexbox）的关键所在。

## 预览

这三个属性控制弹性元素的以下几个方面的灵活性：

- `flex-grow`：该元素获得（伸张）多少正可用空间（positive free space）？
- `flex-shrink`：该元素要消除（收缩）多少负可用空间（negative free space）？
- `flex-basis`：在该元素未伸张和收缩之前，它的大小是多少？

以上属性通常用简写属性 {{cssxref("flex")}} 表达。下面的代码设置 `flex-grow` 属性值为 `2`、`flex-shrink` 属性值为 `1`、`flex-basis` 属性值为 `auto`。

```css
.item {
  flex: 2 1 auto;
}
```

如果你阅读过文章[弹性盒子的基本概念](/zh-CN/docs/Web/CSS/CSS_flexible_box_layout/Basic_concepts_of_flexbox)，那么你应该已经对这些属性有了初步了解。为了让你能明白当你在用它们的时候浏览器在做什么，我们将进一步探讨它们。

## 工作于主轴的重要概念

在考虑 flex 属性如何在主轴方向上控制比率之前，有一些概念值得我们去深究。这涉及到弹性元素在任何伸缩之前的自然尺寸，以及可用空间（free space）的概念

### 弹性元素的尺寸

为了计算出有多少可用空间能用于放置弹性元素，浏览器必须知道这个元素有多大。它是如何解决没有应用绝对单位的宽度和高度的弹性元素？

在 CSS 中还有 {{CSSxRef('min-content')}} 和 {{CSSxRef('max-content')}} 这两个概念；这两个关键字可以用来代替[长度单位](/zh-CN/docs/Web/CSS/length)。

例如下面的例子，我们有两段包含一个文本字符串的段落。第一段设置了 `min-content` 的宽度。在支持这个关键字的浏览器你可以看见文本已尽可能抓住机会来自动换行，使之变得尽可能小且没有溢出。这就是该字符串的 `min-content` 大小。本质上讲，字符串中最长的单词决定了大小。

第二段设置了 `max-content` 值，其与前者相反。它会变得尽可能大，没有自动换行的机会。如果容器太窄，它就会溢出其自身的盒子。

```html live-sample___min-max-content
<p class="min-content">
  I am sized with min-content and so I will take all of the soft-wrapping
  opportunities.
</p>
<p class="max-content">
  I am sized with max-content and so I will take none of the soft-wrapping
  opportunities.
</p>
```

```css live-sample___min-max-content
.min-content {
  width: min-content;
  border: 2px dotted rgb(96 139 168);
}
.max-content {
  width: max-content;
  border: 2px dotted rgb(96 139 168);
}
```

{{EmbedLiveSample("min-max-content", "", "260px")}}

记住这种行为，以及 `min-content` 和 `max-content` 所产生的影响，我们将在后文中探索 `flex-grow` 和 `flex-shrink`。

### 正负可用空间

去谈论这些属性之前我们需要理解**正负可用空间**（positive and negative free space）的概念。当一个弹性容器有正可用空间时，它就有更多的空间用于在容器内显示弹性元素。比如说，如果我们有 500px 宽的容器，{{cssxref("flex-direction")}} 属性值为 `row`，三个 100px 宽的弹性元素，那么我们还有 200px 的正可用空间，如果我们想要填充整个容器，则可将其分配到元素中。

![该图像展示了在元素显示后剩余的空间。](basics7.png)

当弹性元素的自然尺寸加起来比弹性容器内的可用空间大时，我们产生了负可用空间。比如我们有一个像上面那样的 500px 宽的容器，但是三个弹性元素每个都为 200px 宽，那我们就一共需要 600px 宽，因此就有了 100px 的负可用空间。这可以从弹性元素中删除以使其能适应容器。

![元素从容器中溢出](ratios1.png)

我们需要理解正可用空间的分配和负可用空间的移除，这样才能理解 flex 属性。

下面的例子我们会将属性 {{cssxref("flex-direction")}} 设置为 row，因此元素的尺寸仅来自于它们的宽度。我们通过比较所有元素和容器的宽度来计算正负可用空间。你同样可以使用 `flex-direction: column`，这样主轴将变为列，然后你需要比较元素和它们所在容器的高度来计算正负可用空间。

## flex-basis 属性

{{cssxref("flex-basis")}} 属性在任何空间分配发生之前初始化弹性元素的尺寸。此属性的初始值为 `auto`。如果 `flex-basis` 设置为 `auto`，浏览器会先检查元素的主尺寸是否设置了绝对值再计算出它们的初始值。比如说你已经给你的元素设置了 200px 的宽，则 200px 就是这个元素的 `flex-basis`。

如果你的元素为自动调整大小，则 `auto` 会解析为其内容的大小。此时你所熟知的 `min-` 和 `max-content` 大小会变得有用，弹性盒子会将元素的 `max-content` 大小作为 `flex-basis`。下面的实例可以证明这一点。

在这个示例中我们创建了一些固定的盒子，它们的 `flex-grow` 和`flex-shrink` 都设置为 `0`。这里我们看看第一个元素，显式地设置其宽度为 150px，作为主尺寸，即设置 `flex-basis` 为 150px，而另外两个元素没有设置宽度而是根据它们内容的宽度设置尺寸。

```html live-sample___flex-basis
<div class="box">
  <div>One</div>
  <div>Two</div>
  <div>Three</div>
</div>
```

```css live-sample___flex-basis
.box > * {
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
  flex: 0 0 auto;
}

.box {
  width: 500px;
  border: 2px dotted rgb(96 139 168);
  display: flex;
}

.box :first-child {
  width: 150px;
}
```

{{EmbedLiveSample("flex-basis")}}

除了 `auto` 关键字以外，你还可以使用 `content` 关键字作为 `flex-basis` 的值。这会导致即使元素设置了宽度，`flex-basis` 还是会根据内容大小进行设置。你也可以通过设置 flex-basis 为 `auto` 并确保你的元素没有设置宽度来达到相同的效果，以便它能自动调整大小。

空间分配时，如果你想要弹性盒子完全忽略元素的尺寸就需要设置 `flex-basis` 为 `0`。这显式地说明弹性盒子可用抢占所有空间，并按比例进行分配。随着我们继续关注 `flex-grow`，我们将看到这方面的例子。

## flex-grow 属性

{{cssxref("flex-grow")}} 属性指定了**弹性增长因子**（flex grow factor），这决定了在分配正可用空间时，弹性元素相对于弹性容器中的其余弹性元素的增长程度。

如果你所有的元素都设置了相同的 `flex-grow` 属性值，那么空间将会在它们之间平均分配。如果你想要这种情形，通常你需要使用 `1` 作为值，但如果你喜欢你还可以将它们的 `flex-grow` 都设置为 `88`、`100` 或 `1.2`——这只是个比例。如果 flex-grow 的值全部相同，并且在弹性容器中还有正可用空间，那么它就会被平均地分配给所有元素。

### 结合 `flex-grow` 和 `flex-basis`

根据 `flex-grow` 和 `flex-basis` 的相互影响，有些东西会变得迷惑起来。让我们考虑三个不同内容大小的弹性元素的例子，并应用下列 `flex` 规则：

`flex: 1 1 auto;`

这个例子中设置 `flex-basis` 的值为 `auto` 且没有设置它们的宽，因此它们是自动调整大小的。这意味着弹性盒子的大小取决于元素的 `max-content` 大小。在布局完元素之后，弹性容器中还有一些正可用空间，展示在这幅图片的阴影区域中：

![图像使用阴影区域展示了正可用空间](ratios2.png)

我们使用与内容大小相等的 `flex-basis`，以便从总可用空间（弹性容器的宽度）中减去可用分配空间，然后剩余空间在每个元素之间平均分配。我们比较大的元素最终会变得更大，因为它一开始就有一个比较大的尺寸，即使它与其他元素具有相同的分配空间：

![在元素之间分配的正空间](ratios3.png)

如果你真正想要的是三个同样尺寸的元素，即使它们开始具有不同的尺寸，你应该使用这个：

`flex: 1 1 0;`

我们要说的是，为了我们的空间分配，元素的尺寸计算值是 0——所有空间都用来争夺，并且所有元素具有相同的 `flex-grow` 值，它们每个都获得相等的空间分配。最终结果是三个等宽的可伸缩元素。

尝试在这个实例中将 `flex-grow` 的值从 1 更改为 0 以查看不同的行为：

```html live-sample___flex-grow
<div class="box">
  <div>One</div>
  <div>Two</div>
  <div>Three has more content</div>
</div>
```

```css live-sample___flex-grow
.box > * {
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
  flex: 1 1 0;
}

.box {
  width: 400px;
  border: 2px dotted rgb(96 139 168);
  display: flex;
}
```

{{EmbedLiveSample("flex-grow")}}

### 为元素设置不同的 flex-grow 因子

我们对 `flex-grow` 和 `flex-basis` 如何工作的理解使我们能够通过分配不同的 `flex-grow` 值来进一步控制我们单个元素的大小。如果我们设置 `flex-basis` 值为 `0` 则所用空间都可以被分配，我们可以给每个弹性元素分配不同的 `flex-grow` 因子。在下面的示例中，我们会使用下面的值：

- 设置第一个元素的值为 `1`。
- 设置第二个元素的值为 `1`。
- 设置第三个元素的值为 `2`。

`flex-basis` 值为 `0` 意味着可用空间会根据以下规则分配。我们需要将弹性增长因子相加，然后将弹性容器的正可用空间总量除以该值，在这个例子中为 4。而后我们就可以根据每一个的值分配空间——第一个元素得到一个单位、第二个元素得到一个单位、第三个元素得到二个单位。也就是说第三个元素是第一个和第二个元素的两倍。

```html live-sample___flex-grow-ratios
<div class="box">
  <div class="one">One</div>
  <div class="two">Two</div>
  <div class="three">Three</div>
</div>
```

```css live-sample___flex-grow-ratios
.box > * {
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
  flex: 1 1 0;
}

.box {
  border: 2px dotted rgb(96 139 168);
  display: flex;
}

.one {
  flex: 1 1 0;
}

.two {
  flex: 1 1 0;
}

.three {
  flex: 2 1 0;
}
```

{{EmbedLiveSample("flex-grow-ratios")}}

记住你要在这里使用正值。这是一个元素和其他元素之间的比例。你还可以使用更大的数字或者更小的数字——这由你自己决定。把上例中分配的值更改为 `.25`、`.25` 和 `.50` 并去测试——你会得到相同的结果。

## flex-shrink 属性

{{cssxref("flex-shrink")}} 属性指定了**弹性收缩因子**（flex shrink factor），它确定在分配负可用空间时，弹性元素相对于弹性容器中其余弹性元素收缩的程度。

该属性用于处理浏览器计算弹性元素的 `flex-basis` 值，并发现它们太大以至于无法适应弹性容器的情形。只要 `flex-shrink` 有正值，元素就会收缩以至于它们不会溢出容器。

因此 `flex-grow` 用于添加可用空间，而 `flex-shrink` 减少空间来使盒子适应它们的容器而不溢出。

下一个示例中我们的弹性容器有三个元素，我们已经给它们每一个设置了 200px 的宽度，并且设置容器为 500px 宽。设置 `flex-shrink` 为 `0` 的元素不允许收缩，以使它们溢出了盒子。

```html live-sample___flex-shrink
<div class="box">
  <div>One</div>
  <div>Two</div>
  <div>Three has more content</div>
</div>
```

```css live-sample___flex-shrink
.box > * {
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
  flex: 0 0 auto;
  width: 200px;
}

.box {
  width: 500px;
  border: 2px dotted rgb(96 139 168);
  display: flex;
}
```

{{EmbedLiveSample("flex-shrink")}}

改变 `flex-shrink` 值为 `1` 你会发现每个元素都收缩了同样大小的量，现在所有元素都适应盒子。这样做后它们已变得比它们的初始宽度还小。

### 结合 `flex-shrink` 和 `flex-basis`

你可以看见 `flex-shrink` 和 `flex-grow` 工作的一样好。但是它们有两个原因使它们不*完全*一样。

即使它微不足道，但规范中的定义内容是 `flex-shrink` 对于负空间和 `flex-grow` 对于正空间不完全相同的原因：

> “注意：当分配负空间时，弹性收缩因子乘以弹性元素的基本尺寸。这会根据元素能够缩小的比例分配负空间。例如，在较大的元素明显缩小之前，较小的子元素不会缩小到 0。”

第二个原因是在负可用空间消除期间弹性盒子会阻止小的元素缩小到 0。这些元素会以 `min-content` 的大小进行铺设——这个大小是它们利用任何可以利用的自动换行机会后所变成的。

在下面的例子中，在 `flex-basis` 解析为内容大小的位置你会看到 `min-content` 的铺设。如果你改变弹性容器的宽度——比如增加到 700px 宽，再减少弹性元素的宽度，你会看到前两个元素将换行，但是它们绝不会小于 `min-content` 的大小。随着盒子变得越来越小，第三个元素随后从空间中溢出。

```html live-sample___flex-shrink-min-content
<div class="box">
  <div>Item One</div>
  <div>Item Two</div>
  <div>Item Three has more content and so has a larger size</div>
</div>
```

```css live-sample___flex-shrink-min-content
.box > * {
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
  flex: 1 1 auto;
}

.box {
  border: 2px dotted rgb(96 139 168);
  width: 500px;
  display: flex;
}
```

{{EmbedLiveSample("flex-shrink-min-content")}}

在实践中，收缩行为会倾向于给你合理的结果。你通常不希望自己的内容完全消失，或者文本框比自己的最小内容要小，因此上述规则对于需要缩小以适应容器的内容的合理行为而言是有意义的。

### 给元素设置不同的 `flex-shrink` 因子

在 `flex-grow` 值相同的情形下，你可以给弹性元素设置不同的 `flex-shrink` 因子。假如你想让一个元素比它的兄弟元素收缩的更快或更慢，或者不再收缩，这会改变它的默认行为。

在下面的示例中第一个元素设置 `flex-shrink` 的值为 1、第二个为 `0`（因此它将不会收缩）、第三个为 `4`。第三个元素比第一个收缩的更快。任意设置不同的值——你可以给 `flex-grow` 使用小数或者大一点的数。选择对于你来说任意合理的数。

```html live-sample___flex-shrink-ratios
<div class="box">
  <div class="one">One</div>
  <div class="two">Two</div>
  <div class="three">Three</div>
</div>
```

```css live-sample___flex-shrink-ratios
.box > * {
  border: 2px solid rgb(96 139 168);
  border-radius: 5px;
  background-color: rgb(96 139 168 / 0.2);
  width: 200px;
}

.box {
  display: flex;
  width: 500px;
  border: 2px dotted rgb(96 139 168);
}

.one {
  flex: 1 1 auto;
}

.two {
  flex: 1 0 auto;
}

.three {
  flex: 2 4 auto;
}
```

{{EmbedLiveSample("flex-shrink-ratios")}}

## 掌握弹性元素的大小

真正理解弹性元素如何工作的关键是理解有多少东西参与影响弹性元素。思考以下方面，哪些是我们在这些指南中已经讨论过了的：

### 什么设置了元素的基本大小？

1. `flex-basis` 设置为 `auto`，且元素设置了宽度吗？如果设置了，元素的大小将会基于设置的宽度。
2. `flex-basis` 设为了 `auto` 或 `content`（在支持的浏览器中）? 如果设置了，元素的大小为原始大小。
3. `flex-basis` 是不为 `0` 的长度单位吗？如果是这样，那这就是元素的大小。
4. `flex-basis` 设为了 `0`？如果是这样，则元素的大小不在空间分配计算的考虑之内。

### 我们有可用空间吗？

元素没有正可用空间就不会增长，没有负可用空间就不会缩小。

1. 如果我们把所有元素的宽度相加（如果在列方向工作则为高度），总和是否小于容器的总宽度（或高度）？如果是这样，那么你有正可用空间，并且 `flex-grow` 会发挥作用。
2. 如果我们把所有的元素的宽度相加（如果在列方向工作则为高度），总和是否大于容器的总宽度（或高度）？如果是这样，那么你有负可用空间，并且 `flex-shrink` 会发挥作用。

### 分配空间的其他方式

如果我们不想将空间添加到元素中，记住你可以在弹性容器中使用指南中所描述的对准属性来处理元素之间或元素周围的空闲空间。{{cssxref("justify-content")}} 属性能够在元素之间或元素周围分配空闲空间。你还可以在弹性元素上使用自动边距（auto margin）来吸收空间并在元素之间创建间距。

随着所有的弹性工具的使用，你会发现大多数任务都可以实现，尽管在开始时可能需要一些实验。
