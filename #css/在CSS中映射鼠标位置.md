# 如何在CSS中映射的鼠标位置

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

There are a few ways to position the .cells. we can position: absolute them and offset them with the top and left properties. Or we can translate them into position using transform. But the easiest option is likely using display: grid.
有几种方法可以定位。细胞。我们可以定位：绝对的，并抵消他们的顶部和左属性。或者我们可以通过转换将它们转换为位置。但最简单的选择可能是使用显示屏：网格。

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

The border is just temporary, so we could see the cells on the screen. we’ll remove it later on.
the z-index is important, because we want the cells to be in front of the content.
Here’s what we have so far:
边界只是暂时的，所以我们可以看到屏幕上的细胞。我们稍后会删除它。
z-索引很重要，因为我们希望细胞在内容前面。
以下是我们目前拥有的内容：

html
```html
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>

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

## Adding values

We want to use .cell to set the --positionX and --positionY values.

When we hover over a .cell that is in the first (left) column, the value of --positionX should be 0. When we hover over a .cell in the second column, the value should be 1. It should be 2 in the third column, and so on.

The same goes for the y-axis. When we hover over a .cell that is in the first (top) row, --positionY should be 0, and when we hover over a cell in the second row, the value should be 1, and so on.

我们要使用。细胞来设置位置X和位置值。

当我们悬停在第一个（左）列中的。单元格上时，位置X值应为0。当我们悬停在第二列中的。单元格上时，值应为 1。第三栏应该是 2 ，等等。

y 轴也是如此。当我们悬停在第一行（顶部）的。cell上时，位置Y应该是0，当我们悬停在第二行的单元格上时，值应该是1，等等。

![10.1](http://127.0.0.1:8080/%23css/images/10.3.jpg)

A black ten by ten grid of squares with white borders and numbers in sequential order from left to right.
The numbers in this image represent the numbers of the cell elements in the grid. If we take just a single .cell as an example — let’s say cell 42 — we can select it using :nth-child():
从左到右按顺序排列着白色边框和数字的黑色十乘十方格。
此图像中的数字表示网格中单元格元素的数字。如果我们只以一个单一的。细胞为例——比如说细胞42——我们可以使用：nth-儿童（）：

```css
.cell:nth-child(42) { }
```

But we need to remember a couple of things:

We only want this selector to work when we hover over the cell, so we will attach :hover to it.
We want to select the .content instead of the cell itself, so we’ll use the General Sibling Combinator (~) to do that.
So now, to set --positionX to 1 and --positionY to 3 for .content when the 42nd cell is hovered, we need to do something like this:
但我们需要记住几件事：

我们只希望此选择器在悬停在单元格上时工作，因此我们将附加：悬停到它上。
我们希望选择。内容而不是单元格本身，因此我们将使用一般兄弟姐妹组合器（~）来做到这一点。
因此，现在，当第42个单元格处于悬停状态时，要将位置X设置为1和位置Y到3，我们需要做这样的事情：

```css
.cell:nth-child(42):hover ~ .content {
  --positionX: 1;
  --positionY: 3;
}
```

But who wants to do that 100 times!? There are a few approaches to make things easier:
但谁想这样做100次！？有几种方法可以使事情变得更容易：

Use a Sass @for loop to go through all 100 cells, and do some math to set the proper --positionX and --positionY values for each iteration.
使用 Sass @for循环来浏览所有 100 个单元格，并做一些数学运算，为每次迭代设置正确的位置 X 和位置值。

Separate the x- and y-axes to select each row and each column individually with :nth-child’s functional notation.
Combine those two approaches and use a Sass @for loop and :nth-child functional notation.
将 x 轴和 y 轴分开，分别选择每行和每列，并带有：nth-child 的功能符号。
结合这两种方法，并使用萨斯@for循环和：nth-儿童功能符号。
I thought long and hard about what would be the best and easiest approach to take, and while all of then have pros and cons, I landed on the third approach. The amount of code to write, the quality of the compiled code, and the math complexity all went into my thinking. Don’t agree? Tell my why in the comments!
我漫长而努力地思考什么是最好的和最简单的方法，虽然所有然后有利弊，我降落在第三种方法。要编写的代码数量、编译代码的质量和数学复杂性都进入了我的思维。不同意？告诉我为什么在评论！

## Setting values with a @for loop
用@for循环设置值
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
This is the basic loop. We’re going over it 10 times since we have 10 rows and 10 columns. And we’ve separated the x- and y-axes to set --positionX for each column, and the --positionY for each row. All we need to do now is to replace those ??? things with the proper notation to select each row and column.
这是基本循环。我们超过它10次，因为我们有10行和10列。我们将每个列的 x 轴和 y 轴和每行的位置/位置分开。我们现在要做的就是取代那些???具有正确符号以选择每个行和列的东西。

### Let’s start with the x-axis
Going back to our grid image (the one with numbers),We can see that the numbers of all the cells in the second column are multiples of 10, plus 2. The cells in the third column are multiples of 10 plus 3. And so on.
回到我们的网格图像（带有数字的图像），我们可以看到第二列中所有单元格的数字是 10 倍的倍数，外加 2 倍。第三列中的单元格是 10 加 3 的倍数。等等。

Now let’s ‘translate’ it into the :nth-child functional notation. Here’s how that looks for the second column:
现在，让我们把它"翻译"成：nth儿童功能符号。以下是第二栏的外观：

```css
:nth-child(10n + 2)
```

10n selects every multiple of 10.
2 is the column number.
And for our loop, we will replace the column number with #{$i + 1} to iterate sequentially:
* 10n选择10个中的每个倍数。
* 2 是列号。
对于我们的循环，我们将用 #{$i +1] 替换列号，以按顺序重复：

```css
.cell:nth-child(10n + #{$i + 1}):hover ~ .content {
  --positionX: #{$i};
}
```

Now lets deal with the y-axis
Look again at the grid image and focus on the fourth row. The cell numbers are somewhere between 41 and 50. The cells in the fifth row are between 51 to 60, and so on. To select each row, we’ll need to define its range. For example, the range for the fourth row is:
现在让我们处理y轴
再看一遍网格图像，关注第四行。细胞数介于41到50之间。第五排的细胞在51到60之间，等等。要选择每行，我们需要定义其范围。例如，第四行的范围是：

```css
.cell:nth-child(n + 41):nth-child(-n + 50)
```
(n + 41) is the start of the range.
(-n + 50) is the end of the range.
Now we’ll replace the numbers with some math on the $i value. For the start of the range, we get (n + #{10 * $i + 1}), and for the end of the range we get (-n + #{10 * ($i + 1)}).
（n+41）是范围的开始。
（-n+50）是范围的末尾。
现在，我们将用一些关于$i值的数学来代替数字。对于范围的开始，我们得到（n + #+10* $i + 1}），并在范围结束时获得（-n + #{10* （$i + 1}）。

So the final @for loop is:
因此，最终@for循环是：

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
The mapping is done! When we hover over elements, --positionX and --positionY change according to the mouse position. That means we can use them to control the elements inside the .content.
映射完成！当我们悬停在元素上时，--位置X和--位置会根据鼠标位置而变化。这意味着我们可以使用它们来控制。内容中的元素。

## Handling the custom properties
OK, so now we have the mouse position mapped to two custom-properties, The next thing is to use them to control the .square element’s width and height values.
处理自定义属性
好了，现在我们把鼠标位置映射到两个自定义属性，接下来的事情是使用它们来控制 。平方元素的宽度和高度值。

Let’s start with the width and say that we want the minimum width of the .square to be 100px (i.e. when the mouse cursor is at the left side of the screen), and we want it to grow 20px for each step the mouse cursor moves to the right.
让我们从宽度开始，说我们希望 。平方的最低宽度为 100px（即当鼠标光标位于屏幕左侧时），我们希望鼠标光标向右移动的每一步都增长 20px。

Using calc(), we can do that:
使用计算（），我们可以做到这一点：

```css
.square {
  width: calc(100px + var(--positionX) * 20px);
}
```
And of course, we’ll do the same for the height, but with --positionY instead:
当然，我们会做同样的高度，但与-位置Y代替：

```css
.square {
  width: calc(100px + var(--positionX) * 20px);
  height: calc(100px + var(--positionY) * 20px);
}
```
That’s it! Now we have a simple .square element, with a width and height that is controlled by the mouse position. Move your mouse cursor over the window, and see how the square changes its width and height accordingly.
就是这样！现在我们有一个简单的。平方元件，宽度和高度由鼠标位置控制。将鼠标光标移过窗户，查看正方形的宽度和高度如何相应地变化。

html
```html
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>
<div class="cell"></div>

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


