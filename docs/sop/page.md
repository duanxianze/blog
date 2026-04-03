---
description: 微信小程序数字输入限制
title: 微信小程序数字输入限制
readingTime: false
tag:
 - 小程序
recommend: 3
---

使用原生input组件，type="digit"

```js
function inputTem(e) {
  let val = e.detail.value;
  let num = val.toString(); //先转换成字符串类型
  if (num.indexOf('.') == 0) { //第一位就是 .
    num = '1' + num
  }
  num = num.replace(/[^\d.]/g, ""); //清除“数字”和“.”以外的字符
  num = num.replace(/\.{1,}/g, "."); //只保留第一个. 清除多余的
  num = num.replace(".", "$#$").replace(/\./g, "").replace("$#$", ".");
  num = num.replace(/^(\-)*(\d+)\.(\d).*$/, '$1$2.$3'); //只能输入一个小数 
// num = num.replace(/^(\-)*(\d+)\.(\d\d).*$/, '$1$2.$3'); //只能输入两个小数
  if (num.indexOf(".") < 0 && num != "") {
    num = parseFloat(num);
  }
  return num
}
```