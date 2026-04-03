---
description: 前端面试复习-vue
title: 前端面试复习一
readingTime: false
tag:
 - 前端基础
recommend: 1
---

* style属性加scoped的作用？

  为当前单文件的html添加属性选择器，再通过css属性选择器对css选择器做隔离，避免污染全局环境。

* vue3新增的hooks与vue2中的mixin有何相同和不同?

  在vue中，hooks和vue都可以用来对代码进行复用，扩展组件的功能。不同之处在于hooks来源清晰，可以追查定义和实现，并且拥有独立作用域，不会污染当前环境，缺点在于需要单独引入，使用是较为繁琐。而mixin无法轻易追查方法和变量的定义，组件内的混入会覆盖引入的方法和变量，有点在于能简单的复用代码，对于一些全局方法更好用。

* vue3中如何操作dom元素？

  1可以直接使用dom方法,如document.getElementById

  2 推荐使用ref，与vue2不同之处在于申明为ref(null)即可指定为选择dom元素或组件。
```
<template>

  <div ref="divRef"></div>

</template>

<script setup>

  //导入ref

//...

  const divRef = ref(null)

  console.log(divRef)

</script>
```
* vue2和vue3的双向绑定机制是如何实现的？

  在vue2中，双向绑定使用Object.defineProperty来劫持对象属性的get和set方法，当获取和赋值时执行相应的操作，缺点在于无法动态的添加属性，所以如果设置新属性或对引用类型赋值，如整个对象或数组，会导致无法检测。

  在vue3中，通过对象代理proxy方法来实现，通过对象的代理实现对get和set方法进行监听，这是es6新方法，也应如此无法兼容ie9及以下浏览器，优点在于可以随意添加属性，修改数组内的元素。

* vue-router中params和query的区别？

  query类似于传统的Url params方式传参，类型于?a=1，可用过$route获取,也可通过js解析url获取，params用于设置路由定义时的占位参数，也可以通过编程方式获取，但一般只通过vue$route方法获取。

* vuex和pinia有何区别?

  pinia就是vuex的最新版本，他们的作用一致，用于在全局或不同组件间共享响应式的状态。pinia可以兼容旧vuex的语法，也可使用类似vue3的steup语法。

  不同之处:1 pinia没有mutations,简单修改可直接修改，复杂操作可使用$patch或actions提交

          2 pinia有更好的类型推断，对typesctipt兼容更好

          3 piana可直接基于不同的文件创建单独的共享存储，而不必全部挂载到同一个vuex中再划分模块。

* v-show和v-if有什么不同?

  v-show简单的使用dispaly:none来切换显示和隐藏，不会触发组件的生命周期，v-if会将元素dom移出，会触发组件的生命周期，v-if是惰性的，初始未false时不会执行。v-if有更高的切换开销，v-show更适合在需要频繁切换的时候使用和可对子元素于渲染。

* vue父子元素生命周期的执行顺序是？

  生成时，父beforeCreated-> 父created -> 子beforeCreated> 子created -> 子mounted -> 父mounted，销毁时父beforeDestroy->子beforeDestroy->子destroyed->父destroyed，总的来说父组件先执行，等到子组件执行完成，父组件收尾。

* vue不同组件之间通信有哪些方式？

  1 父子组件通过props和emit通信，兄弟组件之间也可通过父组件转发。

  2 可以使用vuex和piain全局管理，并监听属性值变化的方式可通信。

  3 可以注册一个vue实例使之成为事件中心。

* 如何理解v-for中key的原理和作用？如何设置key?为何最好不要用index作为key？

  key的作用是为节点标识唯一值，便于在更新列表时查找所需要更新的节点和复用dom元素，key值最好设置唯一性id，例如文章或用户的uuid，如果使用index作为key,再对列表进行破坏顺序的操作时会导致没有必要的dom更新，若列表中包含输入性操作，还会导致输入错位。

* vite相当于webpack有何不同?

  Vite和webpack都是前端构建工具，用于将vue js html css打包成生成文件，并可辅助开发。都可以通过各种扩展功能，支持各种文件类型。vite使用了全新的es6 modules语法，在开发模式下，利用浏览器的支持实现动态构建所需要的文件，所以启动速度极快。

* computed和watch的区别和运用场景？

  computed是底层就是一个特殊的watch，在watch之后做了缓存，当依赖的属性未改变时不会触发更新。watch就是在双向绑定的基础上，给数据添加一个监听器，用于执行一些异步回调。

  运用场景：computed用于需要对数据进行二次处理的时候，例如筛选，求和等，尤其是需求较为复杂，在模板中使用时会导致可读性较差的情况。watch用于不仅仅需要对数据进行操作，还要进行一些异步操作时进行使用，例如动态搜索。

