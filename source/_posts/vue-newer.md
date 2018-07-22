title: vue 初探
date: 2018-02-25 21:49:30
tags: vue
---
vue初学，读文档总结，间有一些思考和与React的比较
<!-- more -->
## 计算属性
计算属性方式：
```javascript
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // a computed getter
    reversedMessage: function () {
      // `this` points to the vm instance
      return this.message.split('').reverse().join('')
    }
  }
})
```
method方式:
```javascript
<p>Reversed message: "{{ reversedMessage() }}"</p>
methods: {
  reversedMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```
一个强大的功能，将对某数据的处理函数作为该属性的get函数。简单应用场景下，利用缓存，可以替代method，并且，vue.js会根据数据是否变化来判断是否执行该函数，减少执行次数。但因此也会有缓存问题，如果不希望利用缓存，使用method方法

## class与style绑定
1. 都可以使用对象和数组的方式写入，对象方式可以根据key的value的布尔值，来确定是否使用该class，实现了classname的功能，比较好用
2. 绑定style时，vue.js会自动添加前缀。
>当 v-bind:style 使用需要特定前缀的 CSS 属性时，如 transform ，Vue.js 会自动侦测并添加相应的前缀。


## 列表渲染
1. `v-for` 用于对象，数组的遍历渲染，替代 map函数

## 条件渲染
1. 给出 `v-if`,`v-else`,`v-if-else`,不用在return中包裹三目运算符，或者写很多个return了
2. `v-show`和`v-if`效果相同，唯一的区别是前者会做dom渲染，只是控制display属性，后者则完全不做渲染，看实际需求使用 
3. 元素高效复用，不会重新渲染，比如input，若切换tab，不想共用之前的input填值，给元素设置不同的key

## 使用-v-on-绑定自定义事件
父组件可以在使用子组件的地方直接用 `v-on` 来监听子组件触发的事件。
```html
<div id="counter-event-example">
  <p>{{ total }}</p>
  <button-counter v-on:increment="incrementTotal"></button-counter>
  <button-counter v-on:increment="incrementTotal"></button-counter>
</div>
```
```javascript
//子组件
Vue.component('button-counter', {
  template: '<button v-on:click="incrementCounter">{{ counter }}</button>',
  data: function () {
    return {
      counter: 0
    }
  },
  methods: {
    incrementCounter: function () {
      this.counter += 1
      this.$emit('increment')
    }
  },
})
//父组件
new Vue({
  el: '#counter-event-example',
  data: {
    total: 0
  },
  methods: {
    incrementTotal: function () {
      this.total += 1
    }
  }
})
```
在子组件中，给button的click事件绑定了一个自定义函数，函数中触发另一个叫‘increment’的函数
在父组件中使用`v-on`监听了‘increment’，执行`incrementTotal`函数
这种方式比react将父组件的函数以props的方式传递下去，在子组件的对应触发函数中，使用 `this.props.fun()`来执行感觉高明许多，至少从代码上看，简洁明了，减少了一些props的传递
## 组件
组件分为全局注册和局部注册，全局注册就是根组件，局部的就是父组件中包含的子组件，常用于公共组件。区别于react的直接引用，vue需要注册后，用`<xx-component>` 标签的方式引用

注册方式如下：
1. 全局
```
// 注册
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})

// 创建根实例
new Vue({
  el: '#example'
})
// 使用
<div id="example">
  <my-component></my-component>
</div>

```
渲染为：
```
<div id="example">
  <div>A custom component!</div>
</div>
```
2. 局部
```
var Child = {
  template: '<div>A custom component!</div>'
}

new Vue({
  // ...
  components: {
    // <my-component> 将只在父组件模板中可用
    'my-component': Child
  }
})
```
## 动态prop
`v-bind`

与绑定到任何普通的 HTML 特性相类似，我们可以用 v-bind 来动态地将 prop 绑定到父组件的数据。每当父组件的数据变化时，该变化也会传导给子组件
## 插槽
使用特定标签`<slot>`,分为单个和具名两种
除非子组件模板包含至少一个 <slot> 插口，否则父组件的内容将会被丢弃。当子组件模板只有一个没有属性的插槽时，父组件传入的整个内容片段将插入到插槽所在的 DOM 位置，并替换掉插槽标签本身。可用于占位。loading状态，错误信息的处理。与react的不同在于，react需要使用三目运算符，根据状态判断加载哪段dom，加载和错误状态都是写在一个组件中的。而插槽需要写在子组件中，内容由父组件传入，用法其实类似react的`children`。区别在于

用法如下
```
// my-component
<div>
  <h2>我是子组件的标题</h2>
  <slot>
    只有在没有要分发的内容时才会显示。
  </slot>
</div>

//父组件
<div>
  <h1>我是父组件的标题</h1>
  <my-component>
    <p>这是一些初始内容</p>
    <p>这是更多的初始内容</p>
  </my-component>
</div>
```


