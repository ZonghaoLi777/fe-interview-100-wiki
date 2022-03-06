# for...in 和 for...of 的区别

# 题目

for...in 和 for...of 的区别

## key 和 value

for...in 遍历 key , for...of 遍历 value

```js
const arr = [10, 20, 30]
for (let n of arr) {
    console.log(n)
}

const str = 'abc'
for (let s of str) {
    console.log(s)
}
```

```js
function fn() {
    for (let argument of arguments) {
        console.log(argument) // for...of 可以获取 value ，而 for...in 获取 key
    }
}
fn(10, 20, 30)

const pList = document.querySelectorAll('p')
for (let p of pList) {
    console.log(p) // for...of 可以获取 value ，而 for...in 获取 key
}
```

## 遍历对象

for...in 可以遍历对象，for...of 不可以

## 遍历 Map/Set

for...of 可以遍历 Map/Set ，for...in 不可以

```js
const set1 = new Set([10, 20, 30])
for (let n of set1) {
    console.log(n)
}

let map1 = new Map([
    ['x', 10], ['y', 20], ['z', 3]
])
for (let n of map1) {
    console.log(n)
}
```

## 遍历 generator

for...of 可遍历 generator ，for...in 不可以

```js
function* foo(){
  yield 10
  yield 20
  yield 30
}
for (let o of foo()) {
  console.log(o)
}
```

## 对象的可枚举属性

for...in 遍历一个对象的可枚举属性。

使用 `Object.getOwnPropertyDescriptors(obj)` 可以获取对象的所有属性描述，看 ` enumerable: true` 来判断该属性是否可枚举。

对象，数组，字符传

## 可迭代对象

for...of 遍历一个可迭代对象。<br>
其实就是迭代器模式，通过一个 `next` 方法返回下一个元素。

该对象要实现一个 `[Symbol.iterator]` 方法，其中返回一个 `next` 函数，用于返回下一个 value（不是 key）。<br>
可以执行 `arr[Symbol.iterator]()` 看一下。

JS 中内置迭代器的类型有 `String` `Array` `arguments` `NodeList` `Map` `Set` `generator` 等。

## 答案

- for...in 遍历一个对象的可枚举属性，如对象、数组、字符串。针对属性，所以获得 key
- for...of 遍历一个可迭代对象，如数组、字符串、Map/Set 。针对一个迭代对象，所以获得 value

## 划重点

“枚举” “迭代” 都是计算机语言的一些基础术语，目前搞不懂也没关系。<br>
但请一定记住 for...of 和 for...in 的不同表现。

## 连环问：for await...of

用于遍历异步请求的可迭代对象。

```js
// 像定义一个创建 promise 的函数
function createTimeoutPromise(val) {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve(val)
        }, 1000)
    })
}
```

如果你明确知道有几个 promise 对象，那直接处理即可

```js
(async function () {
    const p1 = createTimeoutPromise(10)
    const p2 = createTimeoutPromise(20)

    const v1 = await p1
    console.log(v1)
    const v2 = await p2
    console.log(v2)
})()
```

如果你有一个对象，里面有 N 个 promise 对象，你可以这样处理

```js
(async function () {
    const list = [
        createTimeoutPromise(10),
        createTimeoutPromise(20)
    ]

    // 第一，使用 Promise.all 执行
    Promise.all(list).then(res => console.log(res))

    // 第二，使用 for await ... of 遍历执行
    for await (let p of list) {
        console.log(p)
    }

    // 注意，如果用 for...of 只能遍历出各个 promise 对象，而不能触发 await 执行
})()
```

【注意】如果你想顺序执行，只能延迟创建 promise 对象，而不能及早创建。<br>
即，你创建了 promise 对象，它就立刻开始执行逻辑。

```js
(async function () {
    const v1 = await createTimeoutPromise(10)
    console.log(v1)
    const v2 = await createTimeoutPromise(20)
    console.log(v2)

    for (let n of [100, 200]) {
        const v = await createTimeoutPromise(n)
        console.log('v', v)
    }
})()
```
