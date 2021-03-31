# 使用SVG滤镜创建图案

> 英文原文链接：<https://css-tricks.com/creating-patterns-with-svg-filters>

多年来，我一直困扰于不能在 `CSS` 中找到一种能够创造出看起来自然的图案的方法，比如，有时候我所需要的只是一个木纹背景图案。我知道唯一可以解决上述问题的生产可用方法就是引入一张外部图片，但是外部图片属于附加的依赖，它们会引入新的复杂性。

现在我知道，这些问题的很大一部分都可以通过几行 `SVG` 来解决。

## `feTurbulence` 滤镜

`SVG` 中有一个称为 `<feTurbulence>` 的原生滤镜。从某种意义上说，它不需要任何输入图像——滤镜本身可以会生成一张图像。它产生所谓的 `柏林噪声（Perlin noise）`，是 `噪声渐变（noise gradient）` 的一种。 `柏林噪声` 在计算机图形生成中被广泛应用于创建各种纹理。`<feTurbulence>` 提供创建多种 `噪音纹理` 及每种类型数百万种变化的选项。

以下纹理都是用`<feTurbulence>` 生成的。

"所以呢？"你可能要问。这些纹理当然很嘈杂，但它们也包含隐藏的图案，我们可以通过将它们与其他过滤器配对来发现这一点！这是本文所要介绍的主要内容。

