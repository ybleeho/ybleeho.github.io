---
title: Vue.js组件通信模式
date: 2019-04-22 18:05:20
tags:
---

有几种方法可用于Vue.js中的组件间通信。对于大多数情况，正常的情况下Props和Events应该足够了，但是也可以使用其他方法。

##### Props&Events
通常的通信方法涉及Props和Events。这种通用模式提供了一种强大的组件之间通信方式，而不会对涉及哪些组件产生任何依赖性或限制。

###### Props：
Props允许将任何数据类型传递给子组件，并允许控制组件接收的数据类型。支持更新也是被动的，允许子组件在父数据更改时更新。
模版用法：
```
<my-component v-bind:prop1="parentValue"></my-component>
<!-- Or more succinctly, -->
<my-component :prop1="parentValue"></my-component>
```
###### Events：
事件提供了一种向父组件通知子组件更改的方法。
```
<my-component v-on:myEvent="parentHandler"></my-component>
<!-- Or more succinctly, -->
<my-component @myEvent="parentHandler"></my-component>
```
发射一个事件：
```
export default {
  methods: {
    fireEvent() {
      this.$emit('myEvent', eventValueOne, eventValueTwo);
    }
  }
}
```
此外，还可以创建全局事件，以便在应用中的任何位置传递事件。
##### 绑定：
使用v-model允许将props与事件组合以进行双向绑定。这通常用于输入组件。
v-model假定值prop和input事件，但这可以自定义。
```
<my-component v-model="prop1"></my-component>
```
```
<my-checkbox v-model="foo" value="some value"></my-checkbox>
<!-- equivalent to: -->
<my-checkbox :checked="foo" @change="val => { foo = val }" value="some value"> </my-checkbox>
```
使用情况：需要在组件之间进行几乎任何类型的数据传递和消息传递。
##### Provide/Inject：
Vue的一个新增功能是Provice/Inject机制。它允许从祖先组件到其所有后代的数据或方法的选择性展示。虽然Provice/Inject本身不是反应性的，但它可以用于传递被动对象。
Provice/Inject可能不是一个开发一个应用程序好主意，但它可以在编写整个自定义呈现的组件库时非常方便。
具体用法：https://vuejs.org/v2/api/#provide-inject
##### 直接访问：

如果直接访问父或子组件上的属性或方法，你可以使用每个组件的this.$parent和this.$children属性来完全访问父组件和子组件上的所有内容。
然而，这绝对是一个可怕的想法。如果发现自己处于需要这样做的境地，那么有99.99958％的可能性做错了并且应该重构。
因为在父组件和子组件之间的标记中引入了实现和结构之间的直接耦合，使它们变得不灵活且容易破坏。
