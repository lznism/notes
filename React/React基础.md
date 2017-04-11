#### JSX
* **boolean属性**

省略boolean的值得属性，会默认为true,比如下面的代码

```html
<checkbox checked />
```

如果需要指定true或者false，可以像下面这样使用

```html
<checkbox checked={false} />
```

类似的属性还有`required, disabled, readOnly`

* **自定义HTML属性**

如果要在HTML自定义属性，必须要使用`data-*`的属性，比如

```html
<div data-index="1">Hello World</div>
```

但是在自定义组件中可以自由使用任何形式的属性

* **属性表达式**

这里需要注意的是只能使用三目运算符

#### React组件
* **React组件的构建**

React组件的三个组成部分`props, state, 生命周期钩子`

在实际的开发中我们需要将React组件划分到合理的粒度，这样当数据发生变化不至于让整个UI重新渲染，但是究竟要到什么样的粒度，这里`是个坑`

* **无状态组件**

无状态组件只传入`props`和`context`两个参数，不存在state,没有生命周期。如果需要使用defaultProps, propTypes可以往方法上设置静态属性
使用无状态组件的优点：无状态组件在创建时始终保持了一个实例，避免了不必要的检查和内存分配，做到了内部优化

#### 组件数据流
* **state**

setState是一个异步方法，一个生命周期中，所有的stateState操作都会合并，比如下面的计数器组件，点击add最终状态只会加一，而不是加三

```html
class Counter extends Component {
	state = {
		count: 0
	}
	handleClick = () => {
		this.setState({
			count: this.state.count + 1
		});
		this.setState({
			count: this.state.count + 1
		});
		this.setState({
			count: this.state.count + 1
		});
	}
	render() {
		return <div>
			<button onClick={this.handleClick}>add</button>
			<p>{this.state.count}</p>
		</div>
	}
}
```

那么如何选择到底使用state更新组件还是使用props更新组件，这里又是`另外一个坑`

* **props**

props是React用来让组件之间互相联系的一种机制，通俗的说就像方法的参数一样。props是不可变的，更改props会报错
默认的props能保证渲染后始终有值

特殊的props--children,代表了子组件的集合
通过React.Children.map来遍历子组件

在props中也可以传递组件，类似于下面的样子

```html
<TabPane tab={<i className="fa fa-home"></i>}></TabPane>
```
React中的几种propTypes

```js
array: React.PropTypes.array,
bool: React.PropTypes.bool,
func: React.PropTypes.func,
number: React.PropTypes.number,
object: React.PropTypes.object,
string: React.PropTypes.string,
symbol: React.PropTypes.symbol,
// 任何类型
node: React.PropTypes.node,
// React元素
element: React.PropTypes.element,
// 一个类的实例
instance: React.PropTypes.instanceOf(Message),
// 枚举
enum: React.PropTypes.oneOf(['News', 'Photos']),
// 联合类型
union: React.PropTypes.oneOfType([
	React.PropTypes.bool,
	React.PropTypes.element,
	React.PropTypes.string
]);
// 确定类型的数组
arrayOf: React.PropTypes.arrayOf(React.PropTypes.number),
// 确定属性的对象
ObjectwidthShape: React.PropTypes.shap({
	color: React.PropTypes.string,
	fontSize: React.PropTypes.number
})
```

#### 生命周期
`componentWillUnmount`中一般放入一些清理方法，例如清理定时器，移除事件绑定等等

组件更新三个连续的生命周期方法
shouldComponentUpdate --> componentWillUpdate --> componentDidUpdate

shouldComponentUpdate接受需要更新的props和state,我们可以增加适当的判断条件，如果返回的是false,那么就不会执行接下来的生命周期方法，这里就能让组件在需要时更新，在不需要时就不更新
componentWillUpdate 提供需要更新的props和state
componentDidUpdate  提供更新前的props和state

#### React中的DOM操作
React并不推荐直接操作DOM，但是基于React的模式或者业务需求我们不得不操作DOM时，我们可以用下面的方法

- findDOMNode
- unmountComponentAtNode
- render

在componentDidUpdate和componentDidMount中我们可以获取真正的DOM

```js
ReactDOM.findDOMNode(ReactComponent);
```

findDOMNode只对已经挂载的组件有效

render的主要作用是将VDOM渲染到真实的DOM中

React克隆节点
React.cloneElement(el, props, children)

#### ref
ref可以是一个函数，这个函数在组件被挂载之后立即执行，比如下面的例子

```html
<input type="text"  ref={ref => this.myTextInput = ref}/>
```

为了防止内存泄漏，在组件被卸载时，refs的引用都会变为null






















