# Vue 组件通讯

## 题目

Vue 组件通讯有几种方式，尽量全面

## props / $emit

适用于父子组件。

- 父组件向子组件传递 props 和事件
- 子组件接收 props ，使用 `this.$emit` 调用事件

## 自定义事件

适用于兄弟组件，或者“距离”较远的组件。

常用 API
- 绑定事件 `event.on(key, fn)` 或 `event.once(key, fn)`
- 触发事件 `event.emit(key, data)`
- 解绑事件 `event.off(key, fn)`

Vue 版本的区别
- Vue 2.x 可以使用 Vue 实例作为自定义事件
- Vue 3.x 需要使用第三方的自定义事件，例如 https://www.npmjs.com/package/event-emitter

【注意】组件销毁时记得 `off` 事件，否则可能会造成内存泄漏

## $attrs

`$attrs` 存储是父组件中传递过来的，且未在 `props` 和 `emits` 中定义的属性和事件。<br>
相当于 `props` 和 `emits` 的一个补充。

继续向下级传递，可以使用 `v-bind="$attrs"`。这会在下级组件中渲染 DOM 属性，可以用 `inheritAttrs: false` 避免。

【注意】Vue3 中移除了 `$listeners` ，合并到了 `$attrs` 中。

## $parent

通过 `this.$parent` 可以获取父组件，并可以继续获取属性、调用方法等。

【注意】Vue3 中移除了 `$children` ，建议使用 `$refs`

## $refs

通过 `this.$refs.xxx` 可以获取某个子组件，前提是模板中要设置 `ref="xxx"`。

【注意】要在 `mounted` 中获取 `this.$refs` ，不能在 `created` 中获取。

## provide / inject

父子组件通讯方式非常多。如果是多层级的上下级组件通讯，可以使用 provide 和 inject 。<br>
在上级组件定一个 provide ，下级组件即可通过 inject 接收。

- 传递静态数据直接使用 `provide: { x: 10 }` 形式
- 传递组件数据需要使用 `provide() { return { x: this.xx } }` 形式，但做不到响应式
- 响应式需要借助 `computed` 来支持

## Vuex

Vuex 全局数据管理

## 答案

- 父子组件通讯
    - `props` `emits` `this.$emit`
    - `$attrs` （也可以通过 `v-bind="$attrs"` 向下级传递）
    - `$parent` `$refs`
- 多级组件上下级
    - `provide` `inject`
- 跨级、全局
    - 自定义事件
    - Vuex
