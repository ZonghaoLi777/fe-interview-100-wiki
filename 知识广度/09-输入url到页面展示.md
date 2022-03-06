# 输入 url 到页面展示

## 题目

从输入 url 到显示页面的完整过程

## 特别注意

现在浏览器经过多年发展和优化，加载和渲染机制已经非常复杂，我们只能讲解基本的流程。不可较真细节。

## 步骤

- 网络请求
- 解析
- 渲染页面

## 网络请求

- DNS 解析，根据域名获得 IP 地址
- 建立 TCP 连接 “三次握手”
- 发送 http 请求
- 接收请求响应，获得网页 html 代码

获取了 html 之后，解析过程中还可能会继续加载其他资源：js css 图片等。<br>
静态资源可能会有强缓存，加载时要判断。

![](./img/cache-control.png)

## 解析

> html css 等源代码是字符串形式，需要解析为特定的数据结构，才能被后续使用。

过程
- html 构建 DOM 树
- css 构建 CSSOM（即 style tree）
- 两者结合形成 Render tree （包括尺寸、定位等）

![](./img/render.png)

css 包括：
- 内嵌 css `<style>`
- 外链 css `<link>`

解析到 `<script>` 加载，并有可能修改 DOM 树和 render tree 。
- 内嵌 js
- 外链 js

PS：加载和执行 `<script>` 的情况比较多，如有 `defer` `async` 属性，就不一样。

解析到 `<img>` 等媒体文件，也要并行加载。加载完成后再渲染页面。

综上，为了避免不必要的情况，要遵守以下规则
- css 尽量放在 `<head>` 中，不要异步加载 css
- js 尽量放在 `<body>` 最后，不要中途加载、执行 js
- `<img>` 等媒体文件尽量限制尺寸，防止渲染时重绘页面

## 渲染页面

通过 render tree 绘制页面。

绘制完成之后，还要继续执行异步加载的资源
- 异步的 css ，重新渲染页面
- 异步的 js ，执行（可能重新渲染页面）
- 异步加载的图片等，可能重新渲染页面（根据图片尺寸）

最后页面渲染完成。

## 答案

- 网络请求
    - DNS 解析
    - TCP 连接
    - HTTP 请求和响应
- 解析
    - DOM 树
    - render tree
- 渲染页面
    - 可能重绘页面

## 连环问：什么是重绘 repaint 和重排 reflow ，有何区别

页面渲染完成之后，随着异步加载和用户的操作，会随时发生 repaint 或者 reflow 。例如
- 各种网页动画
- modal dialog 弹框
- 页面元素的新增、删除和隐藏

结论：重排的影响更大
- 重绘 repaint ：某些元素的外观被改变，但尺寸和定位不变，例如：元素的背景色改变。
- 重排 reflow ：重新生成布局，重新排列元素。如一个元素高度变化，导致所有元素都下移。

重绘不一定重排，但重排一定会导致重绘。<br>
所以，要尽量避免重排。

- 集中修改样式，或直接使用 `class`
- DOM 操作前先使用 `display: none` 脱离文档流
- 使用 BFC ，不影响外部的元素
- 对于频繁触发的操作（`resize` `scroll` 等）使用节流和防抖
- 使用 `createDocumentFragment` 进行批量 DOM 操作
- 优化动画，如使用 `requestAnimationFrame` 或者 CSS3（可启用 GPU 加速）

## 连环问：触发 css BFC 的条件

BFC - Block Formatting Context 块格式化上下文
- 根节点 html
- 设置 float `left` `right`
- 设置 overflow `auto` `scroll` `hidden`
- 设置 display `inline-block` `table` `table-cell` `flex` `grid`
- 设置 position `absolute` `fixed`
