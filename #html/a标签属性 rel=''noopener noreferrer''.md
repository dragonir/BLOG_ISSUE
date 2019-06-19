# a标签属性 rel=''noopener noreferrer''

```html
<a href="www.baidu.com" target="_blank"></a>
```

当你浏览一个页面点击一个a标签连接,跳转到另一个页面时，在新打开的页面（baidu）中可以通过 window.opener获取到源页面的部分控制权， 即使新打开的页面是跨域的也照样可以（例如 location 就不存在跨域问题）。  

## rel=noopener 新特性

```html
<a href="www.baidu.com" target="_blank" rel="noopener noreferrer"></a>
```

在chrome 49+，Opera 36+，打开添加了 ```rel=noopener``` 的链接， ```window.opener``` 会为 ```null``` 。在老的浏览器中，可以使用 ```rel=noreferrer``` 禁用HTTP头部的 ```Referer``` 属性，使用下面JavaScript代替target='_blank' 的解决此问题：

```javascript
var otherWindow = window.open('http://keenwon.com');
otherWindow.opener = null;
otherWindow.location = url;
```

使用 window.open 打开页面，手动将opener设置为null。

## rel="nofllow"

```rel="nofllow"``` 的作用，是告诉搜索引擎，不要将该链接计入权重。因此多数情况下，我们可以将一些不想传递权重的链接进行 ```ofllow``` 处理；例如一些非本站的链接，不想传递权重，但是又需要加在页面中的像 统计代码、备案号链接、供用户查询的链接等等。

## rel="external"

此属性的意思是告诉搜索引擎，这个链接不是本站链接，其实作用相当于 ```target="_blank"```。
为什么要这样写呢？因为有些网站因为是采用严格的DOCTYPE声名的，如果你在网页源码中的第一行看到：在这种情况下 ```target="_blank"``` 会失效，因此采用 ```rel="external"``` 这个参数来替代。

## rel="external nofollow"

其实上面已经说过 ```rel="nofollow"``` 和 ```rel="external"``` 两种属性的所代表的意思了。这个属性基本上是相当于将两种属性结合起来，大致可以解释为 “这个链接非本站链接，不要爬取也不要传递权重”。因此在 ```SEO``` 的角度来说，是一种绝对隔绝处理的方法，可以有效减少蜘蛛爬行的流失。

## rel="nofollow noopener noreferrer"

超链接 ```target="_blank"``` 要增加 ```rel="nofollow noopener noreferrer"``` 来堵住**钓鱼安全漏洞**。如果你在链接上使用 ```target="_blank"``` 属性，并且不加上 ```rel="noopener"``` 属性，那么你就让用户暴露在一个非常简单的钓鱼攻击之下。
为了告知来自于不受保护的站点的用户，我们运行一个利用了这个缺陷的脚本。

```javascript
if (window.opener) {
  window.opener.location = "https://dev.to/phishing?referrer="+document.referrer;
}
```

我相信绝大多数站点都没有恰当地处理这个问题,为了限制 ```window.opener``` 的访问行为，原始页面需要在每个使用了 ```target="_blank"``` 的链接中加上一个 ```rel="noopener"``` 属性。然而，火狐不支持这个属性值，所以实际上你要用 ```rel="noopener noreferrer"```来完整覆盖。
