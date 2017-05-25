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

### 重新认识innerHTML和DOM的方式创建DOM
使用innerHTML和纯DOM方法创建HTML表的比较结果如下图。innerHTML的好处在老式浏览器上显而易见，但是在新版浏览器上就不那么明显了。而在最新的Webkit浏览器上其结果正好相反：使用DOM方法更快。因此决定采用哪种方法取决于用户经常访问的浏览器，以及编码偏好。

### 节点克隆
使用DOM方法更新页面的另一个途径是克隆已有的DOM，而不是创建新的--即使用`element.cloneNode()`代替`document.createElement()`
在大多数浏览器上，克隆节点更有效率，但是提高并不多。

```js
document.getElementsByName();
document.getElementsByClassName();
document.getElementsByTagName_r();
document.images;
document.links;
document.forms;
document.forms[0].elements; // 页面中第一个表单的所有字段
```

