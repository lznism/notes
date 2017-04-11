`Virtual DOM`中的节点成为ReactNode,它分为三种类型`ReactElement, ReactFragment, ReactText`;
`ReactElement`又分为`ReactComponentElement, ReactDOMElement` 
React几乎所有的工作都在Virtual DOM上完成，Virtual DOM模型负责Virtual DOM底层框架的构建工作，他拥有一整套的VDOM标签，负责虚拟节点的增删改查

构建虚拟DOM所需的四个基本要素
- 标签名
- 节点属性
- 子节点
- 标识ID

### 创建React元素
通过JSX创建的虚拟元素最终都会调用React的createElement方法

### 初始化组件入口
当React创建组件时，首先会调用`instantiateReactComponent`，这个是初始化组件的入口，它通过判断Node的类型来区分不同组件入口

```js
// 初始化组件入口
function instantiateComponent(node, parentCompoisteType) {}
```

如果node是空，说明node不存在，则初始化空组件`ReactEmptyComponent.create(instantiateComponent)`
如果node是对象，说明是DOM标签组件或者自定义组件
- DOM标签组件 ---> ReactNativeComponent.createInternalComponent
- 自定义组件  ---> ReactCompositeComponentWrapper()

如果node是文本字符串或者数字，则初始化为文本组件`ReactNativeComponent.createInstanceReactComponent`

其余的类型不作处理

### 文本组件
使用ReactDOMTextComponent封装文本组件，这样做的好处在createElement是会给文本组件加上唯一标识，拥有了VDOM DIFF的权利

ReactDOMTextComponent的原型中拥有三个主要方法
- mountComponent   判断这个文本是不是使用createElement创建，如果是，就会给其加上domID和标签
- construct 
- recieveComponent 更新文本内容

### DOM标签组件
虚拟DOM中几乎涵盖了所有的HTML标签，但是这些其实是VDOM中对象，只不过标签名称相同而已，React不直接操作DOM和污染原生DOM。这样不仅能保证性能还能降低风险
React针对vDOM的标签处理主要分为两个
- 更新属性(样式、事件、属性)

如果存在事件，则对当前的节点添加事件代理`enquePutListener`
如果存在样式，会对样式进行合并`Object.assign({}, props.style)`
普通的创建样式
添加唯一标识(方便DIFF)
当执行`recieveComponent`方法时，ReactDOMComponent会通过`updateComponent()`来更新DOM属性，主要有以下不走
1. 删除不需要的就属性(事件、样式)
2. 更新新属性

- 子节点的更新(更新内容、更新子节点)
1. 先删除不需要的子节点
2. 更新子节点的内容

当卸载组件时，会进行一系列的操作，如卸载子节点，清除事件监听，清空标识
































