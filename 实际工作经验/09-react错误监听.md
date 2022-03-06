# React 错误监听

## 题目

如何统一监听 React 组件报错？

## 分析

真实项目需要**闭环**，即考虑各个方面，除了基本的功能外，还要考虑性能优化、报错、统计等。
而个人项目、课程项目一般以实现功能为主，不会考虑这么全面。所以，没有实际工作经验的同学，不会了解如此全面。

## ErrorBoundary

React 16+ 引入。可以监听所有**下级**组件报错，同时降级展示 UI 。<br>
代码参考 ErrorBoundary.js 和 components/ErrorDemo

建议应用到最顶层，监听全局错误

```jsx
// index.js 入口文件
ReactDOM.render(
  <React.StrictMode>
    <ErrorBoundary>
      <App />
    </ErrorBoundary>
  </React.StrictMode>,
  document.getElementById('root')
);
```

函数组件中也可以使用

```js
function App(props) {
    return <ErrorBoundary>
        {props.children}
    </ErrorBoundary>
}
```

## dev 和 build

dev 环境下无法看到 ErrorBoundary 的报错 UI 效果。会显示的提示报错信息。<br>
`yarn build` 之后再运行，即可看到 UI 效果。

## 事件报错

React 不需要 ErrorBoundary 来捕获事件处理器中的错误。与 `render` 方法和生命周期方法不同，事件处理器不会在渲染期间触发。

如果你需要在事件处理器内部捕获错误，使用普通的 `try-catch` 语句。也可以使用全局的 `window.onerror` 来监听。

## 异步错误

ErrorBoundary 无法捕捉到异步报错，可使用 `window.onerror` 来监听。

```js
window.onerror = function(msg, source, line, column, error) {
    console.log('window.onerror---------', msg, source, line, column, error)
}
// 注意，如果用 window.addEventListener('error', event => {}) 参数不一样！！！
```

## 答案

- ErrorBoundary 监听渲染时报错
- `try-catch` 和 `window.onerror` 捕获其他错误

## 扩展

Promise 监听报错要使用 `window.onunhandledrejection` ，后面会有面试题讲解。

前端拿到错误监听之后，需要传递给服务端，进行错误收集和分析，然后修复 bug 。
后面会有一道面试题专门讲解。
