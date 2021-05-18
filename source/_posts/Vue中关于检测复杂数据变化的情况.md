---
title: Vue中关于检测复杂数据变化的情况
date: 2020-07-06 23:13:02
tags: Vue
categories: Vue
---
## 前言
我们在vue项目的开发过程中会遇到，当我们的data对象是一个复杂对象的时候，举一个栗子，对象数组。也就是data是一个数组，数组中的每个元素是一个对象。然后当我们`this.data[i].a=2`给data中的某个元素的a属性赋值时，这时候vue是检测不到数据变化的，也就触发不了更新，所以很可能会出现，点击相关的改变数据的逻辑，出现的数据其实是上一次的数据。或者是点击改变数据的逻辑，没有什么变化，切出去，切回来，发现数据更新了的等等情况。这些大有可能是因为vue检测不到数据更新的情况。

对此我们有三种解决办法：
## this.$set,添加响应式属性
对于已经创建的实例，vue不允许动态添加跟级别的响应式property。但是，可以使用`vue.set(object,propertyName,value)`方法想嵌套对象添加响应式propety。例如：
```
Vue.set(vm.someObject,'b',2)
//或者
this.$set(vm.someObject,'b',2)
//在someObject中添加b属性
```
有时你可能需要为已有对象赋值多个新 `property`，比如使用` Object.assign() `或 `_.extend()`。但是，这样添加到对象上的新 `property` **不会触发更新**。在这种情况下，你应该用**原对象与要混合进去的对象**的 `propert`y 一起创建一个新的对象。
```
// 代替 `Object.assign(this.someObject, { a: 1, b: 2 })`
this.someObject=Object.assign({},this.someObject,{a:1,b:2})
```
## 第二种 对于数组
Vue不能检测以下数组的变动：
1. 当你利用索引直接设置一个数组项时，例如：`vm.items[indexOfItem] = newValue`

2. 当你修改数组的长度时，例如：`vm.items.length = newLength`


对于数组来说，我们在前言举到的栗子，我们不能使用this.data[i].a=2对于其a属性进行赋值，因为不会触发更新。我们可以这样来写:
```
let newData=[...this.data];
for(let i in newData){
    newData[i].a=2
}
this.data=newData;//这种方式来触发更新
```
当然针对数组，vue官方也提供了这两种方式：
```
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x' // 不是响应性的
vm.items.length = 2 // 不是响应性的

// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
vm.$set(vm.items, indexOfItem, newValue)

// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
vm.items.splice(newLength)
```

## 第三种 $forceUpdate()
当在data里没有显示的声明一个对象的属性，而是之后给该对象添加属性，这种情况vue是检测不到数据变化的，可以使用$forceUpdate()
```
html:

<span class="test">{{egData.value}}</span>
<el-button @click="changeData">改变</el-button>

js:
changeData () {
    this.egData.value = 'oldValue'
    this.$forceUpdate()  // dom会更新
}
```
但是这种做法并不推荐，官方说如果你现在的场景需要用`forceUpdate`方法 ,那么99%是你的操作有问题，如上data里不显示声明对象的属性，之后添加属性时正确的做法时用` vm.$set() `方法，所以`forceUpdate`请慎用


## 后记
既然讲到vue中关于监听复杂数据变化的示例，顺便讲一下v-model的原理，因为这两块一般会同时出现，因为当我们处理自定义组件的数据流的时候，会涉及到v-model，当数据是复杂数据时，就涉及到监听。
### 自定义组件v-model的原理
自定义事件也可以用来创建自定义表单输入组件，使用`v-model`来进行数据的双向绑定。
假如我们有一个组件A
```
//父组件引入
<A v-model="data"></A>
//A组件内部
<input :value="something" @change="e=>handlechange(e.target.value)">

handlechange(e){
    this.$emit('input',e)
}
//想让自定义组件的v-model生效，就是必须
//1.接受一个value属性
//2.在有新的value时触发input事件
//官方也说明了，v-moel只是一个语法糖，真正实现靠的是v-bind绑定响应数据和触发input事件并传递数据



//其实input的v-model也是这么实现的
<input v-model="something">
<input :value="something @input="something=$event.target.value">
```
有了v-model，可以大大方便我们进行自定义组件的开发，增加组件的可复用性和抽象性。创造出功能强大的动态表单组件


## 父子组件传值的方式

### props传值与$attrs
前面讲到v-model的原理，这其中当然就是组件中传值的问题。父子组件中的传值，我们都知道通过`props`和`$emit`来进行，当然，如果存在祖父组件要往孙子组件传值。这种多级组件传值，我们会面临一种情况，**vuex太重，props太麻烦，每个组件都要注册props**。

在vue2.4版本中提供了了v-bind="$attrs"，简单点讲`$attrs`是一个对象，黎曼包含了父组件在子组件设置的属性（除`props`传递的属性，`class`和`style`之外）
  使用案例：
```
<div id="app">
      <child1
        :p-child1="child1"
        :p-child2="child2"
        :p-child-attrs="1231"
        v-on:test1="onTest1"
        v-on:test2="onTest2">
      </child1>
    </div>
   <script>
      Vue.component("Child1", {
        inheritAttrs: true,
        props: ["pChild1"],
        template: `
        <div class="child-1">
        <p>in child1:</p>
        <p>props: {{pChild1}}</p>
        <p>$attrs: {{this.$attrs}}</p>/打印出来是包含p-child2和p-child-attrs的对象
        <hr>
        <child2 v-bind="$attrs" v-on="$listeners"></child2></div>`,
        mounted: function() {
          this.$emit("test1");
        }
      });
      Vue.component("Child2", {
        inheritAttrs: true,
        props: ["pChild2"],
        template: `
        <div class="child-2">
        <p>in child->child2:</p>
        <p>props: {{pChild2}}</p>
        <p>$attrs: {{this.$attrs}}</p>/
          <button @click="$emit('test2','按钮点击')">触发事件</button>
        <hr> </div>`,
        mounted: function() {
          this.$emit("test2");
        }
      });
      const app = new Vue({
        el: "#app",
        data: {
          child1: "pChild1的值",
          child2: "pChild2的值"
        },
        methods: {
          onTest1() {
            console.log("test1 running...");
          },
         onTest2(value) {
            console.log("test2 running..." + value);
          }
        }
      });
    </script>
```