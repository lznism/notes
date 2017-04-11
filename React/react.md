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
    + componentWillReceiveProps
    + shouldComponentUpdate
    + componentWillUpdate
    + componentDidUpdate
    + render

`shouldComponentUpdate`是性能优化的主要方式之一

无状态组件总是会重新渲染，使用`Recompose`库的`pure`方法

```js
const OptimizedComponent = pure(ExpensiveComponent);
```

pure方法做的事情就是将无状态组件转化为`class`语法加上`PureRender`后的组件

使用`ES6`开发React主要的两点不同就是`static propTypes`, `static defaultProps`

`ReactDOM`中的API非常少，只有`findDOMNode`, `unmountComponentAtNode`, `render`

#### findeDOMNode
当组件被渲染到真实的DOM中时，`findDOMNode`返回该React组件实例相对应的DOM节点

#### render
`render`方法将组件挂载到`container`中，并且返回`element`实例。如果是无状态组件，返回的就是`null`
当React组件渲染完成需要更新时，会使用`DOM diff`算法做局部更新，这是React最大的亮点之一

如果将`refs`放到原生的DOM组件上，我们通过`refs`获得的就是DOM节点
如果将`refs`放到React组件上，我们获得的就是组件的实例，就可以调用组件上的实例方法

对于DOM操作，我们不仅可以使用`findDOMNode`获得组件DOM，还可以使用`refs`获得组件内部的DOM

```jsx
class App extends Component {
    componentDidMount() {
        const myComp = this.refs.myComp;
        const dom = findDOMNode(myComp);
    }
    render() {
        return <div><Comp ref='myComp'></div>;
    }
}
```

无论是`findDOMNode`还是`refs`都无法用于无状态组件中，原因是无状态组件挂载时只是方法调用，没有新建实例

并不推荐在React中使用React.findDOMNode来操作DOM.因为这在大部分情况下都打破了封装性，而且通常都能用更清晰的方法在React中构建代码

React基于`virtual DOM`实现了一个`SynthicEvent(合成事件层)`

在React底层，主要做了两件事情:`事件委派`, `自动绑定`
#### 事件委派
React并不会将事件绑定到真实的节点上，而是把事件绑定到结构的最外层，做一个统一的事件监听器，这个事件监听器维持了一个映射来保存所有的组件内部的事件监听和处理函数

#### 自动绑定
在React中每个方法的上下文都会自动指向该组件的实例，即自动绑定`this`到当前组件，但是在`ES6 classes`或者纯函数时，这种自动绑定就不复存在了，我们需要手动绑定this

方法一 `bind`
方法二 `stage-0`草案中的`::`

```jsx
<button onClick={::this.handleClick}>button</button>
```

方法三 `构造函数内声明`

```js
constructor(props) {
    super(props);
    this.handleClick = this.handleClick.bind(this);
}
```

方法四 `箭头函数`

```jsx
handleClick = () => {
    // ...code
}
// 或者
<button onClick={() => this.handleClick()}>button</button>
```

#### React中使用原生事件
原生事件必须在`componentDidMount`事件之后使用

```jsx
class NativeEventDemo extends Component {
    componentDidMount() {
        this.refs.button.addEventListener('click', e=> {}, false);
    }
}
```

#### 不要将合成事件和原生事件混用
阻止原生事件传播仅仅需要使用`e.preventDefault()`

#### React受控组件
1. 可以通过初始的`state`中设置表单的默认值
2. 每当表单的值变化时，调用`onChange`事件处理器
3. 事件处理器通过合成事件对象e拿到改变后的状态，并更新`state`
4. 使用setState来更新状态

#### React非受控组件
如果一个表单没有`value`和`props`，就可以成为非受控组件
可以通过`defaultValue`和`defaultChecked`来表示组件的默认状态

不提倡在React中使用非受控组件，这样会使数据流向混乱，不易于数据状态管理

React中写入的内联样式，像素值的结尾不要加`px`

```js
const style = {
    width: 100,
    height: 100,
    backgroundColor: 'red'
}
```

#### classnames库的用法
用来动态操作classnames

```js
const btnClass = ClassNames({
    'btn': true,
    'btn-pressed': this.state.isPressed,
    'btn-over': !this.state.isPressed && this.state.isHovered
})
```

CSS模块化解决的问题`css样式的导入和导出`

`CSS Modules`需要在`webpack.config.js`的`css-loader`后加上查询字段`?modules&localIdentName=[name]-[local]-[hash:base64:5]`
其中`modules`是按照模块化来解析css,`localIdentName`是指`className`的命名规则

CSS Modules实现了以下几个问题
- 所有的样式都是局部化的
- class命名规则可以使用webpack来灵活配置
- 只需要引用组件的JavaScript

样式默认为局部样式。以下两种写法等价

```css
.title {
    color: red;
}
:local(.title){
    color: red;
};
```

全局样式，如果使用的是全局样式，组件中的className用字符串即可

```css
/*定义全局样式*/
:global(.title) {
    color: red;
}
```

```jsx
//直接使用字符串即可
const App = () => <h1 className="title">Hello World</h1>;
```

CSS Modules 使用技巧
- 不使用选择器来定义样式
- 不层叠多个class，只使用一个class来将所有的样式定义好
- 所有的样式通过`composes`组合来实现复用

外部样式覆盖局部样式

```jsx
return <div className={style.root} data-role="dialog-root"></div>

// css
[data-role="dialog-root"] {
    // override css
}
```

可以将classnames库和CSS Module联系起来使用

```js
render() {
    const cx = classNames({
        confirm: !this.state.disabled,
        disabledConfirm: this.state.disabled
    });
    return <div className={styles.root}>
        <a className={styles[cx]}></a>
    </div>;
}
```

### 组件通信
1. 父组件向子组件通信
父组件通过`props`向子组件传递需要的信息

2. 子组件向父组件通信
主要的方法有两种
- 利用回调函数
- 利用自定义事件机制

```js
class App extends Component {
    constructor(props) {
        super(props);
        this.changeItem = this.changeItem.bind(this);
        this.state = {
            list: ['item1', 'item2'],
            curItem: 'item1'
        }
    }
    changeItem(item) {
        this.setState({
            curItem: item
        });
    }
    render() {
        return <div>
            the currentItem is : {this.state.curItem}
            <List list={this.state.list} changeItem={this.changeItem} />
        </div>
    }
}

class List extends Component {
    onClickItem = (item) => {
        this.props.changeItem(item);
    }
    render = () => <ul>
        {
            this.props.list.map((item, index) => (
                <li key={`list-${index}`} onClick={this.onClickItem.bind(this, item)}>i ma {item}, click me</li>
            ))
        }
    </ul>
}
```

还可以使用`context`来进行父子组件之间的传值，但是这个并不推荐使用
`context`就是全局变量，而全局变量正是导致应用混乱的原因

没有嵌套关系的组件，可以使用事件机制进行通讯

```js
componentDidMount() {
    this.itemChange = emitter.addListener('ItemChange', (msg, data) => console.log(data));
}
componentWillUnmount() {
    emitter.removeListener(this.itemChange);
}
```



















