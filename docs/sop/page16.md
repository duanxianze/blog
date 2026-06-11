---
description: 前端基础知识
title: immer 不可变数据的使用
readingTime: true
tag:
 - 前端基础
recommend: 1
---

# 介绍

>官网：https://immerjs.github.io/immer/

immer 是一个用于处理不可变数据的 JavaScript 库。它的核心概念是：通过创建新的数据对象来修改数据，而不是直接修改原始数据。

## 由来

在开发时，我们通常会通过修改数据来更新状态，但是js的对象是引用的，修改时，引用类型经常会产生一些无法意识到的副作用，例如创建一个Echarts的Options对象，同一个Options用于不同的图表，因为对象层级过深，容易导致修改之后应用于所有图表，从而导致数据错误。

```
const options = {
    name: "折线图",
    series: [],
    xAxis: [],
    yAxis: []
}

let chart1 = echarts.init(document.getElementById('chart1'));
options.name = "饼图";
chart1.setOption(options);

let chart2 = echarts.init(document.getElementById('chart2'));
// chart2的数据也会改变
chart2.setOption(options);
```

传统上，一般使用深拷贝来解决这个问题，但是深拷贝会消耗大量内存，且效率较低。
```
let options1 = JSON.parse(JSON.stringify(options));
chart1.setOption(options);
let options2 = JSON.parse(JSON.stringify(options));
chart2.setOption(options);

// 或者使用lodash的cloneDeep
let options1 = _.cloneDeep(options);
chart1.setOption(options);
// chart2的数据不会改变
let options2 = _.cloneDeep(options);
chart2.setOption(options2);
```

## 解决方案

通过immer，我们可以通过修改数据来更新状态，而不需要创建新的数据对象。同时，有比深拷贝更高的性能，且更简单

```
import { produce } from 'immer'

const options = {
    name: "折线图",
    series: [],
    xAxis: [],
    yAxis: []
}
let chart1 = echarts.init(document.getElementById('chart1'));
let options1 = produce(options, draft => {
    draft.name = "饼图";
})
chart1.setOption(options1);
let chart2 = echarts.init(document.getElementById('chart2'));
// 核心代码，使用immer
let options2 = produce(options, draft => {
    draft.name = "饼图2";
})
chart2.setOption(options2);
```

## 优点

1. 更加简单，使用起来更方便。
2. 运行速度更快，因为不需要创建新的数据对象。
3. 更加安全，因为 immer 会检查数据的变化，并确保数据的正确性。
4. 兼容性更好，因为immer 支持所有 JavaScript 数据类型，包括对象、数组、Map、Set 等。


