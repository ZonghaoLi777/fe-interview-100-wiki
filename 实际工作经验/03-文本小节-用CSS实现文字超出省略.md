# 文字超出省略

注：文本小节

## 题目

文字超出省略，用哪个 CSS 样式？

## 分析

如果你有实际工作经验，实际项目有各种角色参与。页面需要 UI 设计，开发完还需要 UI 评审。<br>
UI 设计师可能是这个世界上最“抠门”的人，他们都长有像素眼，哪怕差 1px 他们都不会放过你。所以，开发时要严格按照视觉稿，100% 还原视觉稿。

但如果你没有实际工作经验（或实习经验），仅仅是自学的项目，或者跟着课程的项目。没有 UI 设计师，程序员的审美是不可靠的，肯定想不到很多细节。

所以，考察一些 UI 关注的细节样式，将能从侧面判断你有没有实际工作经验。

## 答案

单行文字

```css
#box1 {
    border: 1px solid #ccc;
    width: 100px;
    white-space: nowrap; /* 不换行 */
    overflow: hidden;
    text-overflow: ellipsis; /* 超出省略 */
}
```

多行文字

```css
#box2 {
    border: 1px solid #ccc;
    width: 100px;
    overflow: hidden;
    display: -webkit-box; /* 将对象作为弹性伸缩盒子模型显示 */
    -webkit-box-orient: vertical; /* 设置子元素排列方式 */
    -webkit-line-clamp: 3; /* 显示几行，超出的省略 */
}
```

## 扩展

UI 关注的问题还有很多，例如此前讲过的移动端响应式，Retina 屏 1px 像素问题。

再例如，网页中常用的字号，如果你有工作经验就知道，最常用的是 `12px` `14px` `16px` `20px` `24px` 等。你如果不了解，可以多去看看各种 UI 框架，例如 [antDesign 排版](https://ant.design/components/typography-cn/)。
