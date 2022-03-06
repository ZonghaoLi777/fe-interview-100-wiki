# curry add

## 题目

写一个 `curry` 函数，可以把其他函数转为 curry 函数

```js
function add(a, b, c) { return a + b + c }
add(1, 2, 3) // 6

const curryAdd = curry(add)
curryAdd(1)(2)(3) // 6
```

## 解答

代码参考 curry.ts

## 总结

- 判断参数长度
- 中间态返回函数，最后返回执行结果
- 如用 this 慎用箭头函数
