[TOC]

## 虚拟DOM ##

虚拟dom本质上就是一个js对象，该对象描述了一个节点对应的一些必要信息。
(目前所有前端框架的虚拟dom都是对象，你也可以用其他东西代表虚拟dom)

真实dom: `<div classs="hola-text">你好</div>`

虚拟dom:

```javascript
const virtualNode = {
	type: 'div',
    content: '你好',
    props: {
        class: 'hola-text',
    },
}
```





## diff算法

https://www.bilibili.com/video/BV1dv411g7JQ

前端的diff其实就是比较虚拟dom或虚拟dom树的差异，然后根据差异点去更新对应的虚拟dom



**为什么是去比较虚拟dom而不是真实dom？**

正常情况：diff去比较真实dom -> 找出差异点 (需要更新的真实dom)  -> render。

虚拟dom情况：diff去比较虚拟dom -> 找出差异点 (需要更新的虚拟dom) -> 真实dom -> render

这两种方式主要是每次比较两个不同的dom树找不同时，消耗时间不同，

虚拟dom对象的数据很少（可看上面虚拟dom的文章）

但真实dom的对象携带数据非常多(控制台随便打印一个真实dom查看即可)







## 类组件和函数组件之间的区别 ##

| 区别               | 函数组件 | 类组件 |
| ------------------ | -------- | ------ |
| 是否有 `this`      | 没有     | 有     |
| 是否有生命周期     | 没有     | 有     |
| 是否有状态 `state` | 没有     | 有     |

简单来说就是函数组件没有this，没有生命周期，没有状态state



## refs ##

Refs 提供了一种方式，用于访问在 render 方法中创建的 React 元素或 DOM 节点。



## React Hooks

弥补了函数组件没有生命周期和state这些缺少的功能。



#### setState为什么用数组而不是对象

方便元素命名，降低复杂度，如果是对象：

`const { state: counter, setState: setCounter } = useState(0)  `





## Redux

Redux 提供了一个叫 store 的统一仓储库，组件通过 dispatch 将 state 直接传入store，不用通过其他的组件。



