# setState

## 题目

React 中以下代码会输出什么

```js
class Example extends React.Component {
    constructor() {
      super()
      this.state = { val: 0 }
    }
  
    componentDidMount() {
      // this.state.val 初始值是 0 

      this.setState({val: this.state.val + 1})
      console.log(this.state.val)
  
      this.setState({val: this.state.val + 1})
      console.log(this.state.val)
  
      setTimeout(() => {
        this.setState({val: this.state.val + 1})
        console.log(this.state.val)
  
        this.setState({val: this.state.val + 1})
        console.log(this.state.val)
      }, 0)
    }
  
    render() {
      return <p>{this.state.val}</p>
    }
}
```

## setState 默认异步更新

```js
componentDidMount() {
  this.setState({val: this.state.val + 1}, () => {
    // 回调函数可以拿到最新值
    console.log('callback', this.state.val)
  })
  console.log(this.state.val) // 拿不到最新值
}
```

## setState 默认会合并

多次执行，最后 render 结果还是 `1`

```js
componentDidMount() {
  this.setState({val: this.state.val + 1})
  this.setState({val: this.state.val + 1})
  this.setState({val: this.state.val + 1})
}
```

## setState 有时同步更新

根据 `setState` 的**触发时机是否受 React 控制**

如果触发时机在 React 所控制的范围之内，则**异步更新**
- 生命周期内触发
- React JSX 事件内触发

如果触发时机不在 React 所控制的范围之内，则**同步更新**
- setTimeout setInterval
- 自定义的 DOM 事件
- Promise then
- ajax 网络请求回调

## setState 有时不会合并

第一，同步更新，不会合并

第二，传入函数，不会合并 （对象可以 `Object.assign`，函数无法合并）

```js
this.setState((prevState, props) => {
  return { val: prevState.val + 1 }
})
```

## 答案

题目代码执行打印 `0 0 2 3`

## 重点

`setState` 是 React 最重要的 API ，三点：
- 使用不可变数据
- 合并 vs 不合并
- 异步更新 vs 同步更新
