# ajax fetch axios 的区别

## 题目

ajax fetch axios 的区别

## 分析

三者根本没有可比性，不要被题目搞混了。要说出他们的本质

## 传统 ajax

AJAX （几个单词首字母，按规范应该大写） - Asynchronous JavaScript and XML（异步的 JavaScript 和 XML）<br>
即使用 JS 进行异步请求，是 Web2.0 的技术基础，从 2005 年左右开始发起。<br>
所以，这里的 AJAX 就是一个称呼，一个缩写。

基于当时 JS 规范，异步请求主要使用 XMLHttpRequest 这个底层 API 。<br>
所以，有一道常考的面试题：请用 XMLHttpRequest 实现 ajax

```js
function ajax(url, successFn) {
    const xhr = new XMLHttpRequest()
    xhr.open("GET", url, false)
    xhr.onreadystatechange = function () {
        // 这里的函数异步执行，可参考之前 JS 基础中的异步模块
        if (xhr.readyState == 4) {
            if (xhr.status == 200) {
                successFn(xhr.responseText)
            }
        }
    }
    xhr.send(null)
}
```

## fetch

fetch 是一个原生 API ，它和 XMLHttpRequest 一个级别。

fetch 和 XMLHttpRequest 的区别
- 写法更加简洁
- 原生支持 promise

面试题：用 fetch 实现一个 ajax

```js
function ajax(url) {
    return fetch(url).then(res => res.json())
}
```

## axios

axios 是一个[第三方库](https://www.npmjs.com/package/axios)，随着 Vue 一起崛起。它和 jquery 一样（jquery 也有 ajax 功能）。

axios 内部可以用 XMLHttpRequest 或者 fetch 实现。

## 答案

- ajax 是一种技术称呼，不是具体的 API 和库
- fetch 是新的异步请求 API ，可代替 XMLHttpRequest
- axios 是第三方库

## 划重点

- 注意 库 和 API 的区别
- 实际项目要用库，尽量不要自己造轮子（除非有其他目的）

## 思考

库 和 框架 有什么区别？
