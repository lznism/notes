### 22.1 地理位置
- navigator.geolocation.getCurrentPosition()
- navigator.geolocation.watchPosition()
- navigator.geolocation.clearWatch()

```js
navigator.geolocation.getCurrentPosition(function(pos) {
    var lat = pos.coords.latitude;
    var lng = pos.coords.longitude;
    console.log(lat, lng);
})
```

`watchPosition()`一旦位置发生变化就会调用回调函数

`clearWatch()`停止监视用户位置

除了第一个回调函数之外，还能接受第二个回调函数，用于在定位失败时调用

### 22.2 历史记录管理
hash属性设置URL的片段标识符，通常用于指定要滚动到的文档中某一部分的ID。但是location.hash不一定要设置一个元素的ID。可以是任何字符串。

window对象上会触发一个hashchange事件。这样在支持hashchange事件的浏览器中，就可以通过设置window.onhashchange为一个处理程序的函数

当web应用进入一个新的状态时，它会调用`history.pushState()`将该状态添加到浏览器的历史记录中

`history.pushState()`方法和`popstate`事件。`pushState()`方法接受一个状态对象并为该对象创建一个私有副本。

`replaceState()`将新的状态添加到浏览器的历史中，使用新的状态代替当前的历史状态

```js
var stateObj = {foo: 'bar'};
// history.pushState(state, title, url)
history.pushState(stateObj, 'page 2', 'bar.html');
```

想要良好的支持浏览器的历史前进和后退的操作，应当部署`popstate`监听

```js
window.addEventListener('popstate', function(evt) {
    var state = evt.state; // 获取本页面的状态
}, false);
```

### 22.3 跨域消息传递
允许非同源脚本调用`window.postMessage()`方法，通过异步消息传递方式。
postMessage接受两个参数，第一个是要传递的数据，第二个是目标窗口的源

```js
postMessage('name','http://lznism.com');
```

在postMessage()时，目标窗口会触发一个message事件。

```js
// 接收消息
window.addEventListener('message', function(e) {
    if(e.source != window.parent) return;
    var color = container.style.backgroundColor;
    window.parent.postMessage(color, '*');
}, false)
```

### 22.4 web worker
`worker`是指定代码的并行县城。与主进程之间的通信只能通过异步消息传递机制来实现

```js
var loader = new Worker('utils/loader.js');
```

可以通过监听worker对象上的message事件来接受worker消息

```js
worker.onmessage = function(e) {
    var message = e.data;
    console.log(message);
}
```

web主线程
- 通过new Worker(url)加载一个JS文件来创建一个worker,同时返回一个worker实力
- 通过worker.postMessage(data)方法像worker发送数据
- 绑定worker.onmessage来接受worker发送来的数据

worker线程
- 绑定 onmessage 方法来接收主线程发送过来的数据。
- 通过 postMessage(data) 方法来向主线程发送数据。
- 可以使用 self.close() 来终止一个 worker 的执行。

### 22.5 类型化数组和ArrayBuffer
`ArrayBuffer`类型化数组，类型化数组是js操作二进制数据的一个接口

```js
var bf = new ArrayBuffer(40); // 生成字节长度为40的内存区域
console.log(bf.byteLength); // 获取字节长度
```

### 22.6 Blob

### 22.7 文件系统API

### 22.8 客户端数据库

### 22.9 web套接字