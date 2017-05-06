`<html>`称为文档元素，文档元素是文档的最外层元素，文档中的其他元素都包含在文档元素中

## Node类型
Javascript中所有的节点类型都是继承自`Node`类型，因此所有的节点类型都共享着相同的基本属性和方法
`IE8-`没有公开Node类型的构造函数

```js
// 以下判断节点类型的方法适用于所有的浏览器
if(someNode.nodeType === 1){
	alert('this node is a element');
}
document.body.nodeValue === undefined; // true 元素节点的nodeValue为undefined
```

节点支持的属性
- nodeName 元素的标签名
- nodeValue 节点的值

节点关系
每个节点都有一个childNodes属性，其中都保存着一个NodeList对象。NodeList是一种类数组对象，用于保存一组有序的节点，`DOM中的变化能自动反应在NodeList中`
使用`slice`方法将类数组转化为数组
**NodeList**是有生命的，有呼吸的对象，并不是第一次访问它的时候拍摄下来的一张快照

```js
// IE8-不能使用这个方法
Array.prototype.slice.call(nodes, 0);
```

每一个节点都有一个`parentNode`属性，该属性只想文档树中的父节点。
同胞节点`previousSibling`和`nextSibling`属性可以访问当上一个和下一个兄弟节点
列表中的第一个节点的前一个兄弟节点 和 最后一个节点的下一个兄弟节点是`null`

```js
if(someNode.nextSibling === null) {
	alert('last node');
} else if(someNode.previousSibling === null) {
	alert('first node');
}
```

父节点中的两个特殊节点`firstChild`和`lastChild`;第一个子节点和最后一个子节点
如果没有子节点两个属性均返回`null`

判断一个节点有没有子节点 `element.hasChildNodes()`

操作节点
向`childNodes`列表的末尾添加一个节点使用`appendChild()`。该方法返回新增的节点

```js
var returnedNode = someNode.appendChild(newNode);
alert(returnedNode === newNode); // true
```

如果`appenChild()`接受的是一个已经存在于文档中的节点，那么会由原来的位置转移到新的位置

```js
var returnedNode = someNode.appendChild(someNode.firstChild);
```

`insertBefore()`如果需要把节点放在 childNodes 列表中某个特定的位置上，而不是放在末尾，那么可以使用
insertBefore()方法

```js
// 插入之后成为最后一个子节点
// 调用insertBefore方法的应该是元素的父节点
var returnedNode = someNode.insertBefore(newNode, null);
alert(newNode == someNode.lastChild); // true

// 插入之后成为第一个子节点
var returnedNode = someNode.insertBefore(newNode, someNode.firstChild);
```

替换节点`replaceChild()` 该节点所有的指针会从被他替换的节点复制过来，尽管从技术上讲，被替换的节点仍然还在文档中，但它在文档中已经没有了自己的位置。

如果只想移除而非替换节点，可以使用`removeChild()`方法。这个方法接受一个参数，即要移除的节点

```js
var formerFirstChild = someNode.removeChild(someNode.firstChild);
```

克隆节点
`cloneNode()` 不会复制事件处理程序
- 深复制 复制整个文档树，包括所有的子节点
- 浅复制 复制节点本身

处理文档树中的文本节点`normalize()`

#### 总结
节点关系
- childNodes
- parentNode
- previousSibling
- nextSibling
- firstChild
- lastChild
- hasChildNodes()

操作节点
- appendChild()
- insertBefore()
- removeChild()
- replaceChild()
- cloneNode()
- normalize()

## Document
#### 文档的子节点
`document.documentElement`始终指向的是HTML元素
`childNodes` 这个会包含`doctype`和`html`

document对象还有一些特殊的集合，为访问文档常用的部分提供了快捷方式
`document.doctype` 文档类型声明，但是在IE8-时，会错误的解释为Comment节点
`document.anchors` 包含文档中带有name特性的a标签
`document.applets` 包含文档中所有的`<applet>`元素
`document.forms` 包含文档中所有的`<form>`元素
`document.images` 包含文档中所有的`<img>`元素
`document.links` 包含文档中所有带有`href`的`<a>`元素

`document.domain`的设置也是跨域的一种形式，可以对`document.domain`设置为相同的值，但是这里也会存在一个限制
举个例子
在`www.a.com`中设置了一个`blog.a.com`的iframe, 本身这两个之间是无法通讯的
但是可以将`document.domain`设置为`a.com`这样两者之间就可以正常通信

DOM一致性检测
`document.implementation` 检测浏览器实现了DOM的哪些部分
`document.implementation.hasFeature()` 检测某个功能是否存在

##### IE8-对于document.getElementById的不同表现
`document.getElementById()`
- 在IE8-下面，不区分ID的大小写
- IE8- 时这个方法也可以将表单元素查找出来，详情请看下面的例子

```html
<input name="aaa" />
<div id="aaa"></div>
```

对于上面的代码使用`document.getElementById('aaa')`则会返回`input`元素

##### document.getElementByTagName()
这个返回的是一个动态的集合

#### namedItem()
返回的是一个元素

#### document.getElementsByName()
返回的是一个集合

#### DOM一致性检测
本方法存在漏洞
返回true的时候也可能没有这些DOM功能，建议在检测某些特殊的DOM功能时候，应该加入能力检测
```js
var hasXmlDom = document.implementation.hasFeature('XML', 1.0);
```

#### 文档写入功能
- write() 
- wirteln() 在写入的字符串末尾加上一个\n
- open()
- close()

#### Element类型
- nodeType的值为1
- nodeName的值为元素的标签名
- nodeValue的值为null

```js
var div = document.getElementById('div');
alert(div.tagName); // "DIV"
```

注意上面返回的值是大写

#### 取得属性
`getAttribute()`, `setAttribute()`, `removeAttribute()`
取得属性时，如果对应的属性不存在会返回null
`getAttribute()`方法也可以取得自定义属性值

```html
<div my_special_attr="hello"></div>
```

#### 设置属性
通过`setAttribute()`方法设置的特姓名会被统一转化为小写形式

```js
div.setAttribute('id', 'div1');
```

#### attributes属性
- getNamedItem(name) 返回nodeName属性等于name的节点
- removeNamedItem(name) 从列表中移除nodeName属性等于name的节点
- setNamedItem(node) 向列表中移除nodeName属性等于name的节点
- item(pos) 返回位于数字pos位置处的节点

```js
var id = element.attributes.getNamedItem('id').nodeValue;
```

#### 创建元素
`document.createElement()`

#### 元素的子节点
这些子节点可能是元素节点，文本节点，注释或处理指令

```html
<ul id="mylist">
    <li>item</li>
    <li>item</li>
    <li>item</li>
</ul>
```