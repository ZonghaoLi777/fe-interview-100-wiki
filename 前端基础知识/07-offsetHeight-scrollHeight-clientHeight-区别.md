# offsetHeight scrollHeight clientHeight 区别

## 题目

offsetHeight scrollHeight clientHeight 区别

## 盒子模型

- width height
- padding
- border
- margin
- **box-sizing**

## offsetHeight offsetWidth

包括：border + padding + content

## clientHeight clientWidth

包括：padding + content

## scrollHeight scrollWidth

包括：padding + 实际内容的尺寸

## scrollTop scrollLeft

DOM 内部元素滚动的距离

## 答案

- offsetHeight - border + padding + content
- clientHeight - padding + content
- scrollHeight - padding + 实际内容的高度

---

代码参考 offset-height.html
