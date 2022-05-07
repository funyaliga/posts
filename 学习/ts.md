[TOC]

## 类型学习

#### 枚举类型

```javascript
enum Color {
  RED = "红色",
  PINK = "粉色",
  BLUE = "蓝色",
}

const resKey = 'RED';
const color = Color[resKey];
```



#### 数组类型

```javascript
const arr1: number[] = [1, 2, 3];
const arr2: Array<number> = [1, 2, 3];
```



#### null 和 undefined

```javascript
let u: undefined = undefined;
let n: null = null;
```



#### object, Object 和 {} 类型

**object** 类型用于表示非原始类型

```javascript
let objectCase: object;
objectCase = 1; // error
objectCase = "a"; // error
objectCase = true; // error
objectCase = null; // error
objectCase = undefined; // error
objectCase = {}; // ok
```

**大 Object** 和 **{}** 代表所有拥有 toString、hasOwnProperty 方法的类型 所以所有原始类型、非原始类型都可以赋给 Object(严格模式下 `null` 和 `undefined` 不可以)

```javascript
let ObjectCase: Object;
ObjectCase = 1; // ok
ObjectCase = "a"; // ok
ObjectCase = true; // ok
ObjectCase = null; // error
ObjectCase = undefined; // error
ObjectCase = {}; // ok
```



#### 联合类型

通过管道(|)将变量设置多种类型。

```javascript
let title: string | number;
title = 1;
console.log(title.toFixed(2));
title = "hello";
console.log(title.length);
```



#### 类型断言

```typescript
值 as 类型
```

或

```typescript
<类型>值
```

> 尖括号<>格式会与 react 中 JSX 产生语法冲突，因此我们更推荐使用 as 语法。





## 类

#### 接口interface

```typescript
interface Config {
  name: string;
  age?: Number; // ?符号表示可选属性
  readonly gender: String;  //readonly 表示该属性是只读属性 不可以更改
  [propName: string]: any; // 其他任意属性
}

function people(config: Config) {
  let text: String = `名字：${config.name}，性别：${config.gender}`;
  if(config.age) {
    text += `，年龄：${config.age}`
  }
  console.log(text);
}

people({ name: 'ming', gender: '男' }); // 名字：ming，性别：男
people({ name: 'tom', gender: '女', age: 18 }); // 名字：tom，性别：女，年龄：18
```

