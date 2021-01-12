

[TOC]

## 构造方法:

### periodic

创建周期性的流

```dart
test() async{
  // 使用 periodic 创建流，第一个参数为间隔时间，第二个参数为回调函数
  Stream<int> stream = Stream<int>.periodic(Duration(seconds: 1), (int t) => t);
  stream.listen(
    (value)=>print('经过$value秒'),
  );
}


test();


// 打印
=> 经过0秒
=> 经过1秒
=> 经过2秒
....
```



### fromFuture

该方法从一个`Future`创建`Stream`，当`Future`执行完成时，就会放入`Stream`中，而后从`Stream`中将任务完成的结果取出。这种用法，很像异步任务队列。

```dart
import  'dart:io';

print("test开始");

Future<String> fut = Future((){
  sleep(Duration(seconds: 2)); 
  return "异步1结束";
});

Stream<String> stream = Stream<String>.fromFuture(fut);

// await Future的流结束
await for(var value in stream){
  print(value);
}

print("test结束");

test();

// 打印
=> test开始
=> 异步结束
=> test结束
```



### fromFutures

从多个`Future`创建`Stream`，即将一系列的异步任务放入`Stream`中，每个`Future`按顺序执行，执行完成后放入`Stream`

```dart
print("test开始");

Future<String> fut1 = Future((){
  sleep(Duration(seconds: 2)); 
  return "异步1结束";
});

Future<String> fut2 = Future((){
  sleep(Duration(seconds: 3)); 
  return "异步2结束";
});

Stream<String> stream = Stream<String>.fromFutures([fut1, fut2]);

// await Future的流结束
await for(var value in stream){
  print(value);
}

print("test结束");

// 打印
=> test开始
=> 异步1结束
=> 异步2结束
=> test结束

```



### fromIterable

该方法从一个集合创建`Stream`

```dart
Stream<String> stream = Stream<String>.fromIterable(['呵呵', '哈哈', '嘻嘻']);

// await Future的流结束
await for(var value in stream){
  print(value);
}

// 打印
=> 呵呵
=> 哈哈
=> 嘻嘻
```



### value

从单个值创建`Stream`

```dart
Stream<String> stream = Stream<String>.value('哈哈哈哈哈');

await for(var value in stream){
  print(value);
}

// 打印
=> 哈哈哈哈哈
```



## 监听方式

### await for

```dart
Stream<int> stream = Stream<int>.periodic(Duration(seconds: 1), (int t) => t).take(3); // take限制3个

await for(var value in stream){
	print(value);
}

print('xx');

=> 0
=> 1
=> 2
=> xx
```



### forEach

```dart
Stream<int> stream = Stream<int>.periodic(Duration(seconds: 1), (int t) => t).take(3); // take限制3个

await stream.forEach((var value){
  print(value);
});

print('xx');

=> 0
=> 1
=> 2
=> xx
```



### listen

- onData(必填)：收到数据时触发
- onError：收到Error时触发
- onDone：结束时触发
- unsubscribeOnError：遇到第一个Error时是否取消订阅，默认为false

```dart
Stream<int> stream = Stream<int>.periodic(Duration(seconds: 1), (int t) => t).take(3);

stream.listen(
  (value) => print(value),
  onDone: () => print("结束")
);
print('xx');

=> xx
=> 0
=> 1
=> 2
=> 结束
```



## 其他方法

### take

限制`Stream`中的元素数量

```dart
Stream<int> stream = Stream<int>.periodic(Duration(seconds: 1), (int t) => t);
stream = stream.take(3); // take限制3个

await for(var value in stream){
	print(value);
}

=> 0
=> 1
=> 2
```



### takeWhile

对当前`Stream`进行判断，不满足条件则取消监听

```dart
Stream<int> stream = Stream<int>.periodic(Duration(seconds: 1), (int t) => t);
stream = stream.takeWhile((value) => value < 5);

await for(var value in stream){
  print(value);
}

=> 0
=> 1
=> 2
=> 3
=> 4
```



### skip

跳过

```dart
Stream<int> stream = Stream<int>.periodic(Duration(seconds: 1), (int t) => t).take(5);

stream = stream.skip(2); // 跳过2个

await for(var value in stream){
  print(value);
}

// 0 和 1被跳过了
=> 2
=> 3
=> 4
```



## skipWhile

对当前`Stream`进行判断，直到返回false前，都跳过

```dart
Stream<int> stream = Stream<int>.periodic(Duration(seconds: 1), (int t) => t).take(5);

stream = stream.skipWhile((value) => value < 3);

await for(var value in stream){
  print(value);
}

// 直到3，条件才返回false,所以前面0~2都跳过了
=> 3
=> 4
```



### where

条件判断

```dart
Stream<int> stream = Stream<int>.periodic(Duration(seconds: 1), (int t) => t);

stream = stream.where((value) => value % 2 == 0).take(5);

await for(var value in stream){
  print(value);
}

=> 0
=> 2
=> 4
=> 6
=> 8
```

### map

遍历

```dart
Stream stream = Stream.periodic(Duration(seconds: 1), (int t) => t);

stream = stream.map((value) => '$value的倍数是${value * 2}').take(5);

await for(var value in stream){
  print(value);
}

=> 0的倍数是0
=> 1的倍数是2
=> 2的倍数是4
=> 3的倍数是6
=> 4的倍数是8
```



### toList

将`Stream`中所有数据存储在List中

