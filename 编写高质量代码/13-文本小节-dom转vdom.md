# DOM 转 VDOM

注：文字小节

## 题目

讲以下 DOM 结构转换为 vnode 数据

```html
<div id="div1" style="border: 1px solid #ccc; padding: 10px;">
    <p>一行文字<a href="xxx.html" target="_blank">链接</a></p>
    <img src="xxx.png" alt="图片" class="image"/>
    <button click="clickHandler">点击</button>
</div>
```

## 答案

vdom 就是用 JS 对象的形式来表示 DOM 结构。vnode 即对应着 DOM 结构的一个 node 节点。

```js
const vnode = {
    tag: 'div', // <div>
    data: {
        id: 'div1',
        style: {
            'border': '1px solid #ccc',
            'padding': '10px'
        }
    },
    children: [
        {
            tag: 'p', // <p>
            data: {},
            children: [
                '一行文字',
                {
                    tag: 'a', // <a>
                    data: {
                        href: 'xxx.html',
                        target: '_blank'
                    },
                    children: ['链接']
                }
            ]
        },
        {
            tag: 'img', // <img>
            data: {
                className: 'image', // 注意，这里要用 className
                src: 'xxx.png',
                alt: '图片'
            }
        },
        {
            tag: 'button', // <button>
            data: {
                events: {
                    click: clickHandler
                }
            }
            children: ['点击']
        }
    ]
}
```

## 注意事项

- vdom 结构没有固定的标准，例如 `tag` 可以改为 `name` ，`data` 可以改为 `props` 。只要能合理使用 JS 数据表达 DOM 即可。
- `style` 和 `events` 要以对象的形式，更易读，更易扩展
- `class` 是 ES 内置关键字，要改为 `className` 。其他的还有如 `for` 改为 `htmlFor`
