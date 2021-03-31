# 如何在CSS中映射的鼠标位置，并实现通过鼠标移动控制页面元素效果

> 映射鼠标位置或实现拖拽效果，我们可以在 `JavaScript` 中做到这一点。但实际上，在CSS中有更加简洁的方法，我们可以在不使用`JavaScript` 的情况下，仍然可以实现相同的功能！

只使用CSS就可以实现模仿鼠标"点击和拖动"效果，让我们来看看如何获得用户的鼠标位置，并将其映射到 `CSS` 自定义属性：`--positionX` 和 `--positionY` 中。下面是具体实现步骤。

## 初始化

我们的第一个 `demo` 将使用 `--positionX` 和 `--positionY` 自定义属性来设置元素的宽度和高度。

```html
<div class="content">
  <div class="square"></div>
</div>  
```

```scss
*, *::before, *::after {
  padding: 0;
  margin: 0 auto;
  box-sizing: border-box;
}
body {
  background-color: black;
  height: 100vh;
}
.content {
  --positionX: 0;
  --positionY: 0;
  position: absolute;
  top: 0; right: 0; bottom: 0; left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}
.square {
  width: 100px;
  height: 100px;
  background: white;
}
```

![10.1](http://127.0.0.1:8080/%23css/images/10.1.png)

这是我们最初的状态。我们这里有一个了名为 `.content` 的容器 `<div>`，它的的宽度和高度填充满 `body`，它是项目的内容主体。类名为 `.square` 的 `<div>` 是在这个例子中我们想用鼠标来进行控制的元素。

我们还在内容中添加了两个自定义属性。我们将使用鼠标位置来设置这些属性的值，然后使用它们来设置 `.square` 元素的宽高。

一旦我们为鼠标位置绘制了自定义属性，我们几乎可以使用它们来做我们想要的任何事情。例如，我们可以使用它们来设置一个绝对定位元素的 `top/left`、控制 `transform` 属性、设置 `background-position`、调整 `color`，甚至设置伪元素的内容等。我们将在文章的结尾看到一些这样的演示效果及对应的`Codepen` 项目链接。

## grid 网格

目标是在屏幕上创建一个不可见的网格，并使用 `:hover` 伪类将每个"单元格"映射成我们自定义属性的一组值。此时，当鼠标光标移动到屏幕的右侧时，`--positionX` 的值将更高：当鼠标向左移动时，它变得更低。对于 `--positionY` 也是一样的：当光标移动到顶部时，值将更低，当光标移动到底部时，值会更高。

> 关于我网格大小及网格分块需要注意的地方：实际上我们可以使任何我们可以达到的网格尺寸。它越大，自定义属性值就越准确。但这也意味着我们将有更多网格分块区间，网格分块过多可能会导致性能问题，根据实际项目保持适当的平衡地调整网格大小是非常重要的。

现在，假如我们需要一个10×10网格，所以总共100个网格分块在我们容器中。（在实际开发中可以使用pug等语法快速创建表格，例子中100个空间全部用 `div` 表示出来了）

```html
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<!-- 97 more cells -->
<div class="content">
  <div class="square"></div>
</div>
```

由于级联关系，将 `.cell` 元素放在 `.content` 元素前面。

我们希望使用 `.cell` 类来控制 `.square`，由于CSS的级联关系，一个元素只能控制其子子元素（或子元素的子元素）及位置在它的后面的兄弟元素（或兄弟元素的子元素）

这意味着两件事：

* 每个 `.cell` 必须先于需要控制的元素（在这个例子中为 `.square`）。
* 不能把这些 `.cell`  放在一个容器里，如果我们这样做`.content` 元素就不再是他们的兄弟元素。

## 定位单元格

有很多方法可以定位 `.cells`。比如我们可以使用 `position: absolute`，并设置它们的 `top` 和 `left` 属性；或者我们也可以通过 `transform` 来转换位置；但最简单的选择其实是使用 `display: grid`。

```css
body {
  background-color: #000;
  height: 100vh;
  display: grid;
  grid-template: repeat(10, 1fr) / repeat(10, 1fr);
}
.cell {
  width: 100%;
  height: 100%;
  border: 1px solid gray;
  z-index: 2;
}
```

`border` 只是暂时的，在开发中所以我们可以看到屏幕上的网格，稍后会删除它。
`z-index` 非常重要，因为我们希望单元格出现在内容顶层。

以下是我们目前完成的内容：

```html
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<!-- 97 more cells -->
<div class="content">
  <div class="square"></div>
</div>
```

```css
*, *::before, *::after {
  padding: 0;
  margin: 0 auto;
  box-sizing: border-box;
}
body {
  background-color: black;
  height: 100vh; 
  display: grid;
  grid-template: repeat(10, 1fr) / repeat(10, 1fr);
}
.cell {
  width: 100%;
  height: 100%;
  border: 1px solid gray;
  z-index: 2;
 }
.content {
  --positionX: 0;
  --positionY: 0;
  position: absolute;
  top: 0; right: 0; bottom: 0; left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}
.square {
  width: 100px;
  height: 100px;
  background: white;
}
```

![10.1](http://127.0.0.1:8080/%23css/images/10.2.png)

## 添加值

我们想通过 `.cell` 来设置 `--positionX` 和 `--positionY` 的值。

当我们 `hover` 悬停在第一个（左列）`.cell`上时，`--positionX` 值应为 `0`。当我们悬停在第二列中的 `.cell` 上时，值应为 `1`。第三 `2` ，等等。

`y轴` 也是如此。当我们悬停在第一行（顶部）`.cell`上时，`--positionY` 应该为 `0` ，当我们悬停在第二行的单元格上时，值应该是 `1`，等等。

![10.1](http://127.0.0.1:8080/%23css/images/10.3.jpg)

从左到右按顺序排列着白色边框和数字的黑色十乘十方格。

图像中的数字表示网格中每个单元格的编号。如果以一个单一的 `.cell` 为例——42号单元格——我们可以使用 `:nth-child()`来选择它：

```css
.cell:nth-child(42) { }
```

但我们需要记住几件事：

* 我们只希望此选择器只在 `hover` 悬停在单元格上时生效，因此我们需要给它附件 `:hover`。
* 我们希望选择的是 `.content` 元素而不是单元格本身，因此需要使用一般兄弟选择器`~` 来做到这一点。

因此，现在当第42个单元格处于 `hover` 状态时，要将 `--positionX` 设置为 `1` 与`--positionY` 设置为 `3`，需要这样做：

```css
.cell:nth-child(42):hover ~ .content {
  --positionX: 1;
  --positionY: 3;
}
```

但是有100个单元格，谁想这样做100次呢！？有几种方法可以使上述操作变得更容易：

1. 使用 `Sass` 中的 `@for` 循环来遍历所有 100 个单元格，并做一些数学运算，每次遍历设置对应的的 `--positionX` 和 `--positionY`值。
2. 将 x 轴和 y 轴分开，用带有`:nth-child` 的功能符号分别选择每行和每列。
3. 结合这两种方法，同时使用`Sass` `@for` 循环和 `:nth-child` 功能符号。

我深思熟虑过什么是最简单最好的方法，虽然所有方法都有优缺点。根据要编写的代码数量、编译代码的质量和数学复杂性方面的考虑，最终我选择了第三种方法。如果你有更好的方法，可以在评论中告诉我。

## 用 `@for` 循环设置值

```css
@for $i from 0 to 10 {
 .cell:nth-child(???):hover ~ .content {
    --positionX: #{$i};
  }
  .cell:nth-child(???):hover ~ .content {
    --positionY: #{$i};
  }
}
```

这是一个基本循环框架，我们需要循环10次，因为上述构造的网格有10行和10列。将网格分为 `x轴`和 `y轴` ，对每列设置 `--positionX`，对每行设置 `--positionY`。现在要做的是找到一个合适的数学表达式，填到 `???` 处，来进行选择每行和每列。

### 让我们从 `x轴` 开始

回到我们上面带有数字的网格图像，我们可以看到 `第2列` 中所有单元格的数字是 `10的倍数加2`。`第2列` 中的单元格是 `10的倍数加3`...

现在，让我们把它转换成 `:nth-child` 的功能表达式。以下是第2列可以表示为：

```css
:nth-child(10n + 2)
```

* 10n表示选择每个10的倍数。
* 2 是列号，在我们的循环中，将用 `#{$i +1]` 替换列号来按顺序重复。

```css
.cell:nth-child(10n + #{$i + 1}):hover ~ .content {
  --positionX: #{$i};
}
```

### 现在让我们处理y轴

再看一遍网格图像，关注 `第4行`，网格编号介于 `41与50` 之间。`第5行` 的网格编号在 `51与60` 之间等等。要选择每行，我们需要定义其范围。例如，第四行的范围是：

```css
.cell:nth-child(n + 41):nth-child(-n + 50)
```

`(n + 41)` 是范围的开始。
`(-n + 50)` 是范围的结尾。
现在，我们用 `$i值` 的来代替数学公式中的数字。对于范围的开始，得到 `(n + #{10 * $i + 1})` ，对于范围结尾获得 `(-n + #{10 * ($i + 1)})`。

因此，最终的 `@for` 循环是：

```css
@for $i from 0 to 10 {
  .cell:nth-child(10n + #{$i + 1}):hover ~ .content {
    --positionX: #{$i};
  }
  .cell:nth-child(n + #{10 * $i + 1}):nth-child(-n + #{10 * ($i + 1)}):hover ~ .content {
    --positionY: #{$i};
  }
}
```

映射完成！当我们悬停在元素上时，`--positionX` 和 `--positionY` 的值会根据鼠标位置而变化。这意味着我们可以使用它们来控制内容中的元素。

## 处理自定义属性

好了，现在我们已经把鼠标位置映射到两个自定义属性，接下来的事情是使用它们来控制 `.square` 元素的宽度和高度值。

让我们从宽度开始，假设我们希望 `.square` 元素的的最小宽度为 `100px`（即当鼠标光标位于屏幕左侧时），我们还希望鼠标光标向右移动的每一步都增长 `20px`。

使用 `calc()`，就可以实现：

```css
.square {
  width: calc(100px + var(--positionX) * 20px);
}
```

对于高度我们做同样的操作，但用 `--positionY` 代替：

```css
.square {
  width: calc(100px + var(--positionX) * 20px);
  height: calc(100px + var(--positionY) * 20px);
}
```

就是这样！现在我们有一个简单的 `.square` 元素，宽度和高度由鼠标位置控制。将鼠标光标在界面移动，查看正方形的宽度和高度如何相应地变化，下面是整个示例的完整代码。

```html
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<!-- 97 more cells -->
<div class="content">
  <div class="square"></div>
</div>
```

```css
*, *::before, *::after {
  padding: 0;
  margin: 0 auto;
  box-sizing: border-box;
}
body {
  background-color: black;
  height: 100vh; 
  display: grid;
  grid-template: repeat(10, 1fr) / repeat(10, 1fr);
}
.cell {
  width: 100%;
  height: 100%;
  // border: 1px solid gray;
  z-index: 2;
}
@for $i from 0 to 10 {
 .cell:nth-child(10n + #{$i + 1}):hover ~ .content {
    --positionX: #{$i};
  }
  .cell:nth-child(n + #{10 * $i + 1}):nth-child(-n + #{10 * ($i + 1)}):hover ~ .content {
    --positionY: #{$i};
  }
}
.content {
  --positionX: 0;
  --positionY: 0;
  position: absolute;
  top: 0; right: 0; bottom: 0; left: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}
.square {
  width: calc(100px + var(--positionX) * 20px);
  height: calc(100px + var(--positionY) * 20px);
  background: white;
  transition: all 0.3s;
}
```

![10.1](http://127.0.0.1:8080/%23css/images/10.4.gif)

我添加了一个小的过渡效果，看起来更顺畅。当然，这不是必须的。我也注释了 `.cell` 元素的的 `border`。

## 让我们尝试一种替代方法

可能会有一种情况，即您想要"绕过" `--positionX` 和 `--positionY`，并将最终值直接设置在 `@for循环` 中。对于我们的例子而言，可以像下面这样实现：

```css
@for $i from 0 to 10 {
 .cell:nth-child(10n + #{$i + 1}):hover ~ .content {
    --squareWidth: #{100 + $i * 20}px;
  }
  .cell:nth-child(n + #{10 * $i + 1}):nth-child(-n + #{10 * ($i + 1)}):hover ~ .content {
    --squareHeight: #{100 + $i * 20}px;: #{$i};
  }
}
```

`.square` 元素从而可以这样自定义属性：

```css
.square {
  width: var(--squareWidth);
  height: var(--squareHeight);
}
```

这种方法相比较而言更灵活一些，因为它允许更高级的 `Sass` 数学（和字符串）函数，但它的主要原理与我们示例的内容是完全相同的。

## 接下来呢？

好吧，剩下的就由你决定如何使用——而且可能性是无穷无尽的！你能在 `CSS` 中更进一步地使用映射鼠标位置地技巧吗？下面是几个页面图形会随着鼠标变换的例子：

* [跳动粒子](https://codepen.io/amit_sheen/pen/c402584728a49c435fefa388e1692687)
![10.1](http://127.0.0.1:8080/%23css/images/10.5.gif)
* [3D文字](https://codepen.io/amit_sheen/pen/261df6f09d15a179b63454bb75acc191)
![10.1](http://127.0.0.1:8080/%23css/images/10.6.gif)
* [透视图像](https://codepen.io/amit_sheen/pen/7ec61599091d3be99d503634b1b7e884)
![10.1](http://127.0.0.1:8080/%23css/images/10.7.gif)
* [打字机效果](https://codepen.io/amit_sheen/pen/af022e80f75789c6b80a8d0eb718f890)
![10.1](http://127.0.0.1:8080/%23css/images/10.8.gif)
