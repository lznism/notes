React提供清晰简洁的视图层(`View`)解决方案

DOM操作非常昂贵，前端开发中，性能消耗最大的就是DOM操作，而且这部分代码会让整体项目变得难以维护，React将真实的DOM转化为`JavaScript对象树`，也就是`virtual DOM`

React提供了直观的`shouldComponentUpdate`生命周期回调，来减少数据变化后不必要的`virtual DOM`对比过程，以保证性能

`virtual DOM`的构建和更新是在内存中完成的，并不会渲染到真实的DOM中

React中创建的元素可以分为两大类：`DOM元素`和`组件元素`，分别对应着`原生DOM元素`和`自定义元素`。

原生DOM元素用js对象可以描述为

```html
<button class="btn"><em>Confirm</em></button>
```
转化为`json`对象

```js
{
    type: 'button',
    props: {
        className: 'btn',
        children: {
            type: 'em',
            props: {
                children: text
            }
        }
    }
}
```

但如果我们封装一个`Button`,它就可表示为这样

```js
{
    type: 'Button',
    props: {
        color: 'blue',
        children: 'Confirm'
    }
}
```

React组件可以分为两大类
狭义上的组件：UI组件，如`Tabs`组件
广义的组件：即带有业务含义和数据UI组件的组合，在复杂的场景下，一般可以采用分层的思路去处理

React组件的3个组成部分`属性、状态、生命周期`

在合适的情况下，最佳实践是使用无状态组件

`props`：如果顶层组件初始化`props`,那么React会向下遍历整棵树，重新尝试渲染所有的相关子组件
`state`：只关心每个组件自己内部的状态，这些状态只能在组件内部改变

`props本身是不可变的`，如果我们试图改变`props`的原始值，React会给出错误警告

`propTypes`用于规范`props`的类型与必须的状态。如果组件定义了`propTypes`，那么在开发环境下，就会对组件的props值进行类型检查，如果传入的值不匹配，控制台就会报错

React生命周期可以分为两类
- 当组件被挂载或者卸载时
    + propTypes
    + defaultProps
    + componentWillMount
    + render
    + componentDidMount
    + componentWillUnmount
- 当组件接收到新的数据时



























