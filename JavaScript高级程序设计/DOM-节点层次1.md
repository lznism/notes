`<html>`称为文档元素，文档元素是文档的最外层元素，文档中的其他元素都包含在文档元素中

#### Node类型
Javascript中所有的节点类型都是继承自`Node`类型，因此所有的节点类型都共享着相同的基本属性和方法

```js
// 以下判断节点类型的方法适用于所有的浏览器
if(someNode.nodeType === 1){
	alert('this node is a element');
}
```

节点支持的属性
- nodeName 元素的标签名
- nodeValue 节点的值

节点关系
每个节点都有一个childNodes属性，其中都保存着一个NodeList对象。NodeList是一种类数组对象，用于保存一组有序的节点，`DOM中的变化能自动反应在NodeList中`
使用`slice`方法将类数组转化为数组

```js
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