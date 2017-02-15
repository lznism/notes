### express.static
`express.static`是express内置的中间件，负责托管`express`应用内部的静态资源
`express.static(root, [options])`
具体用法:

```js
app.use(express.static('public'));
```

于是我们就可以通过`http://localhost:3000/1.png`来访问`public`文件夹下面的静态资源

或者我们可以指定虚拟目录

```js
express.static('/static', express.static('public')) ;
```

于是我们就可以通过`http://localhost:3000/static/1.png`来访问`public`文件夹下面的静态资源

### app.locals
是一个javascript对象，他的属性就是程序本地的变量

```js
app.locals.title = 'MyApp';
app.locals.email = 'lznism@lznism.com';
```

可以在渲染模板时使用这些本地变量。它们非常有用，可以为模板提供一些有用的方法，以及`app`级别的数据

### app.mountpath
这个是子程序挂载的路径模式，一个子程序是一个`express`的实例，其可以用来作为路由句柄处理请求

```js
var express = require('express');
var app = express();
var admin = express();
admin.get('/', function(req, res) {
    console.log(admin.mountpath); // '/admin'
    res.send('Admin Homepage');
});
app.use('/admin', admin);
```

在访问`/admin`这个路由时，会输出`/admin`,这是因为`app.mountpath`就是一个关于挂载路径模式项的列表

### app.on('mount', callback(parent))
当子程序被挂载到父程序上时，`mount`事件被发射。父程序对象作为回调参数，传递给回调方法

```js
var admin = express();
admin.on('mount', function(parent) {
    console.log('Admin Mounted');
    console.log(parent);
});
admin.get('/', function(req, res) {
    res.send('Admin Page');
});
app.use('/admin', admin);
```

### app.all(path, callback)
该方法匹配所有的HTTP动词

```js
app.all('*', requireAuthentication, loadUser);
```

将上述代码写到所有的路由之前，会自动认证和自动加载一个用户，这些回调并不一定是重点，可以在完成任务之后，调用`next()`方法来继续匹配随后的路由

### app.disable(name);
设置类型为布尔的`name`为`false`，调用`app.set('foo', false)`和`app.disable('foo')`是等价的

```js
app.disable('trust proxy');
app.get('trust proxy'); // false
```

### app.enable(name)
设置类型为布尔的`name`为`true`，调用`app.set('foo', true)`和`app.enable('foo')`是等价的

```js
app.disable('trust proxy');
app.get('trust proxy'); // true
```

### app.engine(ext, callback)
注册模板引擎`callback`用来处理`ext`扩展名的文件，默认情况下
如果想要渲染一个`foo.jade`文件，`Express`会在内部执行下面代码，然后缓存`require()`

```js
app.engine('jade', require('jade').__express)
```

### app.get(name)
用来获取`app.set(name, value)`的`value`值

### app.param([name], callback)
给路由参数添加回调触发器，这里的`name`是参数名或者参数数组，`function`是回调方法
如果`name`是数组，会按照各个参数在数组中被声明的顺序将回调触发器注册下来

对于`Param`的回调定义的路由来说，他们是局部的。它们不会被挂载的`app`或者路由继承。所以定义在`app`上的`param`回调只有是在`app`上的路由参数才起作用

`param`回调在请求--相应循环中都会被调用一次并且只有一次，即使有多个路由匹配

```js
app.param('id', function(req, res, next) {
    console.log('called only once');
    next();
});

app.get('/user/:id', function(req, res, next) {
    console.log('although this matches');
    next();
});

app,get('/user/:id/:page', function(req, res, next) {
    console.log('and this matches too');
    res.end();
});
```

### app.path()
通过这个方法可以得到`app`典型的路径，返回一个`string`

```js
app.use('/blog', blog);
app.use('/admin', admin);

console.log(app.path());    // 
console.log(blog.path());   // blog
console.log(admin.path());  // admin
```

### app.render(view, [locals], callback)
通过`callback`回调返回一个`view`渲染之后得到的`HTML`文本。它可以接受一个可选参数，可选参数包含了这个view需要用到的本地数据。

```js
app.render('email', function(err, html) {

});
```

### app.route(path)
返回一个单例模式的路由实例，之后可以在上面施加各种HTTP动作的中间件