```dart
Stream<int> stream = Stream<int>.periodic(Duration(seconds: 1), (int t) => t).take(5);
List <int> data = await stream.toList(); // 等待并转成List
for(var i in data){ 
  print(i);
} 

=> 0
=> 1
=> 2
=> 3
=> 4
```



### length

获取`Stream`中数据数量

```dart
Stream<int> stream = Stream<int>.periodic(Duration(seconds: 1), (int t) => t).take(5);
int len = await stream.length; // 等待并获取数据数量
print(len);
```





## StreamController

参数：

- `onListen` 注册监听时回调

- `onPause` 当流暂停时回调

- `onResume` 当流恢复时回调

- `onCancel` 当监听器被取消时回调

- `sync` 当值为`true`时表示同步控制器`SynchronousStreamController`，默认值为`false`，表示异步控制器

```dart
//任意类型的流
StreamController controller = StreamController();
controller.sink.add(123);
controller.sink.add("xyz");
controller.sink.add(Anything);

//创建一条处理int类型的流
StreamController<int> numController = StreamController();
numController.sink.add(123);
```

```dart
// 注：在组件里需要销毁，不然会有警告

@override
dispose() {
  super.dispose();
  _streamController.close();
}
```



### StreamSink

流事件的入口。提供 `add`，`addError`，`addStream`,`close` 方法向流发送事件。

```dart
int _counter = 0;
StreamController<int> _counterStreamController = new StreamController(
  onListen: () { print('onListen'); },
  onCancel: () { print('onCancel'); },
);

StreamSink _counterStreamSink;

@override
void initState() {
  _counterStreamSink = _counterStreamController.sink;
  
  Timer.periodic(const Duration(seconds: 1), (timer) {
    if (_counter > 5) {
      _counterStreamSink.close();
      timer.cancel();
      return;
    }
    _counterStreamSink.add(_counter++);
  });
  
  _counterStreamController.stream.listen((value) => print(value));

  super.initState();
}

@override
void dispose(){
  _counterStreamController.close();
  _counterStreamSink.close(); // 如果定义了StreamSink的变量，需要close调
  super.dispose();
}

=> onListen
=> 0
=> 1
=> 2
=> 3
=> 4
=> 5
=> onCancel
```

```dart
int _counter = 0;
StreamController<int> _counterStreamController = new StreamController(
  onListen: () { print('onListen'); }
);

StreamSink _counterStreamSink;

@override
void initState() {
  _counterStreamSink = _counterStreamController.sink;
  
  Timer.periodic(const Duration(seconds: 1), (timer) {
    if (_counter > 5) {
      _counterStreamSink.addError('太大了');
      timer.cancel();
      return;
    }
    _counterStreamSink.add(_counter++);
  });
  
  _counterStreamController.stream.listen(
    (value) => print(value),
    onError:(err) => print(err)
  );

  super.initState();
}

@override
void dispose(){
  _counterStreamController.close();
  _counterStreamSink.close(); // 如果定义了StreamSink的变量，需要close调
  super.dispose();
}

=> onListen
=> 0
=> 1
=> 2
=> 3
=> 4
=> 5
=> 太大了
```



### StreamSubscription

流的监听器。提供 `cacenl`、`pause`, `resume` 等方法管理。

```dart
int _counter = 0;
StreamController<int> _counterStreamController = new StreamController(
  onListen: () { print('onListen'); },
  onPause: () { print('onPause'); },
  onResume: () { print('onResume'); },
  onCancel: () { print('onCancel'); },
);

StreamSubscription _counterStreamSubscription;

@override
void initState() {

  _counterStreamSubscription = _counterStreamController.stream.listen(
    (value) => print(value),
    onError:(err) => print(err)
  );

  Timer.periodic(const Duration(seconds: 1), (timer) {
    _counter++;
    if (_counter == 3) {
      _counterStreamSubscription.pause();
      return;
    }
    if (_counter > 3) {
      _counterStreamSubscription.resume();
    }
    if (_counter > 5) {
      _counterStreamSubscription.cancel();
      timer.cancel();
      return;
    }
    _counterStreamController.add(_counter);
  });

  super.initState();
}

@override
void dispose(){
  _counterStreamController.close();
  super.dispose();
}

=> onListen
=> 1
=> 2
=> onPause
=> onResume
=> 4
=> 5
=> onCancel
```



### StreamBuilder

`Stream`的`Widget`，会监听`Stream`的数据变化更新ui

```dart
class _HomePageState extends State<HomePage> {
  int _counter = 0;
  StreamController<int> _counterStreamController = new StreamController();

  @override
  void initState() {
    
    Timer.periodic(const Duration(seconds: 1), (timer) {
      if (_counter > 5) {
        timer.cancel();
        return;
      }
      _counterStreamController.add(_counter++);
    });
    
    super.initState();
  }

  @override
  void dispose(){
    _counterStreamController.close();
    super.dispose();
  }

  @override
  Widget build(BuildContext context){
    return new MaterialApp(
      home: new Scaffold(
        body: new Center(
          child: StreamBuilder<int>(
            stream: _counterStreamController.stream, // 数据流
            initialData: 0, // 初始值
            builder: (context, AsyncSnapshot<int> snapshot) { // AsyncSnapshot 对象为数据快照，缓存了当前数据和状态
              return Text(snapshot.data.toString());
            }
          ),
        ),
      ),
    );
  }
}
```



