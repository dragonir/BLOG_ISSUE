# NOTES

## 1. 无穷数

```js
1 / 0;                  // Infinity
-1 / 0;                 // -Infinity
Infinity / Infinity;    // NaN
```

## 2. 零值

```js
0 / -3; // -0
0 * -3; // -0
-0 == 0; // true
-0 === 0; // true
```

对负零进行字符串化会返回 "0"

## 3. 内部属性

```js
Object.prototype.toString.call( [1,2,3] );
// "[object Array]"
Object.prototype.toString.call( /regex-literal/i );
// "[object RegExp]"
Object.prototype.toString.call( null );
// "[object Null]"
Object.prototype.toString.call( undefined );
// "[object Undefined]"
```

## 假值

```js
以下这些是假值:
• undefined
• null
• false
• +0、-0 和 NaN
• ""
```

## 真值

```js
var a = "false";
var b = "0";
var c = "''";
var d = Boolean( a && b && c );       // true

var a = [];           // 空数组——是真值还是假值? 
var b = {};           // 空对象——是真值还是假值?
var c = function(){};  // 空函数——是真值还是假值?
var d = Boolean( a && b && c );       // true
```

## 强制类型转换

```js
1 + - + + + - + 1;          // 2
```

我们常用下面的方法来获得当前的时间戳，例如:

```js
var timestamp = +new Date();
var timestamp = +new Date;

var timestamp = new Date().getTime();
var timestamp = (new Date()).getTime();
var timestamp = (new Date).getTime();

// 不过最好还是使用 ES5 中新加入的静态方法 Date.now():
var timestamp = Date.now();

// 为老版本浏览器的polyfill
if (!Date.now) {
         Date.now = function() {
             return +new Date();
         };
}
```

## ～运算符

 ~ 和 indexOf() 一起可以将结果强制类型转换(实际 上仅仅是转换)为真 / 假值:

```js
var a = "Hello World";
~a.indexOf( "lo" );       // -4  <-- 真值!
if (~a.indexOf( "lo" )) {
   // 找到匹配!
}
~a.indexOf( "ol" );       // 0  <-- 假值!
!~a.indexOf( "ol" );      // true
if (!~a.indexOf( "ol" )) { // true 
  // 没有找到匹配!
}
```

~~x 能将值截除为一个 32 位整数，x | 0 也可以，而且看起来还更简洁。

```js
~~1E20 / 10;            // 166199296
1E20 | 0 / 10;          // 1661992960
(1E20 | 0) / 10;        // 166199296

```

## 解析非字符串

```js
parseInt( 1/0, 19 ); // 18
// parseInt(1/0, 19) 实际上是 parseInt("Infinity", 19)。第一个字符是 "I"，以 19 为基数 时值为 18。第二个字符 "n" 不是一个有效的数字字符，解析到此为止，和 "42px" 中的 "p" 一样。
```

此外还有一些看起来奇怪但实际上解释得通的例子:

```js
parseInt( 0.000008 );  // 0 ("0" 来自于 "0.000008")
parseInt( 0.0000008 ); // 8 ("8" 来自于 "8e-7")
parseInt( false, 16 );// 250 ("fa" 来自于 "false")
parseInt( parseInt, 16 );// 15 ("f" 来自于 "function..")
parseInt( "0x10" );// 16
parseInt( "103", 2 );// 2
```

## 显式转换为布尔值

显式强制类型转换为布尔值最常用的方法是 !!，因为第二个 ! 会将结果反转回原值:

```js
var a = "0";
var b = [];
var c = {};
var d = "";
var e = 0;
var f = null;
var g;
!!a; // true
!!b; // true
!!c; // true
!!d; // false
!!e; // false
!!f; // false
!!g; // false
```

## 字符串和数字之间的隐式强制类型转换

```js
var a = [1,2];
var b = [3,4];
a + b; // "1,23,4"

> var a = [1]
> var b = [2]
> a + b
'12'
> a - b
-1
```

```js
[] + {}
'[object Object]'
> []+[]
''
> {} + []
0
> {} + {}
'[object Object][object Object]'
```

## 符号的强制类型转换

```js
> var s = Symbol("sym")
> s
Symbol(sym)
> Boolean(s)
true
> s
Symbol(sym)
> s - 1
Thrown:
TypeError: Cannot convert a Symbol value to a number
> s + ""
Thrown:
TypeError: Cannot convert a Symbol value to a string
```

## 抽象相等

NaN 不等于 NaN
+0 等于 -0

(1) 如果 Type(x) 是数字，Type(y) 是字符串，则返回 x == ToNumber(y) 的结果。
(2) 如果 Type(x) 是字符串，Type(y) 是数字，则返回 ToNumber(x) == y 的结果。

(1) 如果 Type(x) 是布尔类型，则返回 ToNumber(x) == y 的结果;
(2) 如果 Type(y) 是布尔类型，则返回 x == ToNumber(y) 的结果。

(1) 如果 x 为 null，y 为 undefined，则结果为 true。
(2) 如果 x 为 undefined，y 为 null，则结果为 true。

(1) 如果 Type(x) 是字符串或数字，Type(y) 是对象，则返回 x == ToPrimitive(y) 的结果;
(2) 如果 Type(x) 是对象，Type(y) 是字符串或数字，则返回 ToPromitive(x) == y 的结果。

## 假值的相等比较

下面分别列出了常规和非常规的情况:

```js
"0" == null;                    // false
"0" == undefined;               // false
"0" == false;                   // true -- 晕!
"0" == NaN;                     // false
"0" == 0;                       // true
"0" == "";                      // false

false == null;                  // false
false == undefined;             // false
false == NaN;                   // false
false == 0;                     // true -- 晕!
false == "";                    // true -- 晕!
false == [];                    // true -- 晕!
false == {};                    // false

"" == null;                     // false
"" == undefined;                // false
"" == NaN;                      // false
"" == 0;                        // true -- 晕!
"" == [];                       // true -- 晕!
"" == {};                       // false

0 == null;                      // false
0 == undefined;                 // false
0 == NaN;                       // false
0 == [];                        // true -- 晕!
0 == {};                        // false
```

## 极端情况

```js
[] == ![]                     // true
2 == [2];                     // true
"" == [null];                 // true
0 == "\n";                    // true
```
