[TOC]



## apply,call,binD ##

* `.apply(thisObj, [arg1, arg2, …])`  
  - 接受类数组参数
  - 立即执行函数
* `.call(thisObj, arg1, arg2, …)`  
  - 接受连续参数
  - 立即执行函数
* `.bind(thisObj, arg1, arg2, …)`
  - 接受连续参数
  - 不会立即执行函数
  - 用bind指向过this后，再用apply/call/bind不会修改this

```javascript
const add = (a, b, c) => a + b + c
add.apply(null, [1, 2, 3]) // 6
add.call(null, 1, 2, 3) // 6
add.bind(null, 1, 2, 3)() // 6
```

```javascript
// 用bind指向过this后，再用apply/call/bind不会修改this
var fn = Array.prototype.join.bind([1, 2, 3])
fn.apply([2, 3, 4]) // 1,2,3
fn.call([2, 3, 4]) // 1,2,3
```



## fun.apply(thisArg, [argsArray]) 

链接： [https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)

#### argsArray

一个数组或者 **类数组** 对象



#### 类数组

带有 **`length` 属性** 和 **`[0...length)`范围的整数属性**



```javascript
Math.max.apply(null, [1, 2, 3, 4]); // 4
Math.max.apply(null, {0:1, 1:2, 2: 3, 3: 4, length:4}) // 4 (类数组)

Array.prototype.slice.apply([1, 2, 3, 4]) // [1, 2, 3, 4]
Array.prototype.slice.apply({0:1, 1:2, 2: 3, 3: 4, length:4}) // [1, 2, 3, 4]
```



## 重写 ##

#### apply ####

```javascript
Function.prototype.myApply = function (newThis = window, args) {
  var key = Symbol();
  newThis[key] = this; // 把fn绑在新this上，下面链式调用fn时，this会指向这个新this。
  var res = newThis[key](...args);
  delete newThis[key];
  return res;
}

var name = 'tom'
var obj = { name: 'mary' };
function fn(age, sex) {
  console.log(`${this.name}, ${age}, ${sex}`)
};
fn.myApply(obj, ['18岁', '男']); // mary, 18岁, 男
```



#### call ####

```javascript
Function.prototype.myCall = function (newThis = window, ...args) {
  var key = Symbol();
  newThis[key] = this; // 把fn绑在新this上，下面链式调用fn时，this会指向这个新this。
  var res = newThis[key](...args);
  delete newThis[key];
  return res;
}

var name = 'tom'
var obj = { name: 'mary' };
function fn(age, sex) {
  console.log(`${this.name}, ${age}, ${sex}`)
};
fn.myCall(obj, '18岁', '男'); // mary, 18岁, 男
```



#### bind

```javascript
 Function.prototype.myBind = function (newThis = window) {
   const fn = this;
   return function () {
     fn.apply(newThis, arguments)
   }
 }

var name = 'tom'
var obj = { name: 'mary' };
function fn(age, sex) {
  console.log(`${this.name}, ${age}, ${sex}`)
};
fn.myBind(obj)('18岁', '男'); // mary, 18岁, 男
```

