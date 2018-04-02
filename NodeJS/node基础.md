事件驱动设计

```js
// node.js
var http = require('http');

http.createServer(function (req, res) {
    console.log('request received');
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.write('hello world');
    res.end();
}).listen(8888);

console.log('server has started');
```

将一个函数作为另一个方法的参数传递进去，这个函数就被称为回调函数。这个方法在相应事件发生时调用。称之为`回调`

上面这段函数在我们使用`node server`时，会在控制台输出`server has started`。当我们在浏览器中访问`localhost:8888`时，控制台会打印`request received`

这个就是事件驱动的异步服务器端JavaScript以及它的回调了