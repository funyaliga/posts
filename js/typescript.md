[TOC]

####  转->可选属性

官方Api: `Partial`    

源码:

```typescript
type Partial<T> = {
    [P in keyof T]?: T[P];
};

// keyof 获取类型中的所有键
// p in xx 相当于for p in xx
```

例子：

```typescript
type User = {
  name: string;
  password: string;
  phone: string;
};

type UserPartial = Partial<User>
/* 等价于：
type UserPartial = {
  name!: string;
  password!: string;
  phone!: string;
};
*/
```



####  转->必选

官方Api: `Required`    

源码:

```typescript
type Required<T> = { [P in keyof T]-?: T[P]; }
// 通过`-`将可选属性的`?`符号去掉，变为必选属性
```

例子：

```typescript
type User = {
  name?: string;
  password?: string;
  phone: string;
};
type UserRequired = Required<User>
/* 等价于：
type UserRequired = {
    name: string;
    password: string;
    phone: string;
}
*/
```



####  转->只读

官方Api: `Readonly`    

源码:

```typescript
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
};

// keyof 获取类型中的所有键
// p in xx 相当于for p in xx

// 如果需要移除已读，前面加个-即可， -readonly [P in keyof T]: T[P];
```

例子：

```typescript
type User = {
  name: string;
  password: string;
  phone: string;
};

type UserReadonly = Readonly<User>
/* 等价于：
type UserReadonly = {
    readonly name: string;
    readonly password: string;
    readonly phone: string;
}
*/
```



#### 转 -> Getters 

```typescript
type Getters<T> = {
  [K in keyof T as `get${Capitalize<string & K>}`]: () => T[K]
};
// as 子句对映射类型中的键进行重新映射
// Capitalize：字符首字母转大写
```

例子：

```typescript
type User = {
  name: string;
  password: string;
  phone: number;
};

type GettersUser = Getters<User>;
/* 等价于：
type GettersUser = {
    getName: () => string;
    getPassword: () => string;
    getPhone: () => number;
}
*/
```



#### `Record`创建了一个拥有`Keys`类型的属性和对应值的`Type`的对象

源码：

```typescript
type Record<K extends keyof any, T> = {
    [P in K]: T;
};
```

例子：

```typescript
type User = {
  name: string;
  password: string;
  phone: string;
};

// demo1
type UserRecord = Record<'a' | 'b' | 1, User>;
/* 
type UserRecord = {
    a: User;
    b: User;
    1: User;
}
*/

// demo2  使用typeof从数组获取
const keys = <const> ['teacher', 'student', 'staff']
type UserRecord = Record<typeof keys[number], User>;
/* 
type UserRecord = {
    teacher: User;
    student: User;
    staff: User;
}
*/

// demo3 使用keyof typeof从对象获取
const obj = { one: 1, two: 2, three: 3}
type UserRecord = Record<keyof typeof obj, User>; 
/* 
type UserRecord = {
    one: User;
    two: User;
    three: User;
}
*/

// demo4  使用keyof从现有类型获取
interface Level {
  senior: string,
  intermediate: number,
  junior: boolean,
}
type UserRecord = Record<keyof Level, User>;
/* 
type UserRecord = {
    senior: User;
    intermediate: User;
    junior: User;
}
*/
```



#### *蛇*形 -> 驼峰 ####

https://stackoverflow.com/questions/60269936/typescript-convert-generic-object-from-snake-to-camel-case

```typescript
type SnakeToCamelCase<S extends string> =
  S extends `${infer T}_${infer U}` ?
  `${Lowercase<T>}${Capitalize<SnakeToCamelCase<Lowercase<U>>>}` :
  S

type demo1 = SnakeToCamelCase<'hight_school'> // type demo1 = "hightSchool"
type demo2 = SnakeToCamelCase<'HELLO_WORLD'> // type demo2 = "helloWorld"
```

对象嵌套：

```typescript
type SnakeToCamelCaseNested<T> = T extends object ? {
  [K in keyof T as SnakeToCamelCase<K & string>]: SnakeToCamelCaseNested<T[K]>
} : T

type Info = {
  password: string;
  new_password: string;
  phone: number;
}

type User = {
  user_name: string;
  user_info: Info
};

type user = SnakeToCamelCaseNested<User>
/*
type user = {
    userName: string;
    userInfo: {
        password: string;
        newPassword: string;
        phone: number;
    };
}
*/
```



#### 驼峰 -> 蛇形 ####

```typescript
type CamelToSnakeCase<S extends string> =
  S extends `${infer T}${infer U}` ?
  `${T extends Capitalize<T> ? "_" : ""}${Lowercase<T>}${CamelToSnakeCase<U>}` :
  S

type demo1 = CamelToSnakeCase<'helloWorld'> // type demo1 = "hello_world"
type demo2 = CamelToSnakeCase<'howAreYou'> // type demo2 = "how_are_you"
```





####  提取

官方Api: `Pick`    

源码:

```typescript
type Pick<T, K extends keyof T> = {
    [P in K]: T[P];
};

```

例子：

```typescript
type User = {
  name: string;
  password: string;
  phone: string;
};

type UserSmall = Pick<User, 'name' | 'phone'>
/* 等价于：
type UserSmall = {
    name: string;
    phone: string;
}
*/

```



#### 移除

```typescript
type RemoveFields<T, U> = {
  [K in keyof T as Exclude<K, U>]: K
};
```

or 

官方api `Omit`

```typescript
type Omit<T, K extends keyof any> = Pick<T, Exclude<keyof T, K>>;
```

例子：

```typescript
type User = {
  name: string;
  password: string;
  phone: number;
};
type UserSamll = RemoveFields<User, 'password' | 'phone'>
/*
type UserSamll = {
    name: string;
}
*/
```



#### 提取指定类型对应的keys

```typescript
// https://github.com/sindresorhus/type-fest/blob/main/source/conditional-keys.d.ts#L33
export type ConditionalKeys<Base, Condition> = NonNullable<
	// Wrap in `NonNullable` to strip away the `undefined` type from the produced union.
	{
		// Map through all the keys of the given base type.
		[Key in keyof Base]:
			// Pick only keys with types extending the given `Condition` type.
			Base[Key] extends Condition
				// Retain this key since the condition passes.
				? Key
				// Discard this key since the condition fails.
				: never;

	// Convert the produced object into a union type of the keys which passed the conditional test.
	}[keyof Base]
>;
```

例子：

```typescript
type User = {
  name?: string;
  password?: string;
  phone?: number;
};

type test = ConditionalKeys<User, string | undefined> // 可选属性要加个undefined才能提取
// type test = "name" | "password"
```

