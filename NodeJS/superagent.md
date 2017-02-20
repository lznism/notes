`superagent`是`nodejs`里面一个非常方便的客户端请求代理模块，支持`get, post, delete, head`等请求

### 请求基础
一个请求的初始化可以用请求对象里合适的方法来执行，然后调用`end()`来发送请求，注意只有调用了`end()`方法才会发出请求。在`end()`调用之前，所有的动作其实都是对请求的配置。默认的是`GET`方法

```js
request.get('/search').end(callback);
// 另外一种写法,直接将参数写入到函数中
request('GET', '/search').end(callback);
```

node客户端也允许使用绝对路径

```js
request.get('http://lznism.com').end(callback);
```

如果是`delete`方法，应该采用`del()`

### 设置头信息
设置请求头，包括请求的`数据格式，API-key`等

```js
request
    .get('/search')
    .set({
        'API-Key': 'bar', 
        Accept: 'application/json'
    });
```

### GET请求
我们可以使用`query()`方法来处理请求字符串

```js
request
    .get('/search')
    .query({
        query: 'Manny', 
        range: '1..5', 
        order: 'desc'
    })
    .end(res => {});
```

### POST请求
通过`set()`设置一个合适的`Content-Type`，然后通过`send()`写入一些`post data`，最后通过`end()`发出这个`post`请求。默认的`Content-Type`就是`json`

```js
request
    .post('/user')
    .send({
        name: 'lznism', 
        age: 20
    })
    .end(callback);
```

如果content-type设置为`application/x-www-form-urlencoded`，多次调用`send()`，将会使用&将所有的数据拼接起来，比如`name=lz&age=20`

```js
request
    .post('/user')
    .send('name=lz')
    .send('age=20')
    .end(callback);
```

###res.text
包含未解析的相应内容

### res.body
跟请求数据自动化序列一样，响应数据也会自动解析。当content-type设置一个解析器之后，就能自动解析，默认的是`application/json`

### res.type






















