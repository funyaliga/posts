测试环境： mac,  chrome 59.0.3071.115



## Function

直接打印Function时会自动调用`toString  `和 `valueOf`

返回值是是原始类型（Undefined、Null、Boolean、Number、 String）为√， 非原始类型是×



`toString  ` √  →  `valueOf`  √

```javascript
const fn = function () { return 'aaa'}
fn.toString = () => {console.log('run toString'); return 'sss'}
fn.valueOf = () => {console.log('run valueOf'); return 'vvv'}
console.log(fn)
// function vvv
// run toString
// run valueOf
```



`toString` √  →  `valueOf` ×  →  `toString` √

```javascript
const fn = function () { return 'aaa'}
fn.toString = () => {console.log('run toString'); return 'sss'}
fn.valueOf = () => {console.log('run valueOf'); return {}}
console.log(fn)
// function sss
// run toString
// run valueOf
// run toString
```



`toString` ×  →  `valueOf` √   →  `valueOf` √ 

```javascript
const fn = function () { return 'aaa'}
fn.toString = () => {console.log('run toString'); return {}}
fn.valueOf = () => {console.log('run valueOf'); return 'vvv'}
console.log(fn)
// function vvv
// run toString
// run valueOf
// run valueOf
```



`toString` ×  →  `valueOf` ×  →  `valueOf` ×  →  `toString` × 

```javascript
const fn = function () { return 'aaa'}
fn.toString = () => {console.log('run toString'); return {}}
fn.valueOf = () => {console.log('run valueOf'); return 'vvv'}
console.log(fn)
// function function #<Function>   (????)
// run toString
// run valueOf
// run valueOf
// run toString
```



## Array/Object/Function→String##

返回值是是原始类型（Undefined、Null、Boolean、Number、 String）为√， 非原始类型是×



`toString` √

```javascript
const a = {}
a.toString = () => {console.log('run toString'); return 'sss'}
a.valueOf = () => {console.log('run valueOf'); return 'vvv'}
console.log(`${a}`)
// run toString
// sss

const fn = () => {}
fn.toString = () => {console.log('run toString'); return 'sss'}
fn.valueOf = () => {console.log('run valueOf'); return 'vvv'}
console.log(`${fn}`)
// run toString
// sss
```



`toString` ×  →  `valueOf` √

```javascript
const a = {}
a.toString = () => {console.log('run toString'); return {}}
a.valueOf = () => {console.log('run valueOf'); return 'vvv'}
console.log(`${a}`)
// run toString
// run valueOf
// vvv

const fn = () => {}
fn.toString = () => {console.log('run toString'); return {}}
fn.valueOf = () => {console.log('run valueOf'); return 'vvv'}
console.log(`${fn}`)
// run toString
// run valueOf
// vvv
```



`toString` ×  →  `valueOf` × → 报错

```javascript
const a = {}
a.toString = () => {console.log('run toString'); return {}}
a.valueOf = () => {console.log('run valueOf'); return {}}
console.log(`${a}`)
// run toString
// run valueOf
// Uncaught TypeError: Cannot convert object to primitive value

const fn = () => {}
fn.toString = () => {console.log('run toString'); return {}}
fn.valueOf = () => {console.log('run valueOf'); return {}}
console.log(`${fn}`)
// run toString
// run valueOf
// Uncaught TypeError: Cannot convert object to primitive value
```



## Array/Object/Function→Number

与前面的String相反

返回值是是原始类型（Undefined、Null、Boolean、Number、 String）为√， 非原始类型是×



`valueOf` √

```javascript
const a = {}
a.toString = () => {console.log('run toString'); return 'sss'}
a.valueOf = () => {console.log('run valueOf'); return 'vvv'}
console.log(a + 1)
// run valueOf
// vvv1

const fn = () => {}
fn.toString = () => {console.log('run toString'); return 'sss'}
fn.valueOf = () => {console.log('run valueOf'); return 'vvv'}
console.log(fn + 1)
// run valueOf
// vvv1
```



`valueOf` ×  →  `toString` √

```javascript
const a = {}
a.toString = () => {console.log('run toString'); return 'toString'}
a.valueOf = () => {console.log('run valueOf'); return {}}
console.log(a + 1)
// run valueOf
// run toString
// sss1

const fn = () => {}
fn.toString = () => {console.log('run toString'); return 'sss'}
fn.valueOf = () => {console.log('run valueOf'); return {}}
console.log(fn + 1)
// run valueOf
// run toString
// sss1
```



`valueOf` ×  →  `toString` × → 报错

```javascript
const a = {}
a.toString = () => {console.log('run toString'); return {}}
a.valueOf = () => {console.log('run valueOf'); return {}}
console.log(a + 1)
// run valueOf
// run toString
// Uncaught TypeError: Cannot convert object to primitive value

const fn = () => {}
fn.toString = () => {console.log('run toString'); return {}}
fn.valueOf = () => {console.log('run valueOf'); return {}}
console.log(fn + 1)
// run valueOf
// run toString
// Uncaught TypeError: Cannot convert object to primitive value
```



## Array/Object/Function→Boolean

返回值是是原始类型（Undefined、Null、Boolean、Number、 String）为√， 非原始类型是×



`valueOf` √

```javascript
const a = {}
a.toString = () => {console.log('run toString'); return 1}
a.valueOf = () => {console.log('run valueOf'); return 2}
console.log(a == 1)
// run valueOf
// false

const fn = () => {}
fn.toString = () => {console.log('run toString'); return 1}
fn.valueOf = () => {console.log('run valueOf'); return 2}
console.log(fn == 1)
// run valueOf
// false
```



`valueOf` ×  →  `toString` √

```javascript
const a = {}
a.toString = () => {console.log('run toString'); return 1}
a.valueOf = () => {console.log('run valueOf'); return {}}
console.log(a == 1)
// run valueOf
// true

const fn = () => {}
fn.toString = () => {console.log('run toString'); return 1}
fn.valueOf = () => {console.log('run valueOf'); return {}}
console.log(fn == 1)
// run valueOf
// true
```



`valueOf` ×  →  `toString` × → 报错

```javascript
const a = {}
a.toString = () => {console.log('run toString'); return {}}
a.valueOf = () => {console.log('run valueOf'); return {}}
console.log(a == 1)
// run valueOf
// run toString
// Uncaught TypeError: Cannot convert object to primitive value

const fn = () => {}
fn.toString = () => {console.log('run toString'); return {}}
fn.valueOf = () => {console.log('run valueOf'); return {}}
console.log(fn == 1)
// run valueOf
// run toString
// Uncaught TypeError: Cannot convert object to primitive value
```



