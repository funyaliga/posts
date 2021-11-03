## Decorator装饰器

在不改变对象自身的基础上，在程序运行期间给对象增加职责。

相当于游戏的装备，可拆卸。相当于我们的大衣，冬天时穿，夏天时可脱掉



因为函数有遍历提升的问题，所以装饰器不能应用与函数上：

```javascript
// 这是不允许的
@decorator1
@decorator2
function xx() {
    log('xx')
}
```

