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

