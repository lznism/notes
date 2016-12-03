在通过JavaScript处理XML时，通常只使用参数root，因为这个参数指定的是XML DOM文档元素的标签名。
因此要创建一个新的、文档元素为`<root>`的XML文档，可以使用如下代码
```js
var xmldom = document.implementation.createDocument("", "root", null);
```

### DOMParser类型
为了将XML解析为DOM文档，fireFox首先引入DOMParser类型
```js
var parser = new DOMParser();
var xmldom = parser.parseFromString("<root><child /></root>", "text/xml");
```

### XMLSerializer
将DOM文档序列化为XML字符串
```js
var serializer = new XMLSerializer();
var xml = serializer.serializeToString(xmldom);
alert(xml);
```

### 浏览器对XPath的支持
XPath是设计用来在DOM文档中查找节点的一种手段，因而对XML处理也很重要。
要确定某浏览器是否支持DOM3级XPath，可以使用下面的JavaScript代码
```js
var supportsXPath = document.implementation.hasFeature("XPath", "3.0");
```