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























