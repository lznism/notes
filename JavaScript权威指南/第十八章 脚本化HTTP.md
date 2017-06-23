### 使用XMLHttpRequest

```js
if(window.XMLHttpRequest === undefined) {
    window.XMLHttpRequest = function() {
        try {
            return new ActiveXObject('Msxml2.XMLHTTP.6.0');
        }catch(e) {
            return new ActiveXObject('Msxml2.XMLHTTP.3.0');
        }catch(2) {
            throw new Error('XMLHttpRequest is not support');
        }
    }
}
```

指定一个请求

```js
xhr.open(method, url);
```

设置一个请求头信息

```js
xhr.setRequestHeader('Content-Type', 'text/plain');
```

`header`不能指定`Content-Length`, `Date`, `Referer`, `User-Agent`

使用send()发送请求

```js
xhr.send(null);
```

对于POST请求而言，请求的主体需要配合`Content-Type`头信息

服务段返回的信息
- status 响应的状态码,例如`200`
- statusText 响应的状态文本，例如,`OK`
- getResponseHeader() 获取请求头
- getAllResponseHeaders() 获取所有的请求头
- responseText 响应文本
- readyState HTTP请求的状态
    + 0 open()尚未调用
    + 1 open()已调用
    + 2 接收到头信息
    + 3 接收到响应主体
    + 4 响应完成

监听`readyState`事件

```js
xhr.onreadystatechange = function() {
    if(xhr.status === 200 && xhr.readyState === 4) {

    }
}
```

如果要采取同步请求，可以将open()的第三个参数设置为false，一般很少使用同步请求

```js
name=lznism&age=20
对应的MIME类型
application/x-www-form-urlencoded
```

使用JSON.stringify()作为请求主体

```js
xhr.setRequestHeader('Content-Type', 'application/json');
xhr.open(JSON.stringify(data));
```

如果是单个文件上传可以直接使用`xhr.send(file)`

如果包含不同类型的提交内容，可以将`Content-Type`设置为`multipart/form-data`，然后使用`send()`提交`FormData`对象

当调用`send()`时会单个触发`loadstart`事件，当事件完成，会触发load事件

请求超时，触发timeout事件

请求终止，触发abort事件

与进度相关的事件对象有三个属性`total`, `lengthComputable`, `loaded`

可以使用`xhr.upload.onprogress`来检测上传进度

`xhr.timeout`属性可以设置请求的超时事件`ontimeout`

使用`<script>`标签来灵活处理跨域问题

如果需要带上cookie和HTTP身份验证令牌，需要使用`xhr.withCredentials = true`

### 借助<script>发送HTTP请求:JSONP
使用步骤如下
1. 在前端定义好需要调用的函数

```js
function aaa(name) {
    console.log(name);
}
```

2. 使用`script`标签发送异步请求

```html
<script src="http://aaa.com/index.php?callback=aaa&age=20"></script>
```

3. 服务端根据get到的参数，进行相关操作，返回字符串

```js
// 字符串
aaa('lznism')
```

### 18.3 基于服务端推送事件的Comet技术
在服务端推送事件的标准草案中定义了一个`EventSource`对象，简化了Comet应用程序的编写

```js
var ticker = new EventSource('demo.php');
ticker.onmessage = function(e) {
    var type = e.type; // 处理时间类型
    var data = e.data; // 事件的字符串数据
}
```