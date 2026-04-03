---
description: 如何正确判断字符串长度
title: 转载-如何正确判断字符串长度
readingTime: false
tag:
 - 前端基础
recommend: 3
---

>原文地址 [淦，为什么 "𠮷𠮷𠮷".length !== 3](https://juejin.cn/post/7025400771982131236)


不知道你是否遇到过这样的疑惑，在做表单校验长度的需求中，发现不同字符 length 可能大小不一。比如标题中的 "𠮷" length 是 2（需要注意📢，这并不是一个中文字！）。

js复制代码'吉'.length // 1  '𠮷'.length // 2  '❤'.length // 1  '💩'.length // 2


要解释这个问题要从 UTF-16 编码说起。

UTF-16
从 ECMAScript® 2015 规范中可以看到，ECMAScript 字符串使用的是 UTF-16 编码。

定与不定:  UTF-16 最小的码元是两个字节，即使第一个字节可能都是 0 也要占位，这是固定的。不定是对于基本平面（BMP）的字符只需要两个字节，表示范围 U+0000 ~ U+FFFF，而对于补充平面则需要占用四个字节 U+010000~U+10FFFF。

在上一篇文章中，我们有介绍过 utf-8 的编码细节，了解到 utf-8 编码需要占用 1~4 个字节不等，而使用 utf-16 则需要占用 2 或 4 个字节。来看看 utf-16 是怎么编码的。

UTF-16 的编码逻辑
UTF-16 编码很简单，对于给定一个 Unicode 码点 cp（CodePoint 也就是这个字符在 Unicode 中的唯一编号）:

如果码点小于等于 U+FFFF（也就是基本平面的所有字符），不需要处理，直接使用。

否则，将拆分为两个部分 ((cp – 65536) / 1024) + 0xD800，((cp – 65536) % 1024) + 0xDC00 来存储。

Unicode 标准规定 U+D800...U+DFFF 的值不对应于任何字符，所以可以用来做标记。

举个具体的例子：字符 A 的码点是 U+0041，可以直接用一个码元表示。

js复制代码'\u0041' // -> A  A === '\u0041' // -> true


Javascript 中 \u 表示 Unicode 的转义字符，后面跟着一个十六进制数。

而字符 💩  的码点是 U+1f4a9，处于补充平面的字符，经过 👆 公式计算得到两个码元 55357, 56489 这两个数字用十六进制表示为 d83d, dca9，将这两个编码结果组合成代理对。

js复制代码'\ud83d\udca9' // -> '💩'  '💩' === '\ud83d\udca9' // -> true


由于 Javascript 字符串使用 utf-16 编码，所以可以正确将代理对 \ud83d\udca9 解码得到码点 U+1f4a9。

还可以使用 \u + {}，大括号中直接跟码点来表示字符。看起来长得不一样，但他们表示的结果是一样的。

js复制代码'\u0041' === '\u{41}' // -> true  '\ud83d\udca9' === '\u{1f4a9}' // -> true


可以打开 Dev Tool 的 console 面板，运行代码验证结果。

所以为什么 length 判断会有问题?
要解答这个问题，可以继续查看 规范，里面提到：在 ECMAScript 操作解释字符串值的地方，每个元素都被解释为单个 UTF-16 代码单元。

Where ECMAScript operations interpret String values, each element is interpreted as a single UTF-16 code unit.

所以像💩 字符实际上占用了两个 UTF-16 的码元，也就是两个元素，所以它的 length 属性就是 2。（这跟一开始 JS 使用 USC-2 编码有关，当初以为 65536 个字符就可以满足所有需求了）

但对于普通用户而言，这就完全没办法理解了，为什么明明只填了一个 '𠮷'，程序上却提示占用了两个字符长度，要怎样才能正确识别出 Unicode 字符长度呢？

我在 Antd Form 表单使用的 async-validator 包中可以看到下面这段代码

js复制代码const spRegexp = /[\uD800-\uDBFF][\uDC00-\uDFFF]/g;  if (str) {   val = value.replace(spRegexp, '_').length; }


当需要进行字符串长度的判断时，会将码点范围在补充平面的字符全部替换为下划线，这样长度判断就和实际显示的一致了！！！

ES6 对 Unicode 的支持
length 属性的问题，主要还是最初设计 JS 这门语言的时候，没有考虑到会有这么多字符，认为两个字节就完全可以满足。所以不止是 length，字符串常见的一些操作在 Unicode 支持上也会表现异常。

下面的内容将介绍部分存在异常的 API 以及在 ES6 中如何正确处理这些问题。

for vs for of
例如使用 for 循环打印字符串，字符串会按照 JS 理解的每个“元素”遍历，辅助平面的字符将会被识别成两个“元素”，于是出现“乱码”。

js复制代码var str = '👻yo𠮷' for (var i = 0; i < str.length; i ++) {   console.log(str[i]) }  // -> � // -> � // -> y // -> o // -> � // -> �


而使用 ES6 的 for of 语法就不会。

js复制代码var str = '👻yo𠮷' for (const char of str) {   console.log(char) }  // -> 👻 // -> y // -> o // -> 𠮷


展开语法(Spread syntax)
前面提到了使用正则表达式，将辅助平面的字符替换的方式来统计字符长度。使用展开语法也可以得到同样的效果。

js复制代码[...'💩'].length // -> 1


slice, split, substr 等等方法也存在同样的问题。

正则表达式 u
ES6 中还针对 Unicode 字符增加了 u 描述符。

js复制代码/^.$/.test('👻') // -> false  /^.$/u.test('👻') // -> true


charCodeAt/codePointAt
对于字符串，我们还常用 charCodeAt 来获取 Code Point，对于 BMP 平面的字符是可以适用的，但是如果字符是辅助平面字符 charCodeAt 返回结果就只会是编码后第一个码元对于的数字。

js复制代码'羽'.charCodeAt(0) // -> 32701 '羽'.codePointAt(0) // -> 32701  '😸'.charCodeAt(0) // -> 55357 '😸'.codePointAt(0) // -> 128568


而使用 codePointAt 则可以将字符正确识别，并返回正确的码点。

String.prototype.normalize()
由于 JS 中将字符串理解成一串两个字节的码元序列，判断是否相等是根据序列的值来判断的。所以可能存在一些字符串看起来长得一模一样，但是字符串相等判断结果确是 false。

js复制代码'café' === 'café' // -> false


上面代码中第一个 café 是有 cafe 加上一个缩进的音标字符\u0301组成的，而第二个 café 则是由一个 caf + é 字符组成的。所以两者虽然看上去一样，但码点不一样，所以 JS 相等判断结果为 false。

js复制代码'cafe\u0301' // -> 'café'  'cafe\u0301'.length // -> 5  'café'.length // -> 4


为了能正确识别这种码点不一样，但是语意一样的字符串判断，ES6 增加了 String.prototype.normalize 方法。

js复制代码'cafe\u0301'.normalize() === 'café'.normalize() // -> true  'cafe\u0301'.normalize().length // -> 4