```js
app.route('/admin')
    .all(function(req, res, next) {
        console.log('all method');
        next();
    })
    .get(function(req, res, next) {
        console.log('get method');
    })
    .post(function(req, res, next) {
        console.log('post method');
    });
```

### app.use([path], function, [function...])
挂载中间件方法到路径上。如果未指定，那么就是`/`
**注意:**`/admin`将匹配`/admin/home`, `/admin/nav`等

```js
app.use('/admin', function(req, res, next) {
    // http://www.example.com/admin/new
    console.log(req.originalUrl); // '/admin/new'
    console.log(req.baseUrl);     // '/admin'
    console.log(req.path);        // '/new'
})
```

### 中间件的不同使用形式
单个中间件

```js
var router = express.Router();
router.get('/', function(req, res, next) {
    // ...
    next();
});
app.use(router);
```

多个中间件

```js
var r1 = express.Router();
r1.get('/', function(req, res, next) {});

var r2 = express.Router();
r2.get('/', function(req, res, next) {});

app.use(r1, r2);
```

### request
代表了一个HTTP请求，其具有一些属性来保存请求中的一些数据，比如`query string`, `parameters`, `body`, `HTTP headers`

```js
app.get('/user:id', function(req, res) {
    res.send('user' + req.params.id);
});
```

### req.app
这个属性持有`express`程序实例的一个引用，其可以作为中间件使用

```js
var express = require('express');
var app = express();
app.set('name', 'NodeExpress');
app.get('/', function(req, res, next){
    res.send('the name of this app is' + req.app.get('name'));
});
app.listen(3000);
```

### req.baseUrl
一个路由挂载的Url路径

```js
var greet = express.Router();
greet.get('/jp', function(req, res){
    console.log(req.baseUrl); // '/greet'
    res.send('hello!');
});
app.use('/greet', greet)
```

### req.body
在请求的body中保存的是提交的一对对键值数据。默认情况下是`undefined`，当你使用比如`body-parser`和`multer`这类解析`body`数据的中间件事，它是填充的

```js
var express = require('express');
var bodyParser = require('body-parser');
var multer = require('multer');
var upload = multer();
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: true}));

app.post('/profile', upload.array(), function(req, res, next){
    console.log(req.body);
    res.json(req.body); 
});
```

### req.cookies
当使用`cookie-parser`中间件的时候，这个属性是一个对象

```js
req.cookies.name  // => 'lznism'
```

### req.fresh
指示这个请求是否新鲜

### req.hostname
包含了源自`host` HTTP头部的`hostname`

```js
req.hostname; // lznism.com
```

### req.ips
当`trust proxy`设置项被设置为启用值，这个属性包含了一组`X-Forwarded-For`请求头中指定的IP地址。不然就会是一个空数组。这个头部可以被客户端或者代理设置

```js
app.enable('trust proxy');
req.ips;
```

### req.originalUrl
原版的请求链接

```js
// get /admin
req.originalUrl; // '/admin'
```

### req.path
包含请求URL的部分路径

```js
req.path; 
```

### req.protocol
请求协议 `http`或者`https`

### req.query
一个对象，为每一个路由中的`query string`参数都分配一个属性。如果没有`query string`，那就是空对象

```js
// GET /search?q=abc
req.query.q; // 'abc'
```

### req.route
当前匹配的路由，其为一串字符串

```js
app.get('/user/:id', function(req, res) {
    console.log(req.route);
    res.send('GET');
});
```

### req.stale
请求是否陈旧

### req.subdomains
请求中的子域名数组

```js
// HOST: 'blog.lznism.com'
req.subdomains; // ['blog']
```

### req.xhr
指示一个请求是否由客户端发送

```js
req.xhr;
```

### req.accepts(types)
检查这个指定的内容类型是否被接受，基于请求的`Accept HTTP`头部。这个方法返回最佳匹配，如果没有一个匹配则返回`undefined`

```js
req.accepts('html'); // text/html
```

### req.acceptsCharsets(charset)
返回指定字符集

### req.acceptsEncodings(encoding)
返回指定编码

### req.acceptsLanguages(lang)
返回指定语言集合中第一个配置的语言，基于请求的`Acceps-Languege`

### req.get(field)
返回指定的HTTP头部内容(不区分大小写)

```js
req.get('Content-Type');
```

