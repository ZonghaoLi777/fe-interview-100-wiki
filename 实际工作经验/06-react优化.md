# React 优化

## 题目

你在实际工作中，做过哪些 React 优化？

## 前端通用的优化策略

压缩资源，拆包，使用 CDN ，http 缓存等。本节只讨论首屏，这些先不讲。

## 循环使用 key

`key` 可以优化内部的 diff 算法。注意，遍历数组时 `key` 不要使用 `index` 。

```jsx
const todoItems = todos.map((todo) =>
  {/* key 不要用 index */}
  <li key={todo.id}>
    {todo.text}
  </li>
)
```

## 修改 css 模拟 `v-show`

条件渲染时，可以通过设置 css 来处理显示和隐藏，不用非得销毁组件。模拟 Vue `v-show`

```jsx
{/* 模拟 v-show */}
{!flag && <MyComponent style={{display: 'none'}}/>}
{flag && <MyComponent/>}
```

或者

```jsx
{/* 模拟 v-show */}
<MyComponent style={{display: flag ? 'block' : 'none'}}/>
```

## 使用 Fragment 减少层级

组件层级过多，如果每个组件都以 `<div>` 作为 root ，则 DOM 层级太多而难以调试。

```jsx
render() {
  return <>
      <p>hello</p>
      <p>world</p>
  </>
}
```

## JSX 中不要定义函数

JSX 是一个语法糖，它和 Vue template 一样，最终将变为 JS render 函数，用以生成 vnode 。<br>
所以，如果在 JSX 中定义函数，那么每次组件更新时都会初始化该函数，这是一个不必要的开销。<br>
可回顾之前的面试题： `for 和 forEach 哪个更快`

```jsx
{/* Bad */}
<button onClick={() => {...}}>点击</button>
```

更好的解决方案是提前定义函数，在 JSX 中只引用执行。

```jsx
// Good
class MyComponent extends React.Component {
    clickHandler = () => { /*  */ }
    render() {
        return <>
            <button onClick={this.clickHandler}>点击</button>
        </>
    }
}
```

注意
- 如果你的系统不够复杂，这个优化几乎看不出效果，因为 JS 执行非常快 —— 但是，面试说出来肯定是一个加分项～
- 如果你用的是函数组件，这个优化方案不适用。如下代码：

```jsx
function App() {
  // 函数组件，每次组件更新都会重新执行 App 函数，所以内部的 clickHandler 函数也会被重新创建，这跟在 JSX 中定义是一样的
  // 不过 React 提供了 useCallback 来缓存函数，下文讲

  function clickHandler() {
    // ...
  }

  return (
    <>
      <button onClick={clickHandler}>点击</button>
    </>
  )
}
```

## 在构造函数 bind this

同理，如果在 JSX 中 bind this ，那每次组件更新时都要 bind 一次。在构造函数中 bind 更好。<br>
或者，直接使用箭头函数。

```jsx
class MyComponent extends React.Component {
    constructor() {
        // 要在构造函数中 bind this ，而不是在 JSX 中
        this.clickHandler1 = this.clickHandler1.bind(this)
    }
    clickHandler1() { /* 如果 JSX 中直接调用，则 this 不是当前组件。所以要 bind this */ }
    clickHander2 = () => { /* 使用箭头函数，不用 bind this */ }
    render() {
        return <>
            <button onClick={this.clickHandler1}>点击</button>
        </>
    }
}
```

PS：如果是函数组件，则不用 bind this

## 使用 shouldComponentUpdate 控制组件渲染

React 默认情况下，只要父组件更新，其下所有子组件都会“无脑”更新。如果想要手动控制子组件的更新逻辑
- 可使用 `shouldComponentUpdate` 判断
- 或者组件直接继承 `React.PureComponent` ，相当于在 `shouldComponentUpdate` 进行 props 的**浅层**比较

但此时，必须使用**不可变数据**，例如不可用 `arr.push` 而要改用 `arr.concat`。考验工程师对 JS 的熟悉程度。<br>
代码参考 components/SimpleTodos/index.js 的 class 组件。

