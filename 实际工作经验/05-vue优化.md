# Vue 优化

## 题目

你在实际工作中，做过哪些 Vue 优化？

## 前端通用的优化策略

压缩资源，拆包，使用 CDN ，http 缓存等。本节只讨论首屏，这些先不讲。

## v-if 和 v-show

区别
- `v-if` 组件销毁/重建
- `v-show` 组件隐藏（切换 CSS `display`）

场景
- 一般情况下使用 `v-if` 即可，普通组件的销毁、渲染不会造成性能问题
- 如果组件创建时需要大量计算，或者大量渲染（如复杂的编辑器、表单、地图等），可以考虑 `v-show`

## v-for 使用 key

`key` 可以优化内部的 diff 算法。注意，遍历数组时 `key` 不要使用 `index` 。

```html
<ul>
    <!-- 而且，key 不要用 index -->
    <li v-for="(id, name) in list" :key="id">{{name}}</li>
</ul>
```

## computed 缓存

`computed` 可以缓存计算结果，`data` 不变则缓存不失效。

```js
export default {
    data() {
        return {
            msgList: [ ... ] // 消息列表
        }
    },
    computed: {
        // 未读消息的数量
        unreadCount() {
            return this.msgList.filter(m => m.read === false).length
        }
    }
}
```

## keep-alive

`<keep-alive>` 可以缓存子组件，只创建一次。通过 `activated` 和 `deactivated` 生命周期监听是否显示状态。<br>
代码参考 components/KeepAlive/index.vue

场景
- 局部频繁切换的组件，如 tabs
- 不可乱用 `<keep-alive>` ，缓存太多会占用大量内存，而且出问题不好 debug

## 异步组件

对于体积大的组件（如编辑器、表单、地图等）可以使用异步组件
- 拆包，需要时异步加载，不需要时不加载
- 减少 main 包的体积，页面首次加载更快

vue3 使用 `defineAsyncComponent` 加载异步组件，代码参考 components/AsyncComponent/index.vue

## 路由懒加载

对于一些补偿访问的路由，或者组件提交比较大的路由，可以使用路由懒加载。

```js
const routes = [
  {
    path: '/',
    name: 'Home',
    component: Home
  },
  {
    path: '/about',
    name: 'About',
    // 路由懒加载
    component: () => import(/* webpackChunkName: "about" */ '../views/About.vue')
  }
]
```

## SSR

SSR 让网页访问速度更快，对 SEO 友好。

但 SSR 使用和调试成本高，不可乱用。例如，一个低代码项目（在线制作 H5 网页），toB 部分不可用 SSR ， toC 部分适合用 SSR 。

## 答案

- v-if 和 v-show
- v-for 使用 key
- computed 缓存
- keep-alive
- 异步组件
- 路由懒加载
- SSR

## 扩展

网上看到过一些“较真”的性能优化，对比普通组件和函数组件，JS 执行多消耗了几 ms 。
- 如果这些是为了探索、学习前端技术，非常推荐
- 但在实际项目中要慎用，不要为了优化而优化。肉眼不可见的 ms 级的优化，对项目没有任何实际价值

## 连环问：Vue 遇到过哪些坑？？？

全局事件、自定义事件要在组件销毁时解除绑定
- 内存泄漏风险
- 全局事件（如 `window.resize`）不解除，则会继续监听，而且组件再次创建时会重复绑定

Vue2.x 中，无法监听 data 属性的新增和删除，以及数组的部分修改 —— Vue3 不会有这个问题
- 新增 data 属性，需要用 `Vue.set`
- 删除 data 属性，需要用 `Vue.delete`
- 修改数组某一元素，不能 `arr[index] = value` ，要使用 `arr.splice` API 方式

路由切换时，页面会 scroll 到顶部。例如，在一个新闻列表页下滑到一定位置，点击进入详情页，在返回列表页，此时会 scroll 到顶部，并重新渲染列表页。所有的 SPA 都会有这个问题，并不仅仅是 Vue 。
- 在列表页缓存数据和 `scrollTop`
- 返回列表页时（用 Vue-router [导航守卫](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)，判断 `from`），使用缓存数据渲染页面，然后 `scrollTo(scrollTop)`