![11.1](http://127.0.0.1:8080/%23css/images/11.1.png)

## 创建 `SVG` 滤镜

自定义滤镜通常由多个 `滤镜原（filter primitives）` 构成，它们串联在一起以实现预期的效果。在 `SVG` 中，我们可以通过声明 `<filter>` 元素和多个 `<fe{原始名称]}>` 元素的方式来描述这些内容。然后，通过引用滤镜的 `id`，可以在可渲染元素（如 `<rect>`、`<circle>`、`<path>`、`<text>`等）上应用已声明的滤镜。下面的代码显示了一个空滤镜，`识别符（id）` 为 `coolEffect`，应用在一个全宽高的 `<rect>` 元素上。

```html
<svg xmlns="http://www.w3.org/2000/svg">
  <filter id="coolEffect">
    <!-- Filter primitives will be written here -->
  </filter>
  <rect width="100%" height="100%" filter="url(#coolEffect)"/>
</svg>
```

`SVG` 提供了十几个不同的 `滤镜原（filter primitives）`，我们先从相对简单的 `<feFlood>` 开始。就像它的名字 `flood` 一样：它会淹没 `目标区域`（当然也是在不需要引入外部图片的情况下）。

> 从技术上讲，`目标区域` 指的是滤镜原在 `<filter>`区域内的子区域。

`滤镜区域` 和 `滤镜原的子区域` 都可以自定义的。但是，在这篇文章中，我们使用默认值，也就是是我们矩形的全部区域。以下代码通过设置 `flood-color`（红色）和 `flood-opacity`（0.5）属性值，让矩形区变成红色和半透明。

```html
<svg xmlns="http://www.w3.org/2000/svg">
  <filter id="coolEffect">
    <feFlood flood-color="red" flood-opacity="0.5"/>
  </filter>
  <rect width="100%" height="100%" filter="url(#coolEffect)"/>
</svg>
```

![11.2](http://127.0.0.1:8080/%23css/images/11.2.png)

现在让我们来看看 `<feBlend>` 滤镜原。它将用于混合多个输入。我们的输入之一的关键字为 `SourceGraphic`，它表示被应用滤镜的原始图形。

我们原始的图形是一个黑色矩形——因为我们没有指定 `<rect>` 的填充颜色，而默认填充色是黑色。我们的另一个输入是 `<feFlood>` 滤镜原产生的结果。如下图所示，我们已将 `result` 属性添加到 `<feFlood>` 元素以命名其输出。我们通过`in` 属性来应用 `<feBlend>` 的输出，`SourceGraphic` 拥有 `in2` 属性。

使用默认混合模式时，输入顺序很重要。以下代码我们的混合操作是把半透明的红色矩形放在黑色矩形的顶层。

```html
<svg xmlns="http://www.w3.org/2000/svg">
  <filter id="coolEffect">
    <feFlood flood-color="red" flood-opacity="0.5" result="flood"/>
    <feBlend in="flood" in2="SourceGraphic"/>
  </filter>
  <rect width="100%" height="100%" filter="url(#coolEffect)"/>
</svg>
```

![11.2](http://127.0.0.1:8080/%23css/images/11.3.png)

A solid dark red rectangle that is wider what it is tall.
Now, our rectangle is dark red, no matter what color the background is behind it. That’s because we stacked our semi-transparent red <feFlood> on top of the black <rect> and blended the two together with <feBlend>, we chained the result of <feFlood> to <feBlend>.
Chaining filter primitives is a pretty frequent operation and, luckily, it has useful defaults that have been standardized. In our example above, we could have omitted the result attribute in <feFlood> as well as the in attribute in <feBlend>, because any subsequent filter will use the result of the previous filter as its input. We will use this shortcut quite often throughout this article.

Generating random patterns with feTurbulence
<feTurbulence> has a few attributes that determine the noise pattern it produces. Let’s walk through these, one by one.

baseFrequency
This is the most important attribute because it is required in order to create a pattern. It accepts one or two numeric values. Specifying two numbers defines the frequency along the x- and y-axis, respectively. If only one number is provided, then it defines the frequency along both axes. A reasonable interval for the values is between 0.001 and 1, where a low value results in large “features” and a high value results in smaller “features.” The greater the difference between the x and y frequencies, the more “stretched” the pattern becomes.

```html
<svg xmlns="http://www.w3.org/2000/svg">
  <filter id="coolEffect">
    <feTurbulence baseFrequency="0.001 1"/>
  </filter>
  <rect width="100%" height="100%" filter="url(#coolEffect)"/>
</svg>
```

The baseFrequency values in the top row, from left to right: 0.01, 0.1, 1.  Bottom row: 0.01 0.1, 0.1 0.01, 0.001 1.
type
The type attribute takes one of two values: turbulence (the default) or fractalNoise, which is what I typically use. fractalNoise produces the same kind of pattern across the red, green, blue and alpha (RGBA) channels, whereas turbulence in the alpha channel is different from those in RGB. I find it tough to describe the difference, but it’s much easier to see when comparing the visual results.

```html
<svg xmlns="http://www.w3.org/2000/svg">
  <filter id="coolEffect">
    <feTurbulence baseFrequency="0.1" type="fractalNoise"/>
  </filter>
  <rect width="100%" height="100%" filter="url(#coolEffect)"/>
</svg>
```

Two squares side-by-side filled with pastel patterns. The left square is sharper than the right square, which is blurry.
The turbulence type (left) compared to the fractalNoise type (right)
numOctaves
The concept of octaves might be familiar to you from music or physics. A high octave doubles the frequency. And, for <feTurbulence> in SVG, the numOctaves attribute defines the number of octaves to render over the baseFrequency.

The default numOctaves value is 1, which means it renders noise at the base frequency. Any additional octave doubles the frequency and halves the amplitude. The higher this number goes, the less visible its effect will be. Also, more octaves mean more calculation, possibly hurting performance. I typically use values between 1-5 and only use it to refine a pattern.

```html
<svg xmlns="http://www.w3.org/2000/svg">
  <filter id="coolEffect">
    <feTurbulence baseFrequency="0.1" type="fractalNoise" numOctaves="2"/>
  </filter>
  <rect width="100%" height="100%" filter="url(#coolEffect)"/>
</svg>
```

numOctaves values compared: 1 (left), 2 (center), and 5 (right)
seed
The seed attribute creates different instances of noise, and serves as the the starting number for the noise generator, which produces pseudo-random numbers under the hood. If the seed value is defined, a different instance of noise will appear, but with the same qualities. Its default value is 0 and positive integers are interpreted (although 0 and 1 are considered to be the same seed). Floats are truncated.

This attribute is best for adding a unique touch to a pattern. For example, a random seed can be generated on a visit to a page so that every visitor will get a slightly different pattern. A practical interval for generating random seeds is from 0 to 9999999 due to some technical details and single precision floats. But still, that’s 10 million different instances, which hopefully covers most cases.

```html
<svg xmlns="http://www.w3.org/2000/svg">
  <filter id="coolEffect">
    <feTurbulence baseFrequency="0.1" type="fractalNoise" numOctaves="2" seed="7329663"/>
  </filter>
  <rect width="100%" height="100%" filter="url(#coolEffect)"/>
</svg>
```

seed values compared: 1 (left), 2 (center), and 7329663 (right)
stitchTiles
We can tile a pattern the same sort of way we can use background-repeat: repeat in CSS! All we need is the stitchTiles attribute, which accepts one of two keyword values: noStitch and stitch, where noStitch is the default value. stitch repeats the pattern seamlessly along both axes.

Two long rectangles with blurry color patterns stacked one on top of the other. The top rectangle is split into six smaller rectangles, carrying the same pattern. The bottom rectangle is a single pattern.
Comparing noStitch (top) to stitch (bottom)
Note that <feTurbulence> also produces noise in the Alpha channel, meaning the images are semi-transparent, rather than fully opaque.

Patterns Gallery
Let’s look at a bunch of awesome patterns made with SVG filters and figure out how they work!


Starry Sky

This pattern consists of two chained filter effects on a full width and height rectangle. <feTurbulence> is the first filter, responsible for generating noise. <feColorMatrix> is the second filter effect, and it alters the input image, pixel by pixel. We can tell specifically what each output channel value should be based on a constant and all the input channel values within a pixel. The formula per channel looks like this:

O =w_RR_i+w_GG_i+w_BB_i+w_AA_i+w_C
O is the output channel value
R_i, G_i, B_i, A_i are the input channel values
w_R, w_G, w_B, w_A, w_C are the weights
So, for example, we can write a formula for the Red channel that only considers the Green channel by setting w_G to 1, and setting the other weights to 0. We can write similar formulas for the Green and Blue channels that only consider the Blue and Red channels, respectively. For the Alpha channel, we can set w_C (the constant) to 1 and the other weights to 0 to create a fully opaque image. These four formulas perform a hue rotation.

The formulas can also be written as matrix multiplication, which is the origin of the name <feColorMatrix>. Though <feColorMatrix> can be used without understanding matrix operations, we need to keep in mind that our 4×5 matrix are the 4×5 weights of the four formulas.

\begin{bmatrix} w_{RR} & w_{GR} & w_{BR} & w_{AR} & w_{CR}\\ w_{RG} & w_{GG} & w_{BG} & w_{AG} & w_{CG} \\ w_{RB} & w_{GB} & w_{BB} & w_{AB} & w_{CB} \\ w_{RA} & w_{GA} & w_{BA} & w_{AA} & w_{CA} \end{bmatrix}
w_{RR} is the weight of Red channel’s contribution to the Red channel.
w_{RG} is the weight of Red channel’s contribution to the Green channel.
w_{GR} is the weight of Green channel’s contribution to the Red channel.
w_{GG} is the weight of Green channel’s contribution to the Green channel.
The description of the remaining 16 weights are omitted for the sake ofbrevity
The hue rotation mentioned above is written like this:

\begin{bmatrix} 0 & 1 & 0 & 0 & 0 \\ 0 & 0 & 1 & 0 & 0 \\ 1 & 0 & 0 & 0 & 0 \\ 0 & 0 & 0 & 0 & 1 & \end{bmatrix}
It’s important to note that the RGBA values are floats ranging from 0 to 1, inclusive (rather than integers ranging from 0 to 255 as you might expect). The weights can be any float, although at the end of the calculations any result below 0 is clamped to 0, and anything above 1 is clamped to 1. The starry sky pattern relies on this clamping, since it’s matrix is this:

\begin{bmatrix} 0 & 0 & 0 & 9 & -4 \\ 0 & 0 & 0 & 9 & -4 \\ 0 & 0 & 0 & 9 & -4 \\ 0 & 0 & 0 & 0 & 1 \end{bmatrix}

The transfer function described by <feColorMatrix> for the R, G, and B channels. The input is always Alpha.
We are using the same formula for the RGB channels which means we are producing a grayscale image. The formula is multiplying the value from the Alpha channel by nine, then removing four from it. Remember, even Alpha values vary in the output of <feTurbulence>. Most resulting values will not be within the 0 to 1 range; thus they will be clamped. So, our image is mostly either black or white — black being the sky, and white being the brightest stars; the remaining few in-between values are dim stars. We are setting the Alpha channel to a constant of 1 in the fourth row, meaning the image is fully opaque.

Pine Wood

This code is not much different from what we just saw in Starry Sky. It’s really just some noise generation and color matrix transformation. A typical wooden pattern has features that are longer in one dimension than the other. To mimic this effect, we are creating “stretched” noise with <feTurbulence> by setting baseFrequency="0.1 0.01". Furthermore, we are setting type="fractalNoise".

With <feColorMatrix>, we are simply recoloring our longer pattern. And, once again, the Alpha channel is used as an input for variance. This time, however, we are offsetting the RGB channels by constant weights that are greater than the weights applied on the Alpha input. This ensures that all the pixels of the image remain within a certain color range. Finding the best color range requires a little bit of playing around with the values.

\begin{bmatrix} 0 & 0 & 0 & .11 & .69 \\ 0 & 0 & 0 & .09 & .38 \\ 0 & 0 & 0 & .08 & .14 \\ 0 & 0 & 0 & 0 & 1 \end{bmatrix}

Mapping Alpha values to colors with <feColorMatrix> While it’s extremely subtle, the second bar is a gradient.
It’s essential to understand that the matrix operates in the linearized RGB color space by default. The color purple (#800080), for example, is represented by values R=0.216, G=0, and B=0.216. It might look odd at first, but there’s a good reason for using linearized RGB for some transformations. This article provides a good answer for the why, and this article is great for diving into the how.

At the end of the day, all it means is that we need to convert our usual #RRGGBB values to the linearized RGB space. I used this color space tool to do that. Input the RGB values in the first line then use the values from the third line. In the case of our purple example, we would input R=128, G=0, B=128 in the first line and hit the sRGB8 button to get the linearized values in the third line.

If we pick our values right and perform the conversions correctly, we end up with something that resembles the colors of pine wood.

Dalmatian Spots

This example spices things up a bit by introducing <feComponentTransfer> filter. This effect allows us to define custom transfer functions per color channel (also known as color component). We’re only defining one custom transfer function in this demo for the Alpha channel and leave the other channels undefined (which means identity function will be applied). We use the discrete type to set a step function. The steps are described by space-separated numeric values in the tableValues attribute. tableValues control the number of steps and the height of each step.

Let’s consider examples where we play around with the tableValues value. Our goal is to create a “spotty” pattern out of the noise. Here’s what we know:

tableValues="1" transfers each value to 1.
tableValues="0" transfers each value to 0.
tableValues="0 1" transfers values below 0.5 to 0 and values from 0.5 to 1.
tableValues="1 0" transfers values below 0.5 to 1 and values from 0.5 to 0.

Three simple step functions. The third (right) shows what is used in Dalmatian Spots.
It’s worth playing around with this attribute to better understand its capabilities and the quality of our noise. After some experimenting we arrive at tableValues="0 1 0" which translates mid-range values to 1 and others to 0.

The last filter effect in this example is <feColorMatrix> which is used to recolor the pattern. Specifically, it makes the transparent parts (Alpha = 0) black and the opaque parts (Alpha = 1) white.

Finally, we fine-tune the pattern with <feTurbulence>. Setting numOctaves="2" helps make the spots a little more “jagged” and reduces elongated spots. The baseFrequency="0.06" basically sets a zoom level which I think is best for this pattern.

ERDL Camouflage

The ERDL pattern was developed for disguising of military personnel, equipment, and installation. In recent decades, it found its way into clothing. The pattern consists of four colors: a darker green for the backdrop, brown for the shapes shapes, a yellowish-green for patches, and black sprinkled in as little blobs.

Similarly to the Dalmatian Spots example we looked at, we are chaining <feComponentTransfer> to the noise — although this time the discrete functions are defined for the RGB channels.

Imagine that the RGBA channels are four layers of the image. We create blobs in three layers by defining single-step functions. The step starts at different positions for each function, producing a different number of blobs on each layer. The cuts for Red, Green and Blue are 66.67%, 60% and 50%, respectively..

```html
<feFuncR type="discrete" tableValues="0 0 0 0 1 1"/>
<feFuncG type="discrete" tableValues="0 0 0 1 1"/>
<feFuncB type="discrete" tableValues="0 1"/>
```

At this point, the blobs on each layers overlap in some places, resulting colors we don’t want. These other colors make it more difficult to transform our pattern into an ERDL camouflage, so let’s eliminate them:

For Red, we define the identity function.
For Green, our starting point is the identity function but we subtract the Red from it.
For Blue, our starting point is the identity function as well, but we subtract the Red and Green from it.
\begin{bmatrix} 1 & 0 & 0 & 0 & 0 \\ -1 & 1 & 0 & 0 & 0 \\ -1 & -1 & 1 & 0 & 0 \\ 0 & 0 & 0 & 0 & 1 \end{bmatrix}
These rules mean Red remains where Red and Green and/or Blue once overlapped; Green remains where Green and Blue overlapped. The resulting image contains four types of pixels: Red, Green, Blue, or Black.

The second chained <feColorMatrix> recolors everything:

The black parts are made dark green with the constant weights.
The red parts are made black by negating the constant weights.
The green parts are made that yellow-green color by the additional weights from the Green channel.
The blue parts are made brown by the additional weights from the Blue channel.
Island Group

This example is basically a heightmap. It’s pretty easy to produce a realistic looking heightmap with <feTurbulence> — we only need to focus on one color channel and we already have it. Let’s focus on the Red channel. With the help of a <feColorMatrix>, we turn the colorful noise into a grayscale heightmap by overwriting the Green and Blue channels with the value of the Red channel.

\begin{bmatrix} 1 & 0 & 0 & 0 & 0 \\ 1 & 0 & 0 & 0 & 0 \\ 1 & 0 & 0 & 0 & 0 \\ 0 & 0 & 0 & 0 & 1 \end{bmatrix}
Now we can rely on the same value for each color channel per pixel. This makes it easy to recolor our image level-by-level with the help of <feComponentTransfer>, although, this time, we use a table type of function. table is similar to discrete, but each step is a ramp to the next step. This allows for a much smoother transition between levels.


The RGB transfer functions defined in <feComponentTransfer>
The number of tableValues determine how many ramps are in the transfer function. We have to consider two things to find the optimal number of ramps. One is the distribution of intensity in the image. The different intensities are unevenly distributed, although the ramp widths are always equal. The other thing to consider is the number of levels we would like to see. And, of course, we also need to remember that we are in linearized RGB space. We could get into the maths of all these, but it’s much easier to just play around and feel out the right values.


Mapping grayscale to colors with <feComponentTransfer>
We use deep blue and aqua color values from the lowest intensities to somewhere in the middle to represent the water. Then, we use a few flavors of yellow for the sandy parts. Finally, green and dark green at the highest intensities create the forest.

We haven’t seen the seed attribute in any these examples, but I invite you to try it out by adding it in there. Think of a random number between 1 and 10 million, then use that number as the seed attribute value in <feTurbulence>, like <feTurbulence seed="3761593" ... >

Now you have your own variation of the pattern!

Production use
So far, what we’ve done is look at a bunch of cool SVG patterns and how they’re made. A lot of what we’ve seen is great proof-of-concept, but the real benefit is being able to use the patterns in production in a responsible way.

The way I see it, there are three fundamental paths to choose from.

Method 1: Using an inline data URI in CSS or HTML
My favorite way to use SVGs is to inline them, provided they are small enough. For me, “small enough” means a few kilobytes or less, but it really depends on the particular use case. The upside of inlining is that the image is guaranteed to be there in your CSS or HTML file, meaning there is no need to wait until it is downloaded.

The downside is having to encode the markup. Fortunately, there are some great tools made just for this purpose. Yoksel’s URL-encoder for SVG is one such tool that provides a copy-paste way UI to generate the code. If you’re looking for a programmatic approach — like as part of a build process — I suggest looking into mini-svg-data-uri. I haven’t used it personally, but it seems quite popular.

Regardless of the approach, the encoded data URI goes right in your CSS or HTML (or even JavaScript). CSS is better because of its reusability, but HTML has minimal delivery time. If you are using some sort of server-side rendering technique, you can also slip a randomized seed value in <feTurbulence> within the data URI to show a unique variation for each user.

Here’s the Starry Sky example used as a background image with its inline data URI in CSS:

.your-selector {
  background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='filter'%3E%3CfeTurbulence baseFrequency='0.2'/%3E%3CfeColorMatrix values='0 0 0 9 -4 0 0 0 9 -4 0 0 0 9 -4 0 0 0 0 1'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23filter)'/%3E%3C/svg%3E%0A");
}
And this is how it looks used as an `<img>` in HTML:

```html
<img src="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg'%3E%3Cfilter id='filter'%3E%3CfeTurbulence baseFrequency='0.2'/%3E%3CfeColorMatrix values='0 0 0 9 -4 0 0 0 9 -4 0 0 0 9 -4 0 0 0 0 1'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23filter)'/%3E%3C/svg%3E%0A"/>
```

Method 2: Using SVG markup in HTML
We can simply put the SVG code itself into HTML. Here’s Starry Sky’s markup, which can be dropped into an HTML file:

```html
<div>
  <svg xmlns="http://www.w3.org/2000/svg">
    <filter id="filter">
      <feTurbulence baseFrequency="0.2"/>
      <feColorMatrix values="0 0 0 9 -4
                             0 0 0 9 -4
                             0 0 0 9 -4
                             0 0 0 0 1"/>
    </filter>
    <rect width="100%" height="100%" filter="url(#filter)"/>
  </svg>
</div>
```

It’s a super simple approach, but carries a huge drawback — especially with the examples we’ve seen.

That drawback? ID collision.

Notice that the SVG markup uses a #filter ID. Imagine adding the other examples in the same HTML file. If they also use a #filter ID, then that would cause the IDs to collide where the first instance overrides the others.

Personally, I would only use this technique in hand-crafted pages where the scope is small enough to be aware of all the included SVGs and their IDs. There’s the option of generating unique IDs during a build, but that’s a whole other story.

Method 3: Using a standalone SVG
This is the “classic” way to do SVG. In fact, it’s just like using any other image file. Drop the SVG file on a server, then use the URL in an HTML <img> tag, or somewhere in CSS like a background image.

So, going back to the Starry Sky example. Here’s the contents of the SVG file again, but this time the file itself goes on the server.

```html
<svg xmlns="http://www.w3.org/2000/svg">
  <filter id="filter">
    <feTurbulence baseFrequency="0.2"/>
    <feColorMatrix values="0 0 0 9 -4
                           0 0 0 9 -4
                           0 0 0 9 -4
                           0 0 0 0 1"/>
  </filter>
  <rect width="100%" height="100%" filter="url(#filter)"/>
</svg>
```

Now we can use it in HTML, say as as image:

```html
<img src="https://example.com/starry-sky.svg"/>
```

And it’s just as convenient to use the URL in CSS, like a background image:

```css
.your-selector {
  background-image: url("https://example.com/starry-sky.svg");
}
```

Considering today’s HTTP2 support and how relatively small SVG files are compared to raster images, this isn’t a bad solution at all. Alternatively, the file can be placed on a CDN for even better delivery. The benefit of having SVGs as separate files is that they can be cached in multiple layers.

Caveats
While I really enjoy crafting these little patterns, I also have to acknowledge some of their imperfections.

The most important imperfection is that they can pretty quickly create a computationally heavy “monster” filter chain. The individual filter effects are very similar to one-off operations in photo editing software. We are basically “photoshopping” with code, and every time the browser displays this sort of SVG, it has to render each operation. So, if you end up having a long filter chain, you might be better off capturing and serving your result as a JPEG or PNG to save users CPU time. Taylor Hunt’s “Improving SVG Runtime Performance” has a lot of other great tips for getting the most performance out of SVG.

Secondly, we’ve got to talk about browser support. Generally speaking, SVG is well-supported, especially in modern browsers. However, I came across one issue with Safari when working with these patterns. I tried creating a repeating circular pattern using <radialGradient> with spreadMethod="repeat". It worked well in Chrome and Firefox, but Safari wasn’t happy with it. Safari displays the radial gradient as if its spreadMethod was set to pad. You can verify it right in MDN’s documentation.

You may get different browsers rendering the same SVG differently. Considering all the complexities of rendering SVG, it’s pretty hard to achieve perfect consistency. That said, I have only found one difference between the browsers that’s worth mentioning and it’s when switching to “full screen” view. When Firefox goes full screen, it doesn’t render the SVG in the extended part of the viewport. Chrome and Safari are good though. You can verify it by opening this pen in your browser of choice, then going into full screen mode. It’s a rare edge-case that could probably be worked around with some JavaScript and the Fullscreen API.

## Thanks

Phew, that’s a wrap! We not only got to look at some cool patterns, but we learned a ton about <feTurbulence> in SVG, including the various filters it takes and how to manipulate them in interesting ways.

Like most things on the web, there are downsides and potential drawbacks to the concepts we covered together, but hopefully you now have a sense of what’s possible and what to watch for. You have the power to create some awesome patterns in SVG!