
# vue项目配置 `webpack-obfuscator` 进行代码加密混淆

## 背景

> 公司代码提供给第三方使用，为了不完全泄露源码，需要对给出的代码进行加密混淆，前端代码虽然无法做到完全加密混淆，但是通过使用 `webpack-obfuscator` 通过增加随机废代码段、字符编码转义等方法可以使构建代码完全混淆，达到无法恢复源码甚至无法阅读的目的。

## 安装

> `webpack-obfuscator` <https://www.npmjs.com/package/webpack-obfuscator>

```bash
npm install --save-dev webpack-obfuscator
```

## 配置

```js
// webpack.config.js
const JavaScriptObfuscator = require('webpack-obfuscator');
module.exports = {
  entry: {
    'abc': './test/input/index.js',
    'cde': './test/input/index1.js'
  },
  output: {
    path: 'dist',
    filename: '[name].js'
  },
  plugins: [
    new JavaScriptObfuscator({
      rotateUnicodeArray: true
      // 数组内是需要排除的文件
    }, ['abc.js'])
  ]
};
```

`vue cli` 项目配置：

```js
// vue.config.js
const path = require('path');
var JavaScriptObfuscator = require('webpack-obfuscator');
module.exports = {
  publicPath: process.env.NODE_ENV === 'production' ? './' : '/',
  productionSourceMap: false,
  configureWebpack: {
    plugins: [
      new JavaScriptObfuscator({
        rotateStringArray: true,
      }, [])
    ]
  },
  pwa: {},
  pages: {}
}
```

> `vue cli 2.x` 配置在 `webpack.prod.conf.js` 中

## 构建

```bash
npm run build
```

## 构建文件对比

### 1. 原始文件

```js
// test.js
function abc() {
    for (let i = 0; i < 10; i++) {
        console.log(`第${i}个,你好,hello`)
    }
}
abc()
```

### 2. `webpack` 默认工具`uglifyjs-webpack-plugin`

```js
webpackJsonp([2],{lVK7:function(o,l){!function(){for(var o=0;o<10;o++)console.log("第"+o+"个,你好,hello")}()}},["lVK7"]);
```

### 3. `webpack-obfuscator` 无参数时

```js
new JavaScriptObfuscator({
}, [])
```

```js
var _0x1f6e=["个,你好,hello","lVK7","log"];!function(n,o){!function(o){for(;--o;)n.push(n.shift())}(++o)}(_0x1f6e,323);var _0x3655=function(n,o){return _0x1f6e[n-=0]};webpackJsonp([2],{lVK7:function(n,o){!function(){for(var n=0;n<10;n++)console[_0x3655("0x0")]("第"+n+_0x3655("0x1"))}()}},[_0x3655("0x2")]);
```

### 4. `webpack-obfuscator` 高度混淆

低性能：性能比没有模糊处理慢 `50-100%`

```js
new JavaScriptObfuscator({
  // 压缩代码
  compact: true,
  // 是否启用控制流扁平化(降低1.5倍的运行速度)
  controlFlowFlattening: true,
  // 应用概率;在较大的代码库中，建议降低此值，因为大量的控制流转换可能会增加代码的大小并降低代码的速度。
  controlFlowFlatteningThreshold: 1,
  // 随机的死代码块(增加了混淆代码的大小)
  deadCodeInjection: true,
  // 死代码块的影响概率
  deadCodeInjectionThreshold: 1,
  // 此选项几乎不可能使用开发者工具的控制台选项卡
  debugProtection: true,
  // 如果选中，则会在“控制台”选项卡上使用间隔强制调试模式，从而更难使用“开发人员工具”的其他功能。
  debugProtectionInterval: true,
  // 通过用空函数替换它们来禁用console.log，console.info，console.error和console.warn。这使得调试器的使用更加困难。
  disableConsoleOutput: true,
  // 标识符的混淆方式 hexadecimal(十六进制) mangled(短标识符)
  identifierNamesGenerator: 'hexadecimal',
  log: false,
  // 是否启用全局变量和函数名称的混淆
  renameGlobals: false,
  // 通过固定和随机（在代码混淆时生成）的位置移动数组。这使得将删除的字符串的顺序与其原始位置相匹配变得更加困难。如果原始源代码不小，建议使用此选项，因为辅助函数可以引起注意。
  rotateStringArray: true,
  // 混淆后的代码,不能使用代码美化,同时需要配置 cpmpat:true;
  selfDefending: true,
  // 删除字符串文字并将它们放在一个特殊的数组中
  stringArray: true,
  stringArrayEncoding: 'rc4',
  stringArrayThreshold: 1,
  // 允许启用/禁用字符串转换为unicode转义序列。Unicode转义序列大大增加了代码大小，并且可以轻松地将字符串恢复为原始视图。建议仅对小型源代码启用此选项。
  transformObjectKeys: true,
  unicodeEscapeSequence: false
}, []),
```

构建后文件大小: `29,999 字节(29.2 KB)`

