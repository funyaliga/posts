一些名词概念：

- `MemberExpression`：成员表达式节点，简单理解就是括号()左边的部分。比如foo()是foo, foo.bar()是foo.bar
- `Reference type`：引用类型，



简图

![this指向](/Users/junli/work/fun/posts/img/this指向.png)

##### this指向优先级

1. `new func()` 绑定新对象

2. `bind/call/apply` 绑定指定的对象

3. 绑定上下文

   ```
   var a = {
       b: function () {
           //this -> a
       }
   }
   ```

4. 默认`window`



##### 函数里的this #####

函数里的this取决于调用函数的方式，而不是定义方式。

函数调用时，如果当前是某个对象的属性访问器时，this为该对象。

否则this不存在，其值会被隐式转换为全局对象。

<font color=red>*注：第5版的ECMAScript中（即严格模式use strict），已经不强迫转换成全局变量了，而是赋值为undefined，可使用`.bind(this)`指向window。*</font>

```javascript
function fn() {
  console.log(this)
}
fn() // window  (严格模式下是undefined)
fn.prototype.constructor() // fn.prototype
```

```javascript
var obj = {
  fn: function () {
    console.log(this);
  }
};
var obj2 = { name: '2' };
obj2.fn = obj.fn

obj.fn(); // obj 
obj['fn'](); // obj
obj2.fn(); // obj2
var fn = obj.fn;
fn(); // Window  (严格模式下是undefined)
```

```javascript
function foo() {
  function bar() {
    console.log(this); // window
  }
  bar();
}
foo();
```



##### 箭头函数的this #####

箭头函数的this是捕获其所在上下文的 this 值。

```javascript
var name = 'win'
var obj = {
  name: 'obj',
  fn() {
    return function() {
      console.log(this.name); // 普通函数还是按上面逻辑
    }
  }
}

obj.fn()(); // win


var name = 'win'
var obj = {
  name: 'obj',
  fn() {
    return () => {
      console.log(this.name); // 箭头函数捕获其所在上下文的 this 值， 所以这里的this是指obj
    }
  }
}

obj.fn()(); // obj
```





##### 一些注意的

1. 不能通过赋值修改this的值，会直接报错。

2. 如果this指向`window`, 修改this会直接修改`window`的属性

   ```javascript
   var a = 1;
   const fn = function() {
     this.a = 2;
     console.log(this.a);
   }
   
   console.log(a); // 1
   fn(); // 2
   console.log(a); // 2
   ```

3. 匿名函数都是默认指向`window`。

   ```javascript
   var obj = {
     fun1:function(){
       console.log(this) // obj
     },
     fun2:function(callback){
       callback()
     }
   }
   obj.fun1()
   obj.fun2(function () {
     console.log(this) // Window
   })
   ```

   ```javascript
   setTimeout(function () {
     console.log(this) // Window
   }, 100);
   ```

   

   <font color=red>但有些API内部改变了this指向，比如`addEventListener`。在[`polyfill`](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)可见this通过call改变指向(polyfill仅参考，真正的实现方式不知道)</font>

   ```javascript
   document.addEventListener('click', function() {
     console.log(this) // document
   })
   ```

