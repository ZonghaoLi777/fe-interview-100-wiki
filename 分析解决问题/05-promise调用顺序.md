# promise

## 题目

以下代码，执行会输出什么

```js
Promise.resolve().then(() => {
    console.log(0)
    return Promise.resolve(4)
}).then((res) => {
    console.log(res)
})

Promise.resolve().then(() => {
    console.log(1)
}).then(() => {
    console.log(2)
}).then(() => {
    console.log(3)
}).then(() => {
    console.log(5)
}).then(() =>{
    console.log(6)
})
```

## 这道题很难

网上有很多文章介绍这道题，都没有给出清晰的答案。

被称为“令人失眠的”题目

## 回顾

- 单线程和异步
- 事件循环
- 宏任务 微任务

## then 交替执行

如果有多个 fulfilled 状态的 promise 实例，同时执行 then 链式调用，then 会交替调用<br>
这是编译器的优化，防止一个 promise 持续占据事件

```js
Promise.resolve().then(() => {
    console.log(1)
}).then(() => {
    console.log(2)
}).then(() => {
    console.log(3)
}).then(() => {
    console.log(4)
})

Promise.resolve().then(() => {
    console.log(10)
}).then(() => {
    console.log(20)
}).then(() => {
    console.log(30)
}).then(() => {
    console.log(40)
})

Promise.resolve().then(() => {
    console.log(100)
}).then(() => {
    console.log(200)
}).then(() => {
    console.log(300)
}).then(() => {
    console.log(400)
})
```

## then 返回 promise 对象

当 then 返回 promise 对象时，可以认为是多出一个 promise 实例。

```js
Promise.resolve().then(() => {
    console.log(1)
    return Promise.resolve(100) // 相当于多处一个 promise 实例，如下注释的代码
}).then(res => {
    console.log(res)
}).then(() => {
    console.log(200)
}).then(() => {
    console.log(300)
}).then(() => {
    console.log(300)
})

Promise.resolve().then(() => {
    console.log(10)
}).then(() => {
    console.log(20)
}).then(() => {
    console.log(30)
}).then(() => {
    console.log(40)
})

// // 相当于新增一个 promise 实例 —— 但这个执行结果不一样，后面解释
// Promise.resolve(100).then(res => {
//     console.log(res)
// }).then(() => {
//     console.log(200)
// }).then(() => {
//     console.log(300)
// }).then(() => {
//     console.log(400)
// })
```

## “慢两拍”

then 返回 promise 实例和直接执行 `Promise.resolve()` 不一样，它需要等待两个过程
- promise 状态由 pending 变为 fulfilled
- then 函数挂载到 microTaskQueue

所以，它变现的会“慢两拍”。可以理解为

```js
Promise.resolve().then(() => {
    console.log(1)
})

Promise.resolve().then(() => {
    console.log(10)
}).then(() => {
    console.log(20)
}).then(() => {
    console.log(30)
}).then(() => {
    console.log(40)
})

Promise.resolve().then(() => {
    // 第一拍
    const p = Promise.resolve(100)
    Promise.resolve().then(() => {
        // 第二拍
        p.then(res => {
            console.log(res)
        }).then(() => {
            console.log(200)
        }).then(() => {
            console.log(300)
        }).then(() => {
            console.log(400)
        })
    })
})
```

## 答案

题目代码输出的结果是 `1 2 3 4 5 6`

## 重点

- 熟悉基础知识：事件循环 宏任务 微任务
- then 交替执行
- then 返回 promise 对象时“慢两拍”

PS：这里一直在微任务环境下，如果加入宏任务就不一样了
