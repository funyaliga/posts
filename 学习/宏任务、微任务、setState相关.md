[toc]

### 宏任务（task ） ###

浏览器为了能够使得JS内部task与DOM任务能够有序的执行，会在一个task执行结束后，在下一个 task 执行开始前，对页面进行重新渲染 （task->渲染->task->...）

正常的异步任务都是宏任务，最常见的就是script、定时器（setInterval, setImmediate, setTimeout）、IO任务。

注：script就是js的整体代码，所以浏览器最先执行的是一个大的宏任务



### 微任务（Microtasks ） ###

微任务通常来说就是需要在当前 task 执行结束后立即执行的任务，比如对一系列动作做出反馈，或或者是需要异步的执行任务而又不需要分配一个新的 task，这样便可以减小一点性能的开销。只要执行栈中没有其他的js代码正在执行且每个宏任务执行完，微任务队列会立即执行。如果在微任务执行期间微任务队列加入了新的微任务，会将新的微任务加入队列尾部，之后也会被执行。

queueMicrotask、Promise/async属于微任务



相关文章：

https://juejin.cn/post/6894078909592109069
https://segmentfault.com/a/1190000014940904



```js
setTimeout(() => {
    console.log('轮到下一位客人'); // 宏任务
});

console.log('办理当前客人大爷的[存款]业务');

new Promise(resolve => {
  resolve()
  console.log('大爷：我还要办[信用卡]呀！！！！');
}).then(_ => {
  console.log('大爷的[信用卡]业务处理完毕'); // 微任务
})

console.log('大爷的[存款]业务处理完毕');

> 办理当前客人大爷的[存款]业务
> 大爷：我还要办[信用卡]呀！！！！
> 大爷的[存款]业务处理完毕
> 大爷的[信用卡]业务处理完毕
> 轮到下一位客人
```

![宏任务/微任务](/Users/junli/work/fun/posts/img/task.jpeg)





```js
// ----------- ↓↓↓↓ 例子1 ↓↓↓↓ -----------
console.log('aaa');
setTimeout(()=>console.log('t1'), 0);
(async ()=>{
  console.log(111);
  await console.log(222);
  console.log(333);
  setTimeout(()=>console.log('t2'), 0);
})().then(()=>{
  console.log(444);
});

console.log('bbb');

// ----------- ↓↓↓↓ 结果 ↓↓↓↓ -----------
> aaa
> 111
> 222
> bbb
> 333
> 444
> t1
> t2

// ----------- ↓↓↓↓ 讲解 ↓↓↓↓ -----------
console.log('aaa'); // 正常队列-1
setTimeout(()=> console.log('t1'), 0); // 宏任务-1
(async ()=>{
  console.log(111); // 正常队列-2
  await console.log(222); // 正常队列-3
  console.log(333); // 微任务-1
  setTimeout(()=> console.log('t2'), 0); // 宏任务-2
})().then(()=>{
  console.log(444); // 微任务-2
});

console.log('bbb'); // 正常队列-3
```



```js
// ----------- ↓↓↓↓ 例子2 ↓↓↓↓ -----------
setTimeout(() => {
  console.log("1-1");
  Promise.resolve().then(() => {
    console.log("2-1");
  });
});
console.log("1-2");
Promise.resolve().then(() => {
  console.log("1-3");
  setTimeout(() => {
    console.log("3-1");
  });
});

// ----------- ↓↓↓↓ 结果 ↓↓↓↓ -----------
> 1-2
> 1-3
> 1-1
> 2-1
> 3-1

// ----------- ↓↓↓↓ 讲解 ↓↓↓↓ -----------
setTimeout(() => {
  console.log("1-1"); // 宏任务-1
  Promise.resolve().then(() => {
    console.log("2-1"); // 宏任务-1的微任务-1
  });
});
console.log("1-2"); // 正常序列-1
Promise.resolve().then(() => {
  console.log("1-3"); // 微任务-1
  setTimeout(() => {
    console.log("3-1"); // 宏任务-2
  });
});
```





### setState是宏任务还是微任务 ###

https://juejin.cn/post/6992006476558499853

结论：都不属于。

- 如果 `setState` 处于react的合成事件里，`setState` 属于伪异步，他处于当前宏任务正常序列后面，微任务和下一个宏任务前面。
  即：当前宏任务 -> setState并重新render -> 微任务 -> 下一个宏任务
- 如果 `setState` 脱离react流，比如处于原生事件(addEventListener)、setTimeout/setInterval等，`setState` 属于同步，当前宏任务正常执行。
  即：setState前的序列 ->  setState并重新render -> setState前的序列 -> 微任务 -> 下一个宏任务


```react
class App extends React.Component {
  constructor() {
    super(...arguments);

    this.onClick = this.onClick.bind(this);
    this.onClickLater = this.onClickLater.bind(this);

    this.state = {
      count: 0
    };
  }
  
  componentDidMount() {
    document.querySelector('#btn-raw').addEventListener('click', this.onClick);
  }
  
  onClickLater() { setTimeout(() => { this.onClick(); }); }


  onClick() {
    console.log('开始运行', this.state.count)
    setTimeout(() => {
      console.log('宏任务触发', this.state.count)
    })
    Promise.resolve().then(() => {
      console.log('微任务触发', this.state.count)
    })
    this.setState({
      count: this.state.count + 1
    }, () => {
      console.log('setState回调', this.state.count)
    })
    console.log('结束运行', this.state.count)
  }


  render() {
    console.log('--渲染--', this.state.count);
    return (
      <div>
        <div>{this.state.count}
          <button onClick={this.onClick}>react合成事件-Increment</button>
          <button id="btn-raw">原生事件-Increment</button>
          <button onClick={this.onClickLater}>setTimeout-Increment</button>
        </div>
      </div>
    )
  }
}

ReactDOM.render(<App />, document.querySelector('#container'));

// react合成事件
> 开始执行 0
> 结束执行 0
> --渲染-- 1
> setState回调 1
> 微任务触发 1
> 宏任务触发 1

// 原生事件 or setTimeout
> 开始执行 0
> --渲染-- 1
> setState回调 1
> 结束执行 1
> 微任务触发 1
> 宏任务触发 1