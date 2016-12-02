### CSRF简介
CSRF的全名是`Cross Site Request Forgery`，跨站点请求伪造
攻击者仅仅诱使用户访问了一个页面，就以该用户身份在第三方站点里执行了一次操作。

### 浏览器的Cookie策略
攻击者的请求之所以能够被服务器通过验证，是因为用户的浏览器成功发送了Cookie的缘故
浏览器所持有的Cookie分为两种：一种是`session cookie`，又称`临时cookie`；另一种是`Third-party Cookie`，也称为`本地cookie`
* session cookie 页面关闭就失效
* Third-party Cookie 有生命周期

### P3P头的副作用
尽管有些CSRF攻击实施起来不需要认证，不需要发送Cookie，但是不可否认的是，大部分敏感或重要的操作是躲藏在认证之后的。因此浏览器拦截第三方Cookie发送，在某种程度上来说降低了CSRF攻击威力
如果网站返回给浏览器的HTTP头中包含P3P头，则某种程度上说，将允许浏览器发送第三方Cookie
在浏览网站的过程中，若是一个网站设置了Session Cookie，那么在浏览器进程的声明周期内，即使浏览器重新打开了Tab页，Session Cookie也是有效的。Session Cookie保存在浏览器进程的内存空间中；而Third-party Cookie则保存在本地

假设有www.a.com和www.b.com两个域，在b页面上有一个iframe指向www.a.com
```html
<iframe src="http://www.a.com/test.php"></iframe>
```

### Get/Post
使用HTML的`<img> <iframe> <script>`等等带有src属性的标签，这类标签只能够发起一次GET请求，而不是POST请求，而对于很多网站应用来说，一些重要的操作并未严格区分GET与POST，攻击者可以使用GET请求表单提交的气质





























