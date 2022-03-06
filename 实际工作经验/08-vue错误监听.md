# Vue 错误监听

## 题目

如何统一监听 Vue 组件报错？

## 分析

真实项目需要**闭环**，即考虑各个方面，除了基本的功能外，还要考虑性能优化、报错、统计等。
而个人项目、课程项目一般以实现功能为主，不会考虑这么全面。所以，没有实际工作经验的同学，不会了解如此全面。

## window.onerror

可以监听当前页面所有的 JS 报错，jQuery 时代经常用。<br>
注意，全局只绑定一次即可。不要放在多次渲染的组件中，这样容易绑定多次。

```js
window.onerror = function(msg, source, line, column, error) {
    console.log('window.onerror---------', msg, source, line, column, error)
}
// 注意，如果用 window.addEventListener('error', event => {}) 参数不一样！！！
```

## errorCaptured 生命周期

会监听所有**下级组件**的错误。可以返回 `false` 阻止向上传播，因为可能会有多个上级节点都监听错误。

```js
errorCaptured(error, instance, info) {
    console.log('errorCaptured--------', error, instance, info)
}
```

## errorHandler

全局的错误监听，所有组件的报错都会汇总到这里来。PS：如果 `errorCaptured` 返回 `false` 则**不会**到这里。

```js
const app = createApp(App)
app.config.errorHandler = (error, instance, info) => {
    console.log('errorHandler--------', error, instance, info)
}
```

请注意，`errorHandler` 会阻止错误走向 `window.onerror`。

PS：还有 `warnHandler`

## 异步错误

组件内的异步错误 `errorHandler` 监听不到，还是需要 `window.onerror`

```js
mounted() {
    setTimeout(() => {
        throw new Error('setTimeout 报错')
    }, 1000)
},
```

## 答案

方式
- `errorCaptured` 监听下级组件的错误，可返回 `false` 阻止向上传播
- `errorHandler` 监听 Vue 全局错误
- `window.onerror` 监听其他的 JS 错误，如异步

建议：结合使用
- 一些重要的、复杂的、有运行风险的组件，可使用 `errorCaptured` 重点监听
- 然后用 `errorHandler` `window.onerror` 候补全局监听，避免意外情况

## 扩展

Promise 监听报错要使用 `window.onunhandledrejection` ，后面会有面试题讲解。

前端拿到错误监听之后，需要传递给服务端，进行错误收集和分析，然后修复 bug 。
后面会有一道面试题专门讲解。
