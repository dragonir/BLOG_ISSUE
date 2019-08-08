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