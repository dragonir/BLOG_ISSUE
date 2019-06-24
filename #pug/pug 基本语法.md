# PUG 基本语法

## 基本语法

```pug
docttype html
html
  head
  body
    h1 hello world!
    p.
      段落1
      段落2
    ul
      li one
      li two
      li three
```

```html
<!DOCTYPE html>
<html>
  <head></head>
  <body>
    <h1>hello world!</h1>
    <p>
      段落1
      段落2
    </p>
    <ul>
      <li>one</li>
      <li>two</li>
      <li>three</li>
    </ul>
  </body>
</html>
```

## id和class

```pug
doctype html
html
  head
  body
    h1#pageTitle page title goes here!
    p.big-para.
      abcdefg
      hijklmn
    p.big-para.
      abcdefg
      hijklmn
    .display-box
    #myOnlyDiv
```

```html
<!DOCTYPE html>
<html>
  <head></head>
  <body>
    <h1 id="pageTitle">page title goes here!</h1>
    <p class="big-para">
      abcdefg
      hijklmn
    </p>
    <p class="big-para">
      abcdefg
      hijklmn
    </p>
    <div class="display-box"></div>
    <div id="myOnlyDiv"></div>
  </body>
</html>
```

## 标签中的属性

```pug
doctype html
html
  head
  body
    input(type="password" name="inpPwd" data-js=`${1 > 2 ? "ok" : "notok"}`)

    - const myClasses = ["class1", "class2", "class3"]
    div(class=myClasses)
    div.mydiv(class=myClasses)

    - const myStyles = {"color": "red", "background-color": "blue"}
    div(style=myStyles)

    - const myAttributes = {"src": "myPhoto.png", "alt": "this is my photo"}
    img&attributes(myAttributes)

    input(type="text" disabled)
    input(type="text" diisabled=false)
```

```html
<!DOCTYPE html>
<html>
  <head></head>
  <body>
    <input type="password" name="inpPwd" data-js="notok">
    <div class="class1 class2 class3"></div>
    <div class="mydiv class1 class2 class3"></div>
    <div style="color:red;background-color:blue;"></div><img src="myPhoto.png" alt="this is my photo">
    <input type="text" disabled>
    <input type="text">
  </body>
</html>
```

## 引入css

```pug
doctype html
html
  head
    link(ref="stylesheets" href="styles.css")
    style.
      p{
        color: red;
        text-decoration: underline;
      }
  
  body
    p this is a html file
  
    p(style="text-align:center; text-transform: uppercase") this is a html file

    -const pStyles = {"text-align": "center", "text-transform": "uppercase"};15
    p(style=pStyles) file216
```

```html
<!DOCTYPE html>
<html>
  <head>
    <link ref="stylesheets" href="styles.css">
    <style>
      p{
        color: red;
        text-decoration: underline;
      }
    </style>
  </head>
  <body>
    <p>this is a html file</p>
    <p style="text-align:center; text-transform: uppercase">this is a html file</p>
    <p style="text-align:center;text-transform:uppercase;">file216</p>
  </body>
</html>
```

## 循环

```pug
doctype html
html
  head
  body
    h1 for / each loop
    each n in [50, 2, 3, 4, 5]
      p= n
    each n,i in [50, 2, 3, 4, 5]
      p= n + '-' + i

    - const names = ["aaa", "bbb", "ccc"]
    each n,i in names
      p= n+ '-' + i

    - const grades = {"web dev": 85, "software design": 76}
    each n,i in grades
      p= n + "-" + i

    for n,i in []
      p= n + '-' + i
    else
      strong "no values are here !"
```

```html
<!DOCTYPE html>
<html>
  <head></head>
  <body>
    <h1>for / each loop</h1>
    <p>50</p>
    <p>2</p>
    <p>3</p>
    <p>4</p>
    <p>5</p>
    <p>50-0</p>
    <p>2-1</p>
    <p>3-2</p>
    <p>4-3</p>
    <p>5-4</p>
    <p>aaa-0</p>
    <p>bbb-1</p>
    <p>ccc-2</p>
    <p>85-web dev</p>
    <p>76-software design</p><strong>"no values are here !"</strong>
  </body>
</html>
```

## 条件判断

```pug
doctype html
html
  head
  body
    h2 my web application

    - let user = {name: "domenic", loggedIn: true, lastLogin: 6}
    if user.loggedIn
      p
        span welcome back,
        strong #{ user.name }
    else if user.lastLogin < 10
      p your last login was #{ user.lastLogin } minutes ago
      p
        a(href="/login") login again
    else
      a(href="/login") login
```

```html
<!DOCTYPE html>
<html>
  <head></head>
  <body>
    <h2>my web application</h2>
    <p><span>welcome back,</span><strong>domenic</strong></p>
  </body>
</html>
```

## 多路分支

```pug
doctype html
html
  head
  body
    - const orderStates = "_Pending"

    case orderStates
      when "Pending"
        p your order has been placed and will be sent shotly
      when "In_Transit"
        p your order is in transit
      when "Completed"
        p your order is completed
      default
        p waiting.
```

```html
<!DOCTYPE html>
<html>
  <head></head>
  <body>
    <p>waiting.</p>
  </body>
</html>
```
