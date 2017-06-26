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

### 22.2 历史记录管理
window对象上会触发一个hashchange事件。这样在支持hashchange事件的浏览器中，就可以通过设置window.onhashchange为一个处理程序的函数

`history.pushState()`方法和`popstate`事件。`pushState()`方法接受一个状态对象并为该对象创建一个私有副本。

### 22.3 跨域消息传递
允许非同源脚本调用`window.postMessage()`方法，通过异步消息传递方式

在postMessage()时，目标窗口会触发一个message事件。

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

### 22.5 类型化数组和ArrayBuffer


### 22.6 Blob

### 22.7 文件系统API

### 22.8 客户端数据库

### 22.9 web套接字