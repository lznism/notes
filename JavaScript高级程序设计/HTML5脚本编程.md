### 跨文档消息传递
`postMessage()`接收两个参数，一条消息和一个表示消息接收方来自哪个域的字符串
```js
var iframeWindow = document.getElementById('myframe').contentWindow;
iframeWindow.postMessage('A secret', 'http://www.wrox.com');
```
第二行代码表示向内嵌框架中发送一条消息，并指定框架中的文档来源于`http://www.wrox.com`,接收到消息后，可以使用`onmessage`处理程序中检测消息来源可以确保传入的消息来自已知的页面

### 原生拖放
拖放元素将会依次触发`dragstart`, `drag`, `dragend`三个事件
默认情况下浏览器不会在拖动期间改变被拖动元素的外观，但可以自己修改，不过大多数浏览器会为正被拖动的元素创建一个半透明的副本，这个副本始终跟随着光标移动
当某个元素被拖动到一个有效的放置目标上，下列事件依次发生`dragenter`, `dragover`, `dragdrop`
自定义放置目标需要重写`dragenter`和`dragover`事件的默认行为
`dataTransfer`对象，它是事件对象的一个属性，用于被拖动元素向放置目标传递字符串格式的数据
`dataTransfer`对象有两个方法`getData()`， `setData()`。
```js
// 设置和接受文本数据
event.dataTransfer.setData('text', 'some text');
var text = event.dataTransfer.getData('text');

// 设置和接受URL
event.dataTransfer.setData('URL', 'http://lznism.com');
var url = event.dataTransfer.getData('URL');
```