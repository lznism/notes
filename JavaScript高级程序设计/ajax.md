### 创建一个XHR对象
支持度 IE7+, FF, Opera, Chrome, safari

```js
function createXHR() {
    if(typeof XMLHttpRequest != 'undefined') {
        return new XMLHttpRequest();
    } else if(typeof ActiveXObject != 'undefined' ) {
        if(typeof arguments.callee.activeXString != 'string') {
            var versions = ['MSXML2.XMLHttp.6.0', "MSXML2.XMLHttp.3.0", "MSXML2.XMLHttp"], i , len;
            for(i = 0; i <version.length; i<len; i++) {
                try{
                    new ActiveXObject(version[i]);
                    arguments.callee.activeXString = versions[i];
                    break;
                } catch (ex) {

                }
            }
        }
        return new ActiveXObject(arguments.callee.activeXString);
    } else {
        throw new Error('no xhr object aviliable');
    }
}
```

### XHR用法
xhr.open(METHOD, PATH, ASYNC);
METHOD: 请求的方法
PATH:   请求的URL
ASYNC:  是否异步发送请求； TRUE --- 异步； FALSE --- 同步

`xhr.open()` 并不会真正发送请求

```js
xhr.open('get', './test.txt', true);
```

如果要发送出去请求。必须使用`xhr.send()`
如果没有需要发送的数据，`xhr.send(null)`
如果有需要发送的数据，可以使用`xhr.send(data)`
当收到响应之后，响应的数据会自动填充XHR对象的属性

- reponseText 作为主体被返回的文本
- reponseXML  
- status      HTTP状态
- statusText  HTTP状态的说明

### XHR.raedyState

- 0 初始化，尚未调用open()方法
- 1 启动，已经调用open()方法，但是未调用send()
- 2 发送，已经调用send
- 3 接受，已经接到部分响应数据
- 4 完成，已经接收到全部响应数据

readyState每变化一次都会调用`onReadyStateChange`事件

```js
xhr.onreadyStateChange = function() {
    if(xhr.readyState === 4) { // 接收到全部的响应数据
        if(xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
            // 正确的请求
            alert(xhr.responseText);
        }
    }
}
```

在收到响应之前，取消异步请求`xhr.abort()`

### HTTP请求头
- Accept
- Accept-Charset
- Accept-Encoding
- Accept-Language
- Connection
- Cookie
- Host
- Referer
- User-Agent

设置请求头

```js
xhr.setHttpRequest('Accept', 'text/html');
```

### HTTP响应头

```js
xhr.getResponseHeader('MyHeader');  // 获取特定的头
xhr.getAllResonseHeaders();         // 获取所有的响应头
```

### GET请求
特征：查询字符串
使用GET请求的常见错误，未使用`encodeURIComponent`来对查询字符串进行编码

```js
function addURLParam(url, name, value) {
    url += (url.indexOf('?') === -1 ? '?' : '&');
    url += encodeURIComponent(name) + '=' + encodeURIComponent(value);
    return url;
}
```

### POST请求
POST请求应该把数据作为请求的主体提交，POST请求的主体可以包含多个数据，并且格式不限制

```js
// open的用法
xhr.open('post', url, true);
// 使用send方法来传递数据
// 首先需要设置头信息的Content-Type
xhr.setRequestHeader('Content-Type': 'application/x-www-form-urlencoded');
// 然后在send中写入需要传递的数据
xhr.send('a=111&b=222');
```

### FormData
FormData为序列化表单的内容提供了遍历
兼容性问题：目前只能兼容IE11

```js
// 基本用法
var data = new FormData();
data.append('name', 'lznism');
// 向其中添加表单数据
var data = new FormData(document.forms[0]);
xhr.send(data);
```

### 超时设定
两个步骤
1. 给xhr加上timeout属性
2. 添加ontimeout回调，同时终止请求

```js
xhr.timeout = 1000; // 将超时设置为1S
xhr.ontimeout = function() { // 超时的回调函数
    alert('Request did not return in a second');
}
```

### 进度事件
- loadstart 在接收到第一个字节时触发
- progress  在接受响应期间不断触发
- error     在请求错误时触发
- abort     终止触发
- load      在接收完成时触发
- loadend   error, abort, load事件之后触发

load可以代替`readyState === 4`的情况

```js
xhr.load = function() {
    if(xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
        alert(xhr.responseText);
    } else {
        alert('undone!');
    }
}
```

`onprogress()`会接受到一个event对象，它有三个额外的属性
- `lengthComputable` 进度信息是否可用
- `position`         已经接收到的字节数
- `totalSize`        根据`Content-Length`来确定预期的字节数

```js
xhr.onprogress = function(event) {
    if(event.lengthComputable) {
        console.log(event.position + 'of' + event.totalSize 'completed');
    }
}
```

**这个方法必须在open方法之前调用**

### 跨域
CORS(跨域资源共享)，基本的原理就是利用自定义的HTTP头部让浏览器和服务器通讯，从而决定请求是应该成功还是失败
IE8中引入了`XDR(XDomainRequest)`
XDR和XHR也有一些不同之处
- cookie不会随着请求发送，也不会随着响应返回
- 只能设置请求头信息中的`Content-Type`字段
- 不能设置响应头信息
- 只支持GET, POST请求
- XDR只支持异步请求

```js
var xdr = new XDomainRequest();
xdr.onload = function() {
    alert(xhr.responseText);
};
xdr.onerror = function() {
    alert('error');
}
xdr.open('get', 'http://lznism.com');
xdr.send(null);
```
### Preflight Request
这种机制允许开发人员使用自定义的请求头，请求方法
- Origin
- Access-Control-Request-Method
- Access-Control-Request-Headers

### 带凭据的请求
```js
Access-Control-Allow-Credentials: true
```

### CORS跨浏览器的兼容性问题
原理：只需要检验`withCredientials`是否存在于xhr对象中

```js
function createCORSRequest(method, url) {
    var xhr = new XMLHttpRequest();
    if('withCredientials' in xhr) {
        xhr.open(method, url ,true);
    } else if (typeof XDomainRequest != 'undefined') {
        xhr = new XDomainRequest();
        xhr.open(method, url);
    } else {
        xhr = null;
    }
    return xhr;
}
```

### img跨域
缺点： 只能GET, 不能访问服务器端的返回
```js
var img = new Image();
img.onload = img.onerror = function() {
    alert('Done');
}
img.src='http://lznism.com/test?name=lznism';
```

### JSONP
```js
function handleResponse(response) {
    console.log(response);
}
var script = document.createElement('script');
script.src="http://lznism.com?callback=handleResponse";
document.body.insertBefore(script, document.body,firstChild);
```

### Comet
`Comet`是一种服务器向页面推送数据的技术
两种实现Comet的技术
- 长轮询 浏览器发起一个请求，然后服务器一直保持请求打开，直到有数据可以发送， 数据发送完毕之后关闭连接
- 流     页面的整个生命周期中只是用一个HTTP链接。浏览器向服务器发送一个请求，而服务器保持打开，而后周期性的向浏览器发送数据
