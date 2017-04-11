每个组件都有自己的生命周期，它规定了组件的状态和方法需要在哪个节点改变和执行
### 使用createClass创建自定义组件
createClass是创建自定义组件的入口方法，负责管理生命周期中的getDefaultProps方法，该方法在整个生命周期中只执行一次

### mounting
这个阶段mountComponent负责管理生命周期中的getInitialState, ComponentWillMount, render, ComponentDidMount
本节点无法再次调用getDefaultProps方法
如果的ComponentWillMount中调用setState方法，组件并不会重新渲染，原因是此时组件根本就还没加载完成
mountComponent本质上是通过递归来渲染内容的，由于递归的特殊性，父组件的ComponentWillMount在子组件的ComponentWillMount之前调用，父组件的ComponentDidMount在子组件的ComponentDidMount之后调用

### recieve_props
updateComponent负责管理生命周期中的componentWillRecieveProps, shouldComponentUpdate, componentWillUpdate, render, componentDidUpdate
禁止在shouldComponentUpdate和componentWillUpdate中使用this.setState(), 因为这会导致循环调用, 造成死循环

```js
var recieveComponent = (nextElement, transaction, nextContext) => {
	// 这里会调用updateComponent
}
var updateComponent = () => {
	// ...
}
```

### unmonting
unmountComponent负责管理生命周期中的componentWillUnmount, 在本生命周期中，调用setState方法不会重新渲染，因为此时所有的更新队列和更新状态都被重置为null，并清楚了公共类，完成了组件的卸载工作

### 无状态组件
无状态组件只有一个生命周期方法render