不可变数据也有相应的第三方库
- [immutable.js](https://www.npmjs.com/package/immutable)
- [immer](https://www.npmjs.com/package/immer) —— 更加推荐，学习成本低

PS：React 默认情况（子组件“无脑”更新）这本身并不是问题，在大部分情况下并不会影响性能。因为组件更新不一定会触发 DOM 渲染，可能就是 JS 执行，而 JS 执行速度很快。所以，性能优化要考虑实际情况，不要为了优化而优化。

## React.memo 缓存函数组件

如果是函数组件，没有用 `shouldComponentUpdate` 和 `React.PureComponent` 。React 提供了 `React.memo` 来缓存组件。<br>
代码参考 FunctionalTodoList.js

`React.memo` 也支持自行比较

```js
function MyComponent(props) {
}
function areEqual(prevProps, nextProps) {
    // 自行比较，像 shouldComponentUpdate
}
export default React.memo(MyComponent, areEqual);
```

## useMemo 缓存数据

在函数组件中，可以使用 `useMemo` 和 `useCallback` 缓存数据和函数。

```jsx
function App(props) {
    const [num1, setNum1] = useState(100)
    const [num2, setNum2] = useState(200)

    const sum = useMemo(() => num1 + num2, [num1, num2]) // 缓存数据，像 Vue computed

    // const fn1 = useCallback(() => {...}, [...]) // 缓存函数

    return <p>hello {props.info}</p>
}
```

PS: 普通的数据和函数，没必要缓存，不会影响性能的。一些初始化比较复杂的数据，可以缓存。

## 异步组件

和 Vue 异步组件一样

```jsx
import React, { lazy, Suspense } from 'react'

// 记载异步组件
const OtherComponent = lazy(
  /* webpackChunkName: 'OtherComponent'*/
  () => import('./OtherComponent')
)

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}> {/* 支持 loading 效果 */}
        <OtherComponent />
      </Suspense>
    </div>
  )
}
```

## 路由懒加载

和 Vue-router 路由懒加载一样

```js
import React, { lazy, Suspense } from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

const Home = lazy(() => import('./Home')); 
const List = lazy(() => import(/* webpackChunkName: 'Home'*/ './List'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Switch>
        <Route exact path="/" component={Home}/>
        <Route path="/list" component={List}/>
      </Switch>
    </Suspense>
  </Router>
);
```

## SSR

同 Vue SSR

## 答案

- 循环使用 key
- 修改 css 模拟 `v-show`
- 使用 Fragment 减少层级
- JSX 中不要定义函数
- 在构造函数 bind this
- 使用 shouldComponentUpdate 控制组件渲染
- React.memo 缓存函数组件
- useMemo 缓存数据
- 异步组件
- 路由懒加载
- SSR

## 面试连环问：React 遇到哪些坑？

JSX 中，自定义组件命名，开头字母要大写，html 标签开头字母小写

```jsx
{/* 原生 html 组件 */}
<input/>

{/* 自定义组件 */}
<Input/>
```

JSX 中 `for` 写成 `htmlFor` ， `class` 写成 `className`

```js
{/* for 改成 htmlFor ，class 要改为 className */}
<label htmlFor="input-name" className="xxx">
    姓名 <input id="input-name"/>
<label>
```

state 作为不可变数据，不可直接修改，使用纯函数

```js
// this.state.list.push({...}) // 错误，不符合 React 规范
this.setState({
    list: curList.concat({...}) // 使用**不可变数据**
})
```

JSX 中，属性要区分 JS 表达式和字符串

```js
<Demo position={1} flag={true}/>
<Demo position="1" flag="true"/>
```

state 是异步更新的，要在 callback 中拿到最新的 state 值

```js
const curNum = this.state.num
this.setState({
    num: curNum + 1
}, () => {
    console.log('newNum', this.state.num) // 正确
})
// console.log('newNum', this.state.num) // 错误
```

React Hooks 有很多限制，注意不到就会踩坑。例如，`useEffect` 内部不能修改 state

```js
function App() {
    const [count, setCount] = useState(0)

    useEffect(() => {
        const timer = setInterval(() => {
            setCount(count + 1) // 如果依赖是 [] ，这里 setCount 不会成功
        }, 1000)

        return () => clearTimeout(timer)
    }, [count]) // 只有依赖是 [count] 才可以，这样才会触发组件 update

    return <div>count: {count}</div>
}

export default App
```

再例如，`useEffect` 依赖项（即第二个参数）里有对象、数组，就会出现死循环。所以，依赖项里都要是值类型。<br>
因为 React Hooks 是通过 `Object.is` 进行依赖项的前后比较。如果是值类型，则不妨碍。
如果是引用类型，前后的值是不一样的（纯函数，每次新建值），就类似 `{x:100} !== {x:100}`

```js
useEffect(() => {
    // ...
}, [obj, arr])
```

## 面试连环问：setState 是同步还是异步？

前端经典面试题。先作为思考题，后面会结合代码详细讲解。
