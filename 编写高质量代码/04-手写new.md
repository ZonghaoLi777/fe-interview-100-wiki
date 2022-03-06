# 手写 new

## 题目

new 一个对象内部发生了什么，手写代码表示

## class 是语法糖

ES6 使用 class 代替了 ES6 的构造函数

```js
class Foo {
    constructor(name) {
        this.name = name
        this.city = '北京'
    }
    getName() {
        return this.name
    }
}
const f = new Foo('双越')
```

其实 class 就是一个语法糖，它本质上和构造函数是一样的

```js
function Foo(name) {
    this.name = name
    this.city = '北京'
}
Foo.prototype.getName = function () { // 注意，这里不可以用箭头函数
    return this.name
}
const f = new Foo('双越')
```

## new 一个对象的过程

- 创建一个空对象 obj，继承构造函数的原型
- 执行构造函数（将 obj 作为 this）
- 返回 obj

## 实现 new

代码参考 new.ts

## 面试连环问：Object.create 和 {} 的区别

`Object.create` 可以指定原型，创建一个空对象。<br>
`{}` 就相当于 `Object.create(Object.prototype)` ，即根据 `Object` 原型的空对象。

PS：对 JS 原型和原型链还不了解的需要抓紧恶补。
