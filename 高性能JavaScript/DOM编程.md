### 浏览器中的DOM
尽管DOM是与语言无关的API，在浏览器中的接口却是以JS实现的

### DOM的修改和访问
尽量减少对DOM的访问和修改，最佳实践
```js
function test(){
	var content = "";
	for(var count=0; count<15000; count++){
		content += 'a';
	}
	document.getElementById('here').innerHTML += content;
}
```

### 节点克隆
使用DOM方法更新页面内容的另一个途径是克隆已有的DOM元素，而不是创建新的--即使用`element.cloneNode()`代替`document.createElement()`
在大多数浏览器上，克隆节点更有效率但是提高不太多

### HTML Collections
- document.images
- document.links
- document.forms

HTML集合实际上查询文档，当你更新时，每次都要重复执行这种查询操作。例如集合元素的数目（也就是集合的length）
```js
// 死循环
var alldivs = document.getElementById('div');
for(var i = 0; i < alldivs.length; i++){
	document.body.appendChild(document.createElement('div'));
}
```
