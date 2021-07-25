---
ctitle: css training
date: 2021-04-30 09:02:36
tags:
- css
- 前端
categories:
- 工程开发
---

只 能 多 写！

冲了朋友们！

<!---more-->

## 基础知识

### 使用

- 外链`<link rel="stylesheet" href="./style.css">`
- 嵌入`<style>...<style/>`
- 内联`<p style="margin:1em 0">`

### 层叠、继承与优先级

> CSS(***Cascading Style Sheets***)，层叠样式表，理解层叠很重要

**层叠**

当应用两条**同级别**的规则到一个元素的时候，写在**后面**的就是实际使用的规则。

**优先级：**

越具体的选择器优先级越高，**不会覆盖所有规则，只覆盖相同的属性。**

优先级计算：**四位数方法**

1. **千位**： 如果声明在 [`style`](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Global_attributes#attr-style) 的属性（内联样式）则该位得一分。这样的声明没有选择器，所以它得分总是1000。
2. **百位**： 选择器中包含ID选择器则该位得一分。
3. **十位**： 选择器中包含class选择器、attr选择器或者伪类则该位得一分。
4. **个位**：选择器中包含元素、伪元素选择器则该位得一分。

> **注**: 通用选择器 (`*`)，组合符 (`+`, `>`, `~`, ' ')，和否定伪类 (`:not`) 不会影响优先级。

> **警告:** 在进行计算时不允许进行进位，例如，20 个类选择器仅仅意味着 20 个十位，而不能视为 两个百位，也就是说，无论多少个类选择器的权重叠加，都不会超过一个 ID 选择器。

> **注**： 覆盖 `!important` 唯一的办法就是另一个 `!important` 具有 相同*优先级* 而且顺序靠后，或者更高优先级。

举个例子：

| 选择器                                    | 千位 | 百位 | 十位 | 个位 | 优先级 |
| :---------------------------------------- | :--- | :--- | :--- | :--- | :----- |
| `h1`                                      | 0    | 0    | 0    | 1    | 0001   |
| `h1 + p::first-letter`                    | 0    | 0    | 0    | 3    | 0003   |
| `li > a[href*="en-US"] > .inline-warning` | 0    | 0    | 2    | 2    | 0022   |
| `#identifier`                             | 0    | 1    | 0    | 0    | 0100   |
| 内联样式                                  | 1    | 0    | 0    | 0    | 1000   |

**继承**

能继承的属性：`color`、`font-family`等

不能继承的：`width`/`margins`/`padding`/`borders`等跟块有关的属性

> 如果borders可以被继承，每个列表和列表项都会获得一个边框 — 可能就不是我们想要的结果!
>
> 哪些属性属于默认继承很大程度上是由常识决定的。

**继承的控制**

在某个样式后面加上

四个通用属性：

- `inherit`：默认属性，（当前元素）继承父元素属性
- `initial`：设置属性值为浏览器默认样式
- `unset`：如果CSS关键字 **`unset`** 从其父级继承，则将该属性重新设置为继承的值，如果没有继承父级样式，则将该属性重新设置为初始值。（相当于重置继承）
- `revert`：

撤销对所有样式继承属性的修改：

`all:unset`

### 选择器

- 类型、类、ID选择器

```css
h1 {}
.box {}
#unique {}
```

- 标签属性选择器（根据一个元素上**是否存在**某个属性或属性值来选择）

```css
a[title] {}
a[href="https://example.com"] {}
[attr~=value]  // 至少有一个值为 value
[attr|=value] // 表示带有以 attr 命名的属性的元素，属性值为“value”或是以“value-”为前缀（"-"为连字符，Unicode 编码为 U+002D）开头。典型的应用场景是用来匹配语言简写代码（如 zh-CN，zh-TW 可以用 zh 作为 value）。
[attr^=value]  //  表示带有以 attr 命名的属性，且属性值是以 value 开头的元素。
[attr$=value] // 表示带有以 attr 命名的属性，且属性值是以 value 结尾的元素。
[attr*=value] // 表示带有以 attr 命名的属性，且属性值至少包含一个 value 值的元素。
[attr operator value i] // 忽略大小写
```

- 伪类、伪元素

伪类(动态伪类)用来样式化一个元素的特定状态，例如鼠标悬浮

```css
a:hover {}
```

伪元素(结构性伪类)用来选择一个元素的某个部分，而不是元素自己。例如，`::first-line`是会选择一个元素（下面的情况中是`<p>`）中的第一行，类似`<span>`包在了第一个被格式化的行外面，然后选择这个`<span>`。

```css
p::first-line { }
```

- 运算符

可以将其他选择器组合起来，更复杂的选择元素。

```css
article > p { } // 子代选择器
article p { } // 后代选择器
h1 + p { } // 相邻xd选择题
h1 ~ p { } // 通用xd选择器
```

### 盒模型

- 块级盒子（block box）
- 内联盒子（inline box）

通过对盒子`display`属性的设置，比如 `inline` 或者 `block` ，来控制盒子的外部显示类型。

#### 块级盒子

一个被定义成块级的（block）盒子会表现出以下行为:

- 盒子会在**内联的方向上**扩展并占据父容器在该方向上的**所有**可用空间，在绝大数情况下意味着盒子会和父容器一样宽
- 每个盒子都会**换行**
- [`width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/width) 和 [`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height) 属性可以发挥作用
- 内边距（padding）, 外边距（margin） 和 边框（border） 会将其他元素从当前盒子周围“推开”

除非特殊指定，诸如标题(`<h1>`等)和段落(`<p>`)默认情况下都是块级的盒子。

#### 内联盒子

如果一个盒子对外显示为 `inline`，那么他的行为如下:

- 盒子不会产生换行。
-  [`width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/width) 和 [`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height) 属性将不起作用。
- 垂直方向的内边距、外边距以及边框会被应用但是不会把其他处于 `inline` 状态的盒子推开。
- 水平方向的内边距、外边距以及边框会被应用且会把其他处于 `inline` 状态的盒子推开。

用做链接的 `<a>` 元素、 `<span>`、 `<em>` 以及 `<strong>` 都是默认处于 `inline` 状态的。

#### 内部显示类型

上面两个都是盒子的外部显示类型（块级还是内敛），称为**正常文档流**

还可以有内部显示类型，使用`display`控制，它决定了盒子**内部元素**是如何布局的，例如`display:flex/grid`，说明这个盒子所有的**子元素**都将成为`flex`元素，但默认的外部显示类型仍然是`block`，`inline-flex`则可以把子元素的显示类型都设成`inline`

display有一个特殊的值`inline-block`，它在内联和块之间提供了一个中间状态。当**不希望一个项切换到新行，但希望它可以设定宽度和高度**。

#### 两种盒模型

标准盒模型：width、height不包含padding和border

IE和模型：包含padding和border

#### margin collapsing的三种情况：

**同一层相邻元素之间**（兄弟块）

相邻的两个元素之间的外边距重叠，除非后一个元素加上[clear-fix清除浮动](https://developer.mozilla.org/zh-CN/docs/Web/CSS/clear)。

**没有内容将父元素和后代元素分开**（父子块）

  如果没有边框`border`，内边距`padding`，行内内容，也没有创建块级格式上下文或清除浮动来**分开**一个块级元素的上边界[`margin-top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-top) 与其内一个或多个后代块级元素的上边界[`margin-top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-top)；或没有边框，内边距，行内内容，高度[`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height)，最小高度[`min-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/min-height)或 最大高度[`max-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/max-height) 来**分开**一个块级元素的下边界[`margin-bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-bottom)与其内的一个或多个后代后代块元素的下边界[`margin-bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-bottom)，则就会出现**父块元素和其内后代块元素**外边界重叠，重叠部分最终会溢出到父级块元素外面。

**空的块级元素**（自身折叠）

当一个块元素上边界[`margin-top`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-top) 直接贴到元素下边界[`margin-bottom`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/margin-bottom)时也会发生边界折叠。这种情况会发生在一个块元素完全没有设定边框[`border`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border)、内边距[`padding`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/padding)、高度[`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height)、最小高度[`min-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/min-height) 、最大高度[`max-height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/max-height) 、内容设定为inline或是加上[clear-fix](https://developer.mozilla.org/zh-CN/docs/Web/CSS/clear)的时候。

> 注意有设定[float](https://developer.mozilla.org/zh-CN/docs/Web/CSS/float)和[position=absolute](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position#absolute)的元素不会产生外边距重叠行为。

### 块级格式上下文BFC

BFC是Web页面的可视CSS渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。

#### 创建BFC

- 根元素（`<html>）`
- 浮动元素（元素的 [`float`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/float) 不是 `none`）
- 绝对定位元素（元素的 [`position`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position) 为 `absolute` 或 `fixed`）
- 行内块元素（元素的 [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `inline-block`）
- 表格单元格（元素的 [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `table-cell`，HTML表格单元格默认为该值）
- 表格标题（元素的 [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `table-caption`，HTML表格标题默认为该值）
- 匿名表格单元格元素（元素的 [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `table、``table-row`、 `table-row-group、``table-header-group、``table-footer-group`（分别是HTML table、row、tbody、thead、tfoot 的默认属性）或 `inline-table`）
- [`overflow`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/overflow) 计算值(Computed)不为 `visible` 的块元素
- [`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 值为 `flow-root` 的元素（可以创建无副作用的BFC）
- [`contain`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/contain) 值为 `layout`、`content `或 paint 的元素
- 弹性元素（[`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `flex` 或 `inline-flex `元素的直接子元素）
- 网格元素（[`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display) 为 `grid` 或 `inline-grid` 元素的直接子元素）
- 多列容器（元素的 [`column-count`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/column-count) 或 [`column-width` (en-US)](https://developer.mozilla.org/en-US/docs/Web/CSS/column-width) 不为 `auto，包括 ``column-count` 为 `1`）
- `column-span` 为 `all` 的元素始终会创建一个新的BFC，即使该元素没有包裹在一个多列容器中（[标准变更](https://github.com/w3c/csswg-drafts/commit/a8634b96900279916bd6c505fda88dda71d8ec51)，[Chrome bug](https://bugs.chromium.org/p/chromium/issues/detail?id=709362)）。

块格式化上下文对浮动定位（参见 [`float`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/float)）与清除浮动（参见 [`clear`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/clear)）都很重要。浮动定位和清除浮动时只会应用于同一个BFC内的元素。浮动不会影响其它BFC中元素的布局，而清除浮动只能清除同一BFC中在它前面的元素的浮动。外边距折叠（[Margin collapsing](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)）也只会发生在属于同一BFC的块级元素之间

### 文字

英文字体放在英文字体前

使用webFonts：

```css
@font-face{
 font-family:'Megrim';
 src:url("....ttf") format("truetype")
}
h1{
    font-family: Megrim;
    font-size: 20px;
}
```

另外一种添加字体的方法：

```html
<link href='https://fonts.googleapis.com/css?family=Rock+Salt' rel='stylesheet' type='text/css'>
```

**设置size的三种方式：**

- 关键字(small, medium, large)
- 长度(px, em)
- 百分数

em是相对于父级文字的大小

**设置font-weight：**

支持100-900的粗细，normal=400，bold=700

**统一设置font：**

`font: style weight size/height family`

### 长度

绝对长度单位：

| 单位 | 名称         | 等价换算            |
| :--- | :----------- | :------------------ |
| `cm` | 厘米         | 1cm = 96px/2.54     |
| `mm` | 毫米         | 1mm = 1/10th of 1cm |
| `Q`  | 四分之一毫米 | 1Q = 1/40th of 1cm  |
| `in` | 英寸         | 1in = 2.54cm = 96px |
| `pc` | 十二点活字   | 1pc = 1/16th of 1in |
| `pt` | 点           | 1pt = 1/72th of 1in |
| `px` | 像素         | 1px = 1/96th of 1in |

相对长度单位：

| 单位   | 相对于                                                       |
| :----- | :----------------------------------------------------------- |
| `em`   | 在 font-size 中使用是相对于父元素的字体大小，在其他属性中使用是相对于自身的字体大小，如 width |
| `ex`   | 字符“x”的高度                                                |
| `ch`   | 数字“0”的宽度                                                |
| `rem`  | 根元素的字体大小                                             |
| `lh`   | 元素的line-height                                            |
| `vw`   | 视窗宽度的1%                                                 |
| `vh`   | 视窗高度的1%                                                 |
| `vmin` | 视窗较小尺寸的1%                                             |
| `vmax` | 视图大尺寸的1%                                               |

#### em与rem

em和百分比类似，且可以被继承，通常用于文字的缩放

rem是css3新增的相对单位，相对于根元素的字体大小

#### 原始尺寸

在受CSS设置影响之前，HTML元素有其原始的尺寸。一个直观的例子就是图像。一副图像的长和宽由这个图像文件自身确定。这个尺寸就是固有尺寸。

#### min-和max-尺寸

[`max-width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/max-width)的常见用法为，在没有足够空间以原有宽度展示图像时，让图像缩小，同时确保它们不会比这一宽度大。如果你使用了`max-width: 100%`，那么图像可以变得比原始尺寸更小，但是不会大于原始尺寸的100%。

例子：第一次使用，属性值已设为`width: 100%`，位于比图片大的容器里，因此图片拉伸到了与容器相同的宽度；第二次的属性值则设为`max-width: 100%`，因此它并没有拉伸到充满容器；第三个盒子再一次包含了相同的图片，同时设定了`max-width: 100%`属性，这时你能看到它是怎样缩小来和盒子大小相适应的。

![image-20210717211548506](https://ericblog.oss-cn-beijing.aliyuncs.com/img/image-20210717211548506.png)

```css
.box {
  width: 200px;
}
.minibox {
  width: 50px;
}
.width {
  width: 100%;
}
.max {
  max-width: 100%;
}
```

```html
<div class="wrapper">
  <div class="box"><img src="star.png" alt="star" class="width"></div>
  <div class="box"><img src="star.png" alt="star" class="max"></div>
  <div class="minibox"><img src="star.png" alt="star" class="max"></div>
</div>
```

这个技术是用来让图片**可响应**的，所以在更小的设备上浏览的时候，它们会合适地缩放。你无论怎样都不应该用这个技术先载入大原始尺寸的图片，再对它们在浏览器中进行缩放。

### 大小

```css
object-fit: cover/fill/contain
```

分别代表：按小边充满/拉伸充满/按长边充满

### 通用表单

```css
button,
input,
select,
textarea {
  font-family: inherit;
  font-size: 100%;
  box-sizing: border-box;
  padding: 0; margin: 0;
}

textarea {
  overflow: auto;
} 
```

防止跨浏览器的form元素对于不同的挂件使用不同的盒子约束规则；

在`<textarea>`上设置`overflow: auto` 以避免IE在不需要滚动条的时候显示滚动条；

### 自定义属性

**自定义属性**（有时候也被称作**CSS变量**或者**级联变量**）是由CSS作者定义的，它包含的值可以在整个文档中重复使用。由自定义属性标记设定值（比如： **`--main-color: black;`**），由[var() ](https://developer.mozilla.org/zh-CN/docs/Web/CSS/var())函数来获取值（比如： `color: var(--main-color);`）自定义属性受级联的约束，并从其父级继承其值。

通常的最佳实践是定义在根伪类 [`:root`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/:root) 下，这样就可以在HTML文档的任何地方访问到它了：

```css
:root {
  --main-bg-color: brown;
}
```

如前所述，使用一个局部变量时用 [`var()`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/var()) 函数包裹以表示一个合法的属性值：

```css
element {
  background-color: var(--main-bg-color);
}
```

其中var可以提供第二个参数（备用值）且可以嵌套

```css
.three {
  background-color: var(--my-var, var(--my-background, pink)); /* pink if --my-var and --my-background are not defined */
}
```

## 布局

终于到布局啦

### 正常布局流(normal flow)

是指在不对页面进行任何布局控制时，浏览器默认的HTML布局方式。

出现在另一个元素下面的元素被描述为**块**元素，与出现在另一个元素旁边的**内联元素**不同，内联元素就像段落中的单个单词一样。

> 注意：块元素内容的布局方向被描述为**块方向**。块方向在英语等具有水平**书写模式**(`writing mode`)的语言中垂直运行。它可以在任何垂直书写模式的语言中水平运行。对应的**内联方向**是内联内容（如句子）的运行方向。

下列布局技术会覆盖默认的布局行为：

-  **[`display`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/display)** 属性 — 标准的value,比如`block`, `inline` 或者 `inline-block` 元素在正常布局流中的表现形式 (见 [Types of CSS boxes](https://developer.mozilla.org/en-US/docs/Learn/CSS/Introduction_to_CSS/Box_model#Types_of_CSS_boxes)). 接着是全新的布局方式，通过设置`display`的值, 比如 [CSS Grid](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Grids) 和 [Flexbox](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox).
- **浮动**——应用 **[`float`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/float)** 值，诸如 `left` 能够让块级元素互相并排成一行，而不是一个堆叠在另一个上面。
- **[`position`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position)** 属性 — 允许你精准设置盒子中的盒子的位置，正常布局流中，默认为 `static` ，使用其它值会引起元素不同的布局方式，例如将元素固定到浏览器视口的左上角。
- **表格布局**— 表格的布局方式可以用在非表格内容上，可以使用`display: table`和相关属性在非表元素上使用。
- **多列布局**— 这个 [Multi-column layout](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Columns) 属性 可以让块按列布局，比如报纸的内容就是一列一列排布的。

### 弹性盒子 flex

使用场景：`float`和`position`难以完成的任务，例如：

- 父内容里垂直居中一个块内容
- 无论剩多少可用宽度，容器中所有子项占用等量的可用宽度/高度
- 无论内容有多少，让多列布局中所有列采用相同的高度

**设置**：

`display:flex/inline-flex`

默认子项宽度均分，高度相等。

主轴：沿**flex元素**放置方向延申的轴`main start`/`main end`

交叉轴：垂直于主轴的轴`cross start`/`cross end`

`flex-direction: row/column` 默认为`row`，行布局/列布局

`flex-wrap: wrap ` 可以自动换行以防止太多子代超出容器

上两项可以合并成`flex-flow: row wrap` **用于容器**

对于容器中的每个元素可以设置`flex`属性来控制宽度

`flex: 200px`：和`wrap`一起设置可以保证最小宽度

`flex: 2` 无单位，按比例划分

`flex: 2 200px` 每个flex项**先给出**200px的可用空间，**剩余空间**按比例划分

事实上，`flex`属性是三个不同全写属性的缩写形式：

[`flex`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex) 是一个可以指定最多三个不同值的缩写属性：

- 第一个就是上面所讨论过的无单位比例。用于分配**剩余空间**，可以单独指定全写 [`flex-grow`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex-grow) 属性的值。
- 第二个无单位比例 — [`flex-shrink`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex-shrink) — 一般用于**溢出容器**的 flex 项。这指定了**从每个 flex 项中取出多少溢出量**，以阻止它们溢出它们的容器。 
- 第三个是上面讨论的**最小值**。可以单独指定全写 [`flex-basis`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex-basis) 属性的值。

#### 水平和垂直对齐

垂直对齐（交叉轴）：`align-items: stretch/center/start/end`

`align-self` 可以在子元素中单独控制本元素的行为

水平对齐（主轴）：`justify-content: center/start/end/space-between/space-around/space-evenly` 区别：`between` 是左右两端对齐，即左右两侧项目都紧贴容器，且元素之间间距相等；`around` 是项目之间间距为左右两侧项目到容器间距的**2倍**，比较特别的布局，日常使用不太多；`evenly` 是项目之间间距与项目与容器间距相等；

#### 排序

弹性盒子也有可以改变 flex 项的布局位置的功能，而不会影响到源顺序（即 dom 树里元素的顺序）。这也是传统布局方式很难做到的一点。

```css
button:first-child {
  order: 1;
}
```

- 所有 flex 项默认的 [`order`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/order) 值是 0。
- order 值大的 flex 项比 order 值小的在显示顺序中更靠后。
- 相同 order 值的 flex 项按原顺序显示。所以假如你有四个元素，其 order 值分别是2，1，1和0，那么它们的显示顺序就分别是第四，第二，第三，和第一

### 网格布局

> 网格是由一系列水平及垂直的线构成的以一种布局模式。根据网格，我们能够将设计元素进行排列，帮助我们设计一系列具有固定位置以及宽度的元素的页面，使我们的网站页面更加统一。
>
> 一个网格通常具有许多的**列（column）**与**行（row）**，以及行与行、列与列之间的间隙，这个间隙一般被称为**沟槽（gutter）**。

**设置**：

```css
.container {
    display: grid;
}
```

这样只创建了一个只有一列的网格为了让我们的容器看起来更像一个网格，我们要给刚定义的网格加一些列。那就让我们加三个宽度为`200px`的列。当然，这里可以用任何长度单位，包括百分比。

```css
.container {
    display: grid;
    grid-template-columns: 200px 200px 200px;
}
```

可以使用fr调整每列的比例

```css
.container {
    display: grid;
    grid-template-columns: 2fr 1fr 1fr;
}
```

使用`grid-column-gap`调整列间隙

使用`grid-row-gap`调整行间隙

使用`grid-gap`同时设定二者

```css
.container {
    display: grid;
    grid-template-columns: 2fr 1fr 1fr;
    grid-gap: 20px;
}
```

- 可以使用`repeat`重复构建行和列：`repeat(repeatNumber,value)`

`repeatNumber`可以使用`auto-fill`和`auto-fit`关键词来代替固定的重复次数

- 可以使用`minmax()` 函数设置最小值，例如`minmax(100, auto)`代表尺寸至少为100像素，并且如果内容尺寸大于100像素则会根据内容自动调整

- 可以使用`grid-template-area(s)` 设置网格区域

```css
grid-template-areas: 
            "a a ."
            "a a ."
            ". b c";
```

​	<img src="https://ericblog.oss-cn-beijing.aliyuncs.com/img/image-20210724173716918.png" alt="image-20210724173716918" style="zoom:33%;" />

- 使用`grid-row`/`grid-column`根据网格线设置区域

```css
.grid {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-template-rows: 100px 100px 100px;
  gap: 10px;
}

.item1 {
  grid-column: 1/3;
  grid-row: 1/3;
}

.item2 {
  grid-row: 2/4;
  grid-column: 2/4;
}
```

<img src="https://ericblog.oss-cn-beijing.aliyuncs.com/img/image-20210724173818355.png" alt="image-20210724173818355" style="zoom:33%;" />

`grid-row: span 2/ 3` 距离3号基线向上两条线

- 隐式网格：溢出时的网格；可以通过[`grid-auto-rows`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/grid-auto-rows)和[`grid-auto-columns`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/grid-auto-columns)属性手动设定隐式网格的大小

### 浮动

> 最初，引入 [`float`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/float) 属性是为了能让 web 开发人员实现简单的布局，包括在一列文本中浮动的图像，文字环绕在它的左边或右边。你可能在报纸版面上看到过。浮动曾被用来实现整个网站页面的布局，它使信息列得以横向排列（默认的设定则是按照这些列在源代码中出现的顺序纵向排列）。目前出现了更新更好的页面布局技术，所以使用浮动来进行页面布局应被看作[传统的布局方法。](https://developer.mozilla.org/zh-CN/docs/Learn/CSS/CSS_layout/Legacy_Layout_Methods)

用浮动做的首字下沉：

```css
p {
  width: 400px;
  margin: 0 auto;
}

p::first-line {
  text-transform: uppercase;
}

p::first-letter {
  font-size: 3em;
  border: 1px solid black;
  background: red;
  float: left;
  padding: 2px;
  margin-right: 4px;
}
```

![image-20210724205115574](https://ericblog.oss-cn-beijing.aliyuncs.com/img/image-20210724205115574.png)

#### 多列布局

对于以下的html：

```html
<h1>2 column layout example</h1>
<div>
  <h2>First column</h2>
  <p> ...</p>
</div>
<div>
  <h2>Second column</h2>
  <p>...</p>
</div>
```

我们给出对应的两列布局css
```css
body {
  width: 90%;
  max-width: 900px;
  margin: 0 auto;
}
div:nth-of-type(1) {
  width: 48%;
  float: left;
}
div:nth-of-type(2) {
  width: 48%;
  float: right;
}
```

效果：

![image-20210724205612586](https://ericblog.oss-cn-beijing.aliyuncs.com/img/image-20210724205612586.png)

增加一列，css改为：

```css
body {
  width: 90%;
  max-width: 900px;
  margin: 0 auto;
}

div:nth-of-type(1) {
  width: 36%;
  float: left;
}

div:nth-of-type(2) {
  width: 30%;
  float: left;
  margin-left: 4%;
}

div:nth-of-type(3) {
  width: 26%;
  float: right;
}
```

#### 清除浮动

目的：解决所有在浮动下面的自身不浮动的内容都将围绕浮动元素进行包装的问题

使用clear属性：它主要意味着"此处停止浮动"——这个元素和源码中后面的元素将不浮动，除非您稍后将一个新的`float`声明应用到此后的另一个元素。

```css
footer {
  clear: both;
}
```

[`clear`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/clear) 可以取三个值：

- `left`：停止任何活动的左浮动
- `right`：停止任何活动的右浮动
- `both`：停止任何活动的左右浮动

浮动的元素存在于正常的文档布局流之外，在某些方面的行为相当奇怪：

- 首先，他们在父元素中所占的面积的有效高度为0 
- 其次，非浮动元素的外边距不能用于它们和浮动元素之间来创建空间

问题：给一个浮动元素与正常文档流中间加上margin时不起作用

解决方法：添加一个新的`clearfix`的`div` ，加上`clear:both` 后相当于在正常文档流和浮动元素之间加了个“隔板”，再给浮动元素加`margin`就可以了

## 布局练习：

[40LayoutExercise](https://github.com/byr-gdp/40LayoutExercise)

#### T1

负值margin：

![浅谈margin负值](https://pic4.zhimg.com/v2-a7d813afe7ab2c6c4233146609d00dfa_1440w.jpg?source=172ae18b)

margin参考线的定义如上，显然负值时相当于**向参考线的反方向移动**