```js
var _0xa0d1=["w7Bzw6oKw6E=","wrwIUcOVw4M=","w4bChi3DtcOQ","wpLDtsK5w4LDpA==","OUlQwp1z","woEqw4XCtsOe","YR3DrkDCiA==","woAjwq/Ci8KQ","dDNzw5bDgA==",
// ...
("0x201","xatR")]=function(x){return x()},x[_0x34e6("0x202","vdcx")](_0x2c01f8)},4e3);
```

### 3.  `webpack-obfuscator` 中等混淆

最佳性能：性能比没有模糊处理慢 `30-35%`

```js
new JavaScriptObfuscator({
  // 压缩代码
  compact: true,
  // 是否启用控制流扁平化(降低1.5倍的运行速度)
  controlFlowFlattening: true,
  // 应用概率;在较大的代码库中，建议降低此值，因为大量的控制流转换可能会增加代码的大小并降低代码的速度。
  controlFlowFlatteningThreshold: 0.75,
  // 随机的死代码块(增加了混淆代码的大小)
  deadCodeInjection: true,
  // 死代码块的影响概率
  deadCodeInjectionThreshold: 0.4,
  // 此选项几乎不可能使用开发者工具的控制台选项卡
  debugProtection: false,
  // 如果选中，则会在“控制台”选项卡上使用间隔强制调试模式，从而更难使用“开发人员工具”的其他功能。
  debugProtectionInterval: false,
  // 通过用空函数替换它们来禁用console.log，console.info，console.error和console.warn。这使得调试器的使用更加困难。
  disableConsoleOutput: true,
  // 标识符的混淆方式 hexadecimal(十六进制) mangled(短标识符)
  identifierNamesGenerator: 'hexadecimal',
  log: false,
  // 是否启用全局变量和函数名称的混淆
  renameGlobals: false,
  // 通过固定和随机（在代码混淆时生成）的位置移动数组。这使得将删除的字符串的顺序与其原始位置相匹配变得更加困难。如果原始源代码不小，建议使用此选项，因为辅助函数可以引起注意。
  rotateStringArray: true,
  // 混淆后的代码,不能使用代码美化,同时需要配置 cpmpat:true;
  selfDefending: true,
  // 删除字符串文字并将它们放在一个特殊的数组中
  stringArray: true,
  stringArrayEncoding: 'base64',
  stringArrayThreshold: 0.75,
  transformObjectKeys: true,
  // 允许启用/禁用字符串转换为unicode转义序列。Unicode转义序列大大增加了代码大小，并且可以轻松地将字符串恢复为原始视图。建议仅对小型源代码启用此选项。
  unicodeEscapeSequence: false
}, []),
```

构建后文件大小：`7066字节(6.90kb)`

```js
var _0x1a25=["UmFzT1U=","RkVIc0o=","VUt2eW4=","Q29IS0g=","V1NSZ0k=","d3RNT2w=","dlV6cUw=","RlpzZWg=","QnpzSlE=","cXBqQ1k=","YXBwbHk=","bFZLNw==","Y3p1Ymo=","TFZlQXE=","Y2NKWlY=","cmV0dXJuIChmdW5jdGlvbigpIA==",
// ...
(b[_0x4bcb("0x2a")]("第"+c,b[_0x4bcb("0x2b")]))}})}},[_0x4bcb("0x2f")]);
```

### 4. `webpack-obfuscator` 低混淆

高性能: 性能稍微慢于没有混淆

```js
new JavaScriptObfuscator({
  // 压缩代码
  compact: true,
  // 是否启用控制流扁平化(降低1.5倍的运行速度)
  controlFlowFlattening: false,
  // 随机的死代码块(增加了混淆代码的大小)
  deadCodeInjection: false,
  // 此选项几乎不可能使用开发者工具的控制台选项卡
  debugProtection: false,
  // 如果选中，则会在“控制台”选项卡上使用间隔强制调试模式，从而更难使用“开发人员工具”的其他功能。
  debugProtectionInterval: false,
  // 通过用空函数替换它们来禁用console.log，console.info，console.error和console.warn。这使得调试器的使用更加困难。
  disableConsoleOutput: true,
  // 标识符的混淆方式 hexadecimal(十六进制) mangled(短标识符)
  identifierNamesGenerator: 'hexadecimal',
  log: false,
  // 是否启用全局变量和函数名称的混淆
  renameGlobals: false,
  // 通过固定和随机（在代码混淆时生成）的位置移动数组。这使得将删除的字符串的顺序与其原始位置相匹配变得更加困难。如果原始源代码不小，建议使用此选项，因为辅助函数可以引起注意。
  rotateStringArray: true,
  // 混淆后的代码,不能使用代码美化,同时需要配置 cpmpat:true;
  selfDefending: true,
  // 删除字符串文字并将它们放在一个特殊的数组中
  stringArray: true,
  stringArrayEncoding: false,
  stringArrayThreshold: 0.75,
  // 允许启用/禁用字符串转换为unicode转义序列。Unicode转义序列大大增加了代码大小，并且可以轻松地将字符串恢复为原始视图。建议仅对小型源代码启用此选项。
  unicodeEscapeSequence: false
}, []),
```

构建后文件大小: `2,424 字节(2.36 KB)`

