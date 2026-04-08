---
description: Echarts中绘制区域标准值标线时coord属性用法
title: Echarts中绘制区域标准值标线时coord属性用法
readingTime: false
tag:
 - ECharts
recommend: 2
---

```js
import { onMounted, onUnmounted, nextTick } from 'vue'

export function useEchartsResize(chartInstances) {
  const debounce = (fn, delay) => {
    let timer
    return function () {
      if (timer) {
        clearTimeout(timer)
      }
      timer = setTimeout(() => {
        fn()
      }, delay)
    }
  }

  const handleResize = () => {
    nextTick(() => {
      // 支持函数形式获取图表实例
      const charts = typeof chartInstances === 'function' ? chartInstances() : chartInstances
      if (Array.isArray(charts)) {
        charts.forEach(it => {
          const chart = typeof it === 'function' ? it() : it;
          if (chart && typeof chart.resize === 'function') {
            chart.resize()
          }
        })
      } else if (charts && typeof charts.resize === 'function') {
        charts.resize()
      }
    })
  }

  const debouncedResize = debounce(handleResize, 100)

  onMounted(() => {
    window.addEventListener('resize', debouncedResize)
  })

  onUnmounted(() => {
    window.removeEventListener('resize', debouncedResize)
  })

  return {
    handleResize: debouncedResize
  }
}
```

使用方法

```js
import { useEchartsResize } from "./useEchartsResize";
const chart = shallowRef(null);

useEchartsResize(() => chart.value);
```