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

### constructor
ES6中每一个class要创造一个实例，都需要调用constructor

constructor的目的有两个

1. 初始化state
2. 绑定成员函数的this环境(此步奏可以使用箭头函数省略)

### getInitialState/getDefaultProps
获取组件的初始化状态和默认的参数,es6中的写法如下

```js
class Sample extends Component {}
Sample.defaultProps = {}
```

### render
渲染，本函数不做实际的渲染动作，他只是返回一个JSX描述的的结构，最终的渲染由react来完成

注意render函数是一个纯函数，请勿在render方法中调用`this.setState`

### componentWillMount/componentDidMount
`componentWillMount`会在render函数之前调用；`componentDidMount`会在render函数之后调用

`componentDidMount`只能在浏览器端调用，不能在服务器端调用

`componentDidMount`被调用时，组件已经被装载到DOM树上，可以放心获取渲染出来任何DOM

### 组件的更新过程
组件的更新过程依次会执行以下几个生命周期方法

- componentWillReceiveProps

	只要是父组件的render方法被调用，在render函数里面被渲染的子组件就会经历更新过程，不管父组件传给子组件的props有没有改变，都会触发子组件的componentWillReceiveProps

	在本生命周期内部调用`this.setState`或造成死循环

- shouldComponentUpdate

	shouldComponentUpdate(nextProps, nextState) 应该有一个返回值，决定了当前组件是不是被渲染

	如果返回true，就会继续执行接下来的渲染过程，如果返回的是false，渲染过程就会立刻停止

	该函数能够大大提升react的性能，我们要做的实际上就是nextProps/nextState和this.props/this.state的对比

- componentWillUpdate
- render
- componentDidUpdate

	`componentDidUpdate`既可以在浏览器上又可以在服务器端使用

### 卸载过程
React组件的卸载过程只涉及一个函数componentWillUnmount，其工作往往和componentDidMount有关，比如componentDidMount中用非react创建了一些元素，如果不清理的话可能会导致内存泄漏，那就需要在componentWillUnmount中清理掉