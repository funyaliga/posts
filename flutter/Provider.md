## Provider ##

最基础的Provider，他只给`widget`提供值，但不会因值发生改变时更新`widget`。

```dart
// 创建model
class CounterModel {
  CounterModel({this.counter=0});

  int counter = 0;

  Future<void> incrementCounter() async {
    await Future.delayed(Duration(seconds: 1));
    counter++;
    print(counter);
  }
}

void main() {
  runApp(
    Provider( // 创建基础Provider
      create: (_) => CounterModel(),
      child: MaterialApp(
        home: MyApp(),
      )
    )
  );
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    // 通过Provider.of获取CounterModel数据
    CounterModel _model = Provider.of<CounterModel>(context);

    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text('counter：${_model.counter}'), // 显示counter的值
            RaisedButton(
              child: Text("+"),
              onPressed: () {
                _model.incrementCounter(); // 这里能累计counter，但ui不会更新
              },
            ),
          ],
        ),
      )
    );
  }
}
```



## ChangeNotifierProvider ##

`ChangeNotifierProvider`会监听其`model`的更改。当值更改后，所涉及的`widget`将刷新。



#### ChangeNotifier

创建监听器的model，订阅变化。其中`notifyListeners()`是通知 UI 刷新。

```dart
class CounterModel extends ChangeNotifier {
  CounterModel({this.counter=0});

  int counter = 0;

  Future<void> incrementCounter() async {
    await Future.delayed(Duration(seconds: 1));
    counter++;
    print(counter);
    notifyListeners();
  }
}
```

将widget替换成`ChangeNotifierProvider`:

```dart
void main() {
  runApp(
    ChangeNotifierProvider(
      create: (context) => CounterModel(),
      child: MaterialApp(
        home: MyApp(),
      )
    )
  );
}
```



## MultiProvider ##

嵌套多个`Provider`

```dart
void main() {
  runApp(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (context) => CounterModel()),
        ChangeNotifierProvider(create: (context) => AuthModel()),
      ],
      child: MaterialApp(
        home: MyApp(),
      )
    )
  );
}
```



## ProxyProvider<T, R> ##

依赖于`Provider` 的 `Provider`。

`create` 初始化，`update`监听其他`Provider`更新

```dart
class CounterModel extends ChangeNotifier {
  CounterModel({this.counter=0});

  int counter = 0;

  Future<void> incrementCounter() async {
    await Future.delayed(Duration(seconds: 1));
    counter++;
    print(counter);
    notifyListeners();
  }
}

class TextModel {
  const TextModel(this._value);
  final int _value;
  String get value => 'counter: $_value';
}

ProxyProvider<CounterModel, TextModel>(
  create: TextModel(0),
  update: (context, counter, text) => TextModel(counter.counter),
);
```

他有很多变种体，`ProxyProvider2<ModelA, ModelB, ResultModel>`，`ProxyProvider3<ModelA, ModelB, ModelC, ResultModel>` ......`ProxyProvider6<ModelA, ModelB, ..., ModelF, ResultModel>`。这些其实都是`ProxyProvider0`的语法糖。

比如`ProxyProvider<ModelA, ResultModel>` 等于：

```dart
ProxyProvider0<ResultModel>(
  update: (context, result) {
    final modelA = Provider.of<ModelA>(context);
    return update(context, modelA, result);
  }
)
```

`ProxyProvider2<ModelA, ModelB, ModelResult>`等于

```dart
ProxyProvider0<Result>(
  update: (context, result) {
    final modelA = Provider.of<ModelA>(context);
    final modelB = Provider.of<ModelB>(context);
    return update(context, modelA, modelB, result);
  }
);
```



还有其他变种 `ChangeNotifierProxyProvider`、`ListenableProxyProvider` 等



## Provider.of<T>(context) ##

在`widget` 里获取model对象的引用。其中`listen`是可选的（默认为true），数据更新会导致context 页面范围的刷新，当`listen`为false时，则不会触发页面刷新。

```dart
Provider.of<ViewModel>(context, listen: false)
```



在生命周期的`initState` 或 `didChangeDependencies`中，获取Provider 时应关闭监听

```dart
initState() {
    super.initState();
    print(context.read<CounterModel>().counter);
}

// or 

initState() {
    super.initState();
    print(Provider.of<CounterModel>(context, listen: false).counter);
}
```



## Consumer<T> ##

在小部件调用`Provider.of`，泛型`T`代表了获取哪个Model

```dart
Consumer<T>(
   builder: (BuildContext context, T model, Widget child) => Fn,
   child: Widget
}
```

其中`child` 部件是在该Model更新时，不进行重构。

```dart
Consumer<CounterModel>(
  builder: (context, counter, child) {
    return Column(
      children: [
        Text(counter.counter.toString()),
        child,
      ],
    );
  },
  child: Text('我不会因为CounterModel更新而刷新'),
),
```



需要获取多个Model时，可用`Consumer2<A,B>`、`Consumer3<A,B,C>`、......、`Consumer6<A,B,C,D,E,F>`，最多6个



## Selector<T, S>##

类似`Cosumer`，提供某些值不变的情况下，不rebuild

```dart
Selector<T, S>(
   selector: (context, T) => S,
   shouldRebuild: (previous, next) => bool,
   builder: (context, S, child) => Fn
   child: Widget
}
```

`Selector<T, S>` ：`T`是model的类型， `S`是`selector` 中返回值类型。

`selector`：返回我们要操作的`S`

`shouldRebuild`： 通过旧的`S`和新的`S`进行比较，判断是否需要刷新。（测试不起效果啊啊啊，求教）

`builder`：返回` Widget `

`child`: Model更新时，不进行重构的部件



Demo:

```dart
Selector<CounterModel, int>(
  selector: (context, model) => model.counter,
  builder: (context, counter, child) {
    return Column(
      children: [
        Text(counter.toString()),
        child,
      ],
    );
  },
  child: Text('我不会以刷新aa'),
)
```













dio 接口请求

device_info 获取手机信息