I added a small transition to make the effect smoother. That’s not required, of course. And I’ve also commented out on the .cell border.
我添加了一个小的过渡，使效果更顺畅。当然，这不是必须的。我也评论过.cell边界。

## Let’s try an alternative method
There might be a case where you want to ‘bypass’ --positionX and --positionY, and set the end value directly inside the @for loop. So, for our example it would look like this:
让我们尝试一种替代方法
可能会有一种情况，即您想要"绕过"位置X和位置Y，并将最终值直接设置在@for循环中。因此，就我们的例子而言，它看起来像这样：

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
Then the .square would use the custom properties like this:
然后。平方将使用这样的自定义属性：
```css
.square {
  width: var(--squareWidth);
  height: var(--squareHeight);
}
```
This method is a little more flexible because it allows for more advanced Sass math (and string) functions. That said, the general principle is absolutely the same as what we already covered.
此方法更灵活一点，因为它允许更高级的萨斯数学（和字符串）功能。这就是说，一般原则与我们已经涵盖的内容完全相同。

## What’s next?
Well, the rest is up to you — and the possibilities are endless! How do you think you’d use it? Can you take it further? Try using this trick in your CSS, and share your work in the comments or let me know about it on Twitter. It will be great to see a collection of these come together.
接下来呢？
好吧，剩下的就由你决定——而且可能性是无穷无尽的！你觉得你会怎么用？你能更进一步吗？尝试在CSS中使用此技巧，并在评论中分享您的工作，或在Twitter上告知我有关此技巧。这将是伟大的，看到这些集合走到一起。

Here are a few examples to get your hamster wheel turning:
下面是几个例子，让你的仓鼠轮转动：
https://codepen.io/amit_sheen/pen/c402584728a49c435fefa388e1692687
![10.1](http://127.0.0.1:8080/%23css/images/10.5.gif)
https://codepen.io/amit_sheen/pen/261df6f09d15a179b63454bb75acc191
![10.1](http://127.0.0.1:8080/%23css/images/10.6.gif)
https://codepen.io/amit_sheen/pen/7ec61599091d3be99d503634b1b7e884
![10.1](http://127.0.0.1:8080/%23css/images/10.7.gif)
https://codepen.io/amit_sheen/pen/af022e80f75789c6b80a8d0eb718f890
![10.1](http://127.0.0.1:8080/%23css/images/10.8.gif)
