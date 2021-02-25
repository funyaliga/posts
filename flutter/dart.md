[TOC]

## 对象操作符

#### ? 条件运算

```dart
​```dart
class Test {
  printInfo() {
    print("哈哈");
  }
}

void main() {
  Test test1;
  test1?.printInfo(); // test1为空，没有printInfo，不执行

  Test test2 = new Test();
  test2?.printInfo(); // 哈哈
}
​```
```



#### is 类型判断

```dart
class Test {
}

void main() {
  Test a = new Test();
  print(a is Test); // true
  print(a is Object); // true, 对象都继承来自Object
  String b = '哈哈';
  print(b is String); // true
}
```



#### as 类型转换



#### .. 链式调用

```dart
class Test {
  String name;
  int age;
  printAll() {
    print("${this.name} -- ${this.age}");
  }
}

void main() {
  Test test = new Test();
  test..name = "小李"
      ..age = 18
      ..printAll(); // 小李 -- 18
}
```



## 方法 ##

* `void fn1() { print('11'); }` 没有返回值时，写void



## 类 ##

#### 首字母要大写 ####

`class Person{}`



#### 类的构造函数跟类同名 ####

```dart
class Test {
    Test(str) {
        print(str);
    }
}

void main() {
    Test aa = new Test('hello'); // hello
}
```



#### 构造函数快速赋值 ####

```dart
class Test {
  int age = 12;
  Test(this.age); // 缩写快速赋值， 相当于Test(age) { this.age = age }
  void printAge() {
    print(this.age);
  }
}

void main() {
    Test aa = new Test(22);
    aa.printAge();
}
```



#### 命名构造函数 ####

```dart
class Test {
  String name;
  int age;
  Test(this.age);
  Test.name(name) {
    this.name = name;
    print(this.name);
  }
  Test.age(name) {
    this.age = age;
    print(this.age);
  }
}

void main() {
    Test name = new Test.name('李健');
    Test age = new Test.name(20);
}
```



## 私有属性 ##

> 在前面加`_`
>
> 注： 同文件还是能访问，不同文件才达到私有

```dart
class Test {
  String _name;
}
```



#### 访问器(get/set)

> 跟js类似

```dart
class Test {
  int _age;
  Test(this._age);
  get age {
    return this._age;
  }
  set age(n) {
    this._age = n;
  }
}

void main() {
  Test a = new Test(18);
  print(a.age); // 18
  a.age = 20;
  print(a.age); // 20
}
```



#### 初始化默认值

```dart
class React {
  int width;
  int height;
  React():width=10,height=20 {
    print("${this.width}---${this.height}"); // 10---20 (他是先赋值，再执行
  }
  get area{
    return this.width * this.height;
  }
}

void main() {
  React a = new React();
  print(a.area); // 200
}
```



#### static 静态属性 ####

> 静态属性在外部不需要new， 直接访问。
> 静态属性在内部不需要加this。
> 静态方法不能访问非静态属性。

```dart
class Test {
  // 静态属性
  static String name = '小李';

  // 普通属性
  int age = 18; 
  
  // 静态方法
  static void showName() {
    print(name);
  }

  void showAll() {
    print("${name} -- ${this.age}");
  }
}

void main() {
  Test a = new Test();
  print(Test.name); // 小李
  Test.showName(); // 小李 
  a.showAll(); // 小李 -- 18
}
```



#### extends 继承 ####

> 子类会继承父类可见的属性的方法
> 不会继承构造函数



#### super() ####

> - 调用父类的变量。
> - 每个子类的构造方法，都必须通过super调用父类的构造方法，保证了父类的构造方法先执行完毕，然后执行子类的构造方法。
> - 通过super 来访问父类的构造方法，执行的意义在于，对父类实例成员的初始化。

```dart
class Test {
  String name;
  Test(this.name);
}

class Ex extends Test {
  String name;
  Ex(String name) : super(name) {
    this.name = "${name}老师";
  }
  printInfo() {
    print('child name: ${this.name}');
    print('parent name: ${super.name}');
  }
}

void main() {
  Ex ex = new Ex('小李');
  ex.printInfo();
  // child name: 小李老师
  // parent name: 小李
}
```

```dart
// 调用父级构造函数
class Test {
  String name;
  int age;
  Test(this.name, this.age) {
    print("parent: ${name} --- ${age}");
  }
  Test.age(this.age);
}

class Ex extends Test {
  Ex(String name, int age, String sex) : super(name, age) {
    print("child: ${name} --- ${age} --- ${sex}");
  }
  
  // 调用父类的命名构造函数
  Ex.xx(int age) : super.age(age) {

  }
}

void main() {
  Ex ex = new Ex('小李', 20);
  // parent: 小李 --- 20
  // child: 小李 --- 20 --- 人妖
  
  Ex ex2 = new Ex.xx(20);
}
```



#### 覆写父级方法 ####

```dart
class Test {
  String name;
  int age;
  Test(this.name, this.age);

  printInfo() {
    print("parent: ${name} --- ${age}");
  }
}

class Ex extends Test {
  String sex;
  Ex(String name, int age, String sex) : super(name, age) {
    this.sex = sex;
  }
  
  @override
  printInfo() {
    print("parent: ${name} --- ${age} --- ${sex}");
  }
}

void main() {
  Ex ex = new Ex('小李', 20, '人妖');
  ex.printInfo();
}
```



#### 抽象类 ####

> - 抽象类主要是定义子类的标准
> - 抽象类不能直接实例化，只能被继承





## import ##

```dart
// fn.dart
void fn1() {
  print('11');
}
void fn2() {
  print('22');
}
void fn3() {
  print('33');
}
```



普通引入, 直接用。

```dart
import './fn.dart';

main() {
  fn1(); // 11
  fn2(); // 22
  fn3(); // 33
}
```



重命名，每个方法调用时，前面要加新名字。

```dart
import './fn.dart' as aa;

main() {
  aa.fn1(); // 11
  aa.fn2(); // 22
  aa.fn3(); // 33
}
```



只引入部分方法。

```dart
import './fn.dart' show fn1, fn3;

main() {
  fn1(); // 11
  fn3(); // 11
}
```



隐藏部分方法。

```dart
import './fn.dart' hide fn2;

main() {
  fn1(); // 11
  fn3(); // 11
}
```





