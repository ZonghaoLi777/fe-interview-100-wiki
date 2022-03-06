# for vs forEach

## 题目

for 和 forEach 哪个更快？为什么

## 测试

代码参考 for-foreach.html ，测试结果：for 更快

## 创建函数需要开销

for 直接在当前函数中执行，forEach 每次都要新创建一个函数。
函数有单独的作用域和上下文（可回顾“堆栈模型”），所以耗时更久。

## 答案

for 更快，因为 forEach 每次创建函数需要开销

## 扩展

开发中不仅要考虑性能，还要考虑代码的可读性，forEach 可读性更好。
