### 压缩
如果浏览器和服务器都支持的话，可以使用压缩来减小相应的大小，浏览器可以使用`Accept-Encoding`头来声明支持压缩。服务器可以使用`Content-Encoding`头来确定相应已经被压缩

```
浏览器端请求头
Accepte-Encoding: gzip, deflate
服务器端响应头
Content-Encoding: gzip
```

压缩通常能将响应的数据量减少近70%

### 条件GET请求
如果浏览器在缓存中保留了一个组件的副本，但并不确定它是否仍然有效，就会生成一个条件get请求。如果确定缓存的副本仍然有效，浏览器就可以使用缓存的副本，这会得到更小的相应和更快的用户体验
缓存副本的有效性源自其最后修改时间。基于响应中的`Last-Modified`头，知道其最后修改的时间，然后会使用`If-Modified-Since`头奖最后修改时间发送给服务器

```
浏览器段请求头
If-Modified-Since: XXXXXX
服务器端响应头
Last-Modified: XXXX
```

如果没有更改，服务器会返回`304 Not Modified`

### Expires
通过明确指出浏览器是否可以使用组件的缓存副本
如今的web页面都包含了大量的组件，并且数量还在不断地增长。页面的初访者会进行很多HTTP请求，但通过使用一个长久的`Expires`头，使这些组件缓存。这会在后面的页面中避免不必要的HTTP请求。长久的`Expires`头最常用于图片，但应该将其用在所有的组件上包括脚本样式和Flash

HTTP1.1使用max-age指令制定组件被缓存多久。

```
缓存1小时
Cache-Control: max-age=3600
```

```
服务器端响应头
Expires: Web, 05 Oct 2016 19:16:20 GMT
```
当浏览器看到响应中有一个Expires头时，它会和相应的过期时间组件一起保存到期缓存中，只要组件没有过期，就不会进行任何HTTP请求

### Keep-Alive
在HTTP早期实现中，每个HTTP请求都要打开一个socket连接。这样做效率很低，因为一个web页面中的很多HTTP请求都是指向同一个服务器，持久连接的引入解决了多对一请求服务器导致的socket连接低效性的问题。它使浏览器可以在一个单独的连接上进行多个请求
浏览器和服务器使用`Connection`头来指出`Keep-Alive`的支持

```
浏览器端
Connection: Keep-Alive
服务器端
Connection: Keep-Alive
```