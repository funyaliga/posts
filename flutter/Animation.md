[toc]

## 隐式动画

指自动完成的动画，当改变部件的动画属性值时，框架会自动计算出一个从旧值过渡到新值的动画。

### AnimatedContainer

这个只是其中一个例子，他有各种各样的，比如`AnimatedOpacity`, `AnimatedPadding`, `AnimatedPositioned`, `AnimatedSwitcher`， `AnimatedAlign` 等。

这些控件比正常的多了个`duration`（动画持续时间）和 `curve`（曲线）、`onEnd`（动画结束回调）

```dart
// demo
class Test extends StatefulWidget {
  Test({Key key}) : super(key: key);

  @override
  _TestState createState() => _TestState();
}

class _TestState extends State<Test> {
  Color color = Colors.blue[100];

  @override
  Widget build(BuildContext context){
    return Scaffold(
      body: Center(
        child: AnimatedContainer(
          width: 300,
          height: 300,
          color: color,
          curve: Curves.easeInBack, // 动画曲线
          duration: Duration(milliseconds: 600), // 动画持续600毫秒
          onEnd: () { print('动画结束'); },
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          setState(() {
            color = Color.fromARGB(255, Random().nextInt(256)+0, Random().nextInt(256)+0, Random().nextInt(256)+0); // 随机颜色
          });
        },
        child: Icon(Icons.add),
      ),
    );
  }
}
```



#### AnimatedSwitcher

```dart
const AnimatedSwitcher({
  Key key,
  this.child,
  @required this.duration, //动画持续时间
  this.reverseDuration,// 旧child隐藏的动画时长
  this.switchInCurve = Curves.linear, // 新child显示的动画曲线
  this.switchOutCurve = Curves.linear,// 旧child隐藏的动画曲线
  this.transitionBuilder = AnimatedSwitcher.defaultTransitionBuilder, // 动画构建器
  this.layoutBuilder = AnimatedSwitcher.defaultLayoutBuilder, //布局构建器
})
```



当`child`发生改变时（类型或Key不同），旧child会执行隐藏动画，新child会执行执行显示动画

```dart
// demo
class Test extends StatefulWidget {
  @override
  _TestState createState() => _TestState();
}

class _TestState extends State<Test> {

  int _count = 0;

  @override
  Widget build(BuildContext context){
    return new Scaffold(
      body: new Center(
        child: Container(
          width: 300,
          height: 300,
          color: Colors.blue[400],
          child: AnimatedSwitcher(
            duration: Duration(milliseconds: 1000),
            transitionBuilder: (Widget child, Animation<double> animation) {
              return ScaleTransition( // 默认是FadeTransition
                scale: animation,
                child: child
              );
            },
            child: Text(
              '$_count',
              key: ValueKey<int>(_count), // 通过不同key知道发生改变，或者用UniqueKey()唯一key替代
              style: TextStyle(fontSize: 40)
            ),
          ),
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: () {
          setState(() {  _count += 1; });
        },
        child: Icon(Icons.add),
      ),
    );
  }
}
```



#### TweenAnimationBuilder ####

```dart
TweenAnimationBuilder({
  Key key, 
  @required Tween<T> tween, // 动画的参数范围，
  @required Duration duration,  // 动画持续时间
  Curve curve: Curves.linear,  // 动画曲线
  @required ValueWidgetBuilder<T> builder,  // (context, value, child)
  VoidCallback onEnd, // 动画结束回调
  Widget child // child，用于优化，不参与动画的部分
})
```



`tween`:  

> 如果没有设置`begin`，`begin`=`end`
>
> `begin`只在初始化有用，后续不会因为`begin`改变而变化。
>
> 每次改变`end`值时，动画都会从当前值往最新的`end`值变化



类型指定：

```dart
// 方法1
TweenAnimationBuilder<double>(
  tween: Tween(begin: 300, end: 400),
)
TweenAnimationBuilder<Color>(
  tween: ColorTween(begin: Colors.red, end: Colors.blue),
)
  
// 方法2，直接写明类型
TweenAnimationBuilder(
  tween: Tween(begin: 300.0, end: 400.0),
)
TweenAnimationBuilder(
  tween: Tween(begin: Offset(0.0, -10), end: Offset(0.0, 40)),
)
```

```dart
// demo
TweenAnimationBuilder(
  duration: Duration(milliseconds: 600),
  tween: ColorTween(begin: Colors.red, end: Colors.black),
  builder: (BuildContext context, value, Widget child) {
    return Container(
      width: 300,
      height: 400,
      color: value,
      child: child,
    );
  },
  child: Text('不变'),
)
```





## 显示动画

显式动画指的是需要手动设置动画的时间，运动曲线，取值范围的动画。

