# map parseInt

## 题目

`['1', '2', '3'].map(parseInt)` 输出什么？

## parseInt

`parseInt(string, radix)` 解析一个字符串并返回指定基数的**十进制**整数
- `string` 要解析的字符串
- `radix` 可选参数，数字基数（即进制），范围为 2-36

示例

```js
parseInt('11', 1) // NaN ，1 非法，不在 2-36 范围之内
parseInt('11', 2) // 3 = 1*2 + 1
parseInt('3', 2) // NaN ，2 进制中不存在 3
parseInt('11', 3) // 4 = 1*3 + 1
parseInt('11', 8) // 9 = 1*8 + 1
parseInt('9', 8) // NaN ，8 进制中不存在 9
parseInt('11', 10) // 11
parseInt('A', 16) // 10 ，超过 10 进制，个位数就是 1 2 3 4 5 6 7 8 9 A B C D ...
parseInt('F', 16) // 15
parseInt('G', 16) // NaN ，16 进制个位数最多是 F ，不存在 G
parseInt('1F', 16) // 31 = 1*16 + F
```

## radix == null 或者 radix === 0

- 如果 `string` 以 `0x` 开头，则按照 16 进制处理，例如 `parseInt('0x1F')` 等同于 `parseInt('1F', 16)`
- 如果 `string` 以 `0` 开头，则按照 8 进制处理 —— **ES5 之后就取消了，改为按 10 进制处理，但不是所有浏览器都这样，一定注意！！！**
- 其他情况，按 10 进制处理

## 分析代码

题目代码可以拆解为

```js
const arr = ['1', '2', '3']
const res = arr.map((s, index) => {
    console.log(`s is ${s}, index is ${index}`)
    return parseInt(s, index)
})
console.log(res)
```

分析执行过程

```js
parseInt('1', 0) // 1 ，radix === 0 按 10 进制处理
parseInt('2', 1) // NaN ，radix === 1 非法（不在 2-36 之内）
parseInt('3', 2) // NaN ，2 进制中没有 3
```

## 答案

```js
['1', '2', '3'].map(parseInt) // [1, NaN, NaN]
```

## 划重点

- 要知道 `parseInt` 参数的定义
- 把代码拆解到最细粒度，再逐步分析

## 扩展

为何 eslint 建议 `partInt` 要指定 `radix`（第二个参数）？<br>
因为 `parseInt('011')` 无法确定是 8 进制还是 10 进制，因此必须给出明确指示。