```js
var _0x37a6=["exception","trace","console","个,你好,hello","lVK7","apply","return (function() ",'{}.constructor("return this")( )',"log","warn","debug","info","error"];!function(n,e){var o=function(e){for(;--e;)n.push(n.shift())};
// ...
[_0xe1fd("0x3")]("第"+n+_0xe1fd("0xb"))}()}},[_0xe1fd("0xc")]);
```

#### 对比表格

| 文件名称 | 文件大小          | 正常构建          | 无参数               | 高度混淆             | 中度混淆           | 低度混淆          |
| --------- | --------------------- | --------------------- | ----------------------- | ------------------------ | ---------------------- | --------------------- |
| `test.js`   | `117字节`             | `177字节`             | `363字节`               | `29.2 KB(29,999 字节)` | `6.90KB(7066字节)`    | `2.36 KB(2,424 字节)` |
| `jquery.js` | `111 KB (113,852 字节)` | `85.0 KB (87,064 字节)` |  `115 KB (117,770 字节)`  | `1.24 MB (1,304,998 字节)` | `401 KB (411,543 字节)`  | `117 KB (120,243 字节)` |

## 主要属性

```js
{
  // 压缩,无换行
  compact: true,
  // 是否启用控制流扁平化(降低1.5倍的运行速度)
  controlFlowFlattening: false,
  // 应用概率;在较大的代码库中，建议降低此值，因为大量的控制流转换可能会增加代码的大小并降低代码的速度。
  controlFlowFlatteningThreshold: 0.75,
  // 随机的死代码块(增加了混淆代码的大小)
  deadCodeInjection: false,
  // 死代码块的影响概率
  deadCodeInjectionThreshold: 0.4,
  // 此选项几乎不可能使用开发者工具的控制台选项卡
  debugProtection: false,
  // 如果选中，则会在“控制台”选项卡上使用间隔强制调试模式，从而更难使用“开发人员工具”的其他功能。
  debugProtectionInterval: false,
  // 通过用空函数替换它们来禁用console.log，console.info，console.error和console.warn。这使得调试器的使用更加困难。
  disableConsoleOutput: false,
  //锁定混淆的源代码，使其仅在特定域和/或子域上运行。这使得某人只需复制并粘贴您的源代码并在其他地方运行就变得非常困难。
  domainLock: [],
  //标识符的混淆方式 hexadecimal(十六进制) mangled(短标识符)
  identifierNamesGenerator: 'hexadecimal',
  //全局标识符添加特定前缀,在混淆同一页面上加载的多个文件时使用此选项。此选项有助于避免这些文件的全局标识符之间发生冲突。为每个文件使用不同的前缀
  identifiersPrefix: '',
  inputFileName: '',
  // 允许将信息记录到控制台。
  log: false,
  // 是否启用全局变量和函数名称的混淆
  renameGlobals: false,
  // 禁用模糊处理和生成标识符
  reservedNames: [],
  // 禁用字符串文字的转换
  reservedStrings: [],
  // 通过固定和随机（在代码混淆时生成）的位置移动数组。这使得将删除的字符串的顺序与其原始位置相匹配变得更加困难。如果原始源代码不小，建议使用此选项，因为辅助函数可以引起注意。
  rotateStringArray: true,
  // 混淆后的代码,不能使用代码美化,同时需要配置 cpmpat:true;
  seed: 0,
  selfDefending: false,
  sourceMap: false,
  sourceMapBaseUrl: '',
  sourceMapFileName: '',
  sourceMapMode: 'separate',
  // 删除字符串文字并将它们放在一个特殊的数组中
  stringArray: true,
  // 编码的所有字符串文字stringArray使用base64或rc4并插入即用其解码回在运行时的特殊代码。true（boolean）：stringArray使用编码值base64;false（boolean）：不编码stringArray值;'base64'（string）：stringArray使用编码值base64;'rc4'（string）：stringArray使用编码值rc4。大约慢30-50％base64，但更难获得初始值。建议禁用unicodeEscapeSequence带rc4编码的选项以防止非常大的混淆代码。
  stringArrayEncoding: false,
  // 调整字符串文字将插入stringArray的概率
  stringArrayThreshold: 0.75,
  // 您可以将混淆代码的目标环境设置为以下之一：Browser;Browser No Eval;Node
  target: 'browser',
  // 是否启用混淆对象键
  transformObjectKeys: false,
  // 允许启用/禁用字符串转换为unicode转义序列。Unicode转义序列大大增加了代码大小，并且可以轻松地将字符串恢复为原始视图。建议仅对小型源代码启用此选项。
  unicodeEscapeSequence: false
}
```

## 注意

安装 `webpack-obfuscator` 时要注意`webpack-obfuscator`的版本要与本地项目 `webpack` 版本相匹配，我用的是`webpack-obfuscator@0.18.0` 项目 `webpack` 是 `4.x` 版本。（`4.x版` 本 `webpack` 使用最新版 `webpack-obfuscator@3.3.0` 会报错无法使用，`webpack` 杳升级到 `5.x` 版本）

> 相关文章参考：js代码混淆 webpack-obfuscator <https://blog.csdn.net/qq_31126175/article/details/86526237>