### req.is(type)
如果进来的请求的`Content-Type`头部匹配参数`type`给定的`MIME type`，那么返回`true`。否则返回`false`

```js
req.is('html');
```

### res.app
这个和`req.app`的作用是一样的，都是返回这个`express`程序实例的引用

### res.headersSent
布尔值，指示这个响应是否已经发送HTTP头部

```js
app.get('/', function(req, res) {
    console.log(res.headersSent);
});
```

### res.locals

### res.append
在指定的`field`的HTTP头部追加特殊的`value`

```js
res.append('Lind', ['http://localhost', 'http://localhost:8080']);
res.append('Set-Cookie', 'foo=bar;path=/;HttpOnly');
```

### res.cookie(name, value, [options])

```js
res.cookir('name', 'lznism', {'expires': new Date(Date.now() + 9000), 'httpOnly': true})
```

### res.clearCookie(name, [options])

```js
res.cookie('name', 'lznism');
res.clearCookie('name');
```

### res.download(path, [filename], [fn])
下载文件

```js
res.download('/pdf', 'report.pdf', function(err) {});
```

### res.end()
结束请求

### res.format(object)
进行内容协商，根据请求的对象中`Accept` HTTP头部指定的接受内容

```js
res.format({
    'text/plain': function(){
        res.send('hey');
    },
    'text/html': function() {
        res.send('<p>hey</p>');
    }
    //...
})
```

### res.get(filed)
返回`field`指定的HTTP响应的头部

```js
res.get('Content-Type'); //=> 'text/plain'
```

### res.json()
发送一个`json`响应。这个方法和将一个对象或者一个数组作为参数传递给`res.send`方法想过相同

```js
res.json({name: 'glz'});
```

### res.jsonp([body])
发送一个json响应，并且支持`JSONP`，这个方法和res.json()效果相同

### res.links(links)
连接这些`links`，`links`是以参数的属性形式提供，连接之后内容是用来填充响应`Link HTTP`头部

```js
res.link({
    next: 'http://api.example.com/users?page=2'
})
```

### res.location(path)
设置响应的`location` HTTP头部为指定的`path`参数

```js
res.location('/foo/bar');
```

### res.redirect()
重定向来源于指定`path`的URL

### res.render()
渲染一个视图，然后将渲染得到的HTML文档发送给客户端
`res.render(file, option)`是express中专门渲染视图用的，首先需要在`app.js`或者`index.js`中设置一些渲染引擎，比如`html, jade, ...`, 然后将视图模板未见放入`file`，将需要传递的数据导入`option`对象中，模板引擎就能渲染出自己的视图

```js
app.set('view engine', 'jade');
app.set('views', path.join(__dirname, 'views'));

app.get('/', function(req, res, next){
    res.render('home'); // 渲染views文件夹下面的home.jade文件
})
```

### res.send()
发送HTTP响应，`body`参数是可以Buffer对象，一个字符串，一个对象，或者一个数组

```js
res.send(new Buffer('whoop'));
res.send({some: 'json'});
```

### res.sendFile(path)
传输`path`指定的文件。根据文件的扩展名`Content-Type` HTTP头部。除非在`options`中有关于`root`的设置

### res.sendStatus(statusCode)
设置响应对象的`HTTP statsu code`为`statuscode`并且发送`statuscode`的相应的字符串形式作为响应的`body`

```js
res.sendStatus(200);
```

### res.set()
设置响应对象的HTTP头部`field`为`value`

### res.status()
设置响应对象的HTTP status 

```js
res.status(400).send('bad request');
```

### res.type(type)
设置`Content-Type` HTTP头部为MIME type

```js
res.type('html');
```

### res.vary(field)
设置`vary`响应头为`field`

```js
res.vary('User-Agent').render('docs');
```

### Router

```js
var router = express.Router();
```

### router.all()
支持所有的HTTP方法

### router.param(name, callback)
给路由参数添加回调触发器

### router.route(path)
返回一个单例模式的路有实例，之后就可以在其上施加HTTP动作的中间件

```js
var router = exrpess.Router();
router.route('/user/:user_id')
    .all()
    .get()
    .post();
```

### router.use()
给可选的`path`参数指定路径挂载给定的中间件方法

```js
router.use('/bar', function(){
    // ...
    next();
})
```





























