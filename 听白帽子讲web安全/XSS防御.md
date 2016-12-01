### HttpOnly
浏览器禁止页面JavaScript访问带有HttpOnly属性的Cookie
一个Cookie的使用过程
1. 浏览器向服务器发起请求，这个时候没有Cookie
2. 在服务器返回值发送Set-Cookie头，向客户端浏览器写入Cookie
3. 在该Cookie到期前，浏览器访问该域下的所有页面，都将发送该Cookie
httponly是在`Set-Cookie`时标记的，以下是一个使用HttpOnly的过程
```php
<?php
    header("Set-Cookie: cookie1=test1");
    header("Set-Cookie: cookie2=test2;httponly", false);
?>
```
于是我们再次在客户端通过`document.cookie`来获取cookie,于是我们只能获取到`cookie1=test1`
添加HttpOnly的过程十分简单，但是部署时要注意如果业务非常复杂，则需要在所有的Set-Cookie的地方给关键的Cookie都加上HttpOnly

### 输入检查
常见的Web漏洞如XSS, SQL Injection等，都要求攻击者构造一些特殊字符，这些特殊字符可能是正常用户无法用到的，所以检查输入就有存在的必要了
输入检查的逻辑，必须放在服务器端代码实现，如果只是在客户端使用JS输入检查，是很容易被攻击者绕过的。目前Web开发中普遍做法就是同时在客户端JS中和服务端代码中同时进行输入检查。客户端的输入检查，可以阻挡大部分误操作的正常用户，从而节省服务器资源
在XSS防御上输入检查一般是检查用户输入的数据中是否包含一些特殊的字符`< > \`。如果发现存在特殊字符，则将其过滤或者编码

### 输出检查
一般来说，除了富文本输出外，在变量输出到HTML页面时，可以使用编码或转义的方式来防御XSS攻击

### 正确的防御XSS
为了更好的设计XSS防御方案，需要认清XSS产生的本质原因
XSS的本质还是一种`HTML注入`，用户的数据被当成了HTML代码一部分来执行，从而混淆了原本的语义，产生了新的语义

**在HTML中输出**
```html
<div>$var</div>
<a href="#">$var</a>
```
所有在标签中输出的变量，如果未做任何处理，都能导致直接产生XSS,比如$var的值为`<script>alert(/xss/)</script>`，防御的方法也是使用HtmlEncode

**在HTML属性中输出**
```html
<div id="abc" name="$var"></div>
```
如果$var的取值是`"><script>alert(/xss/)</script><"`，防御的方法也是HtmlEncode
在`<script>`标签中输出时，首先应该确保输出的变量在引号中
```html
<script>
var x = "$var";
</script>
```
攻击者需要先闭合引号才能实施XSS攻击
```html
<script>
    var x = "";alert(/xss/);
</script>
```
防御时使用JavascriptEncode

**在事件中输出**
```html
<a href="#" onclick="func('$var')"></a>
```
可能的攻击方法
```html
<a href="#" onclick="func('')alert(/xss/);//')"></a>
```
在防御时需要采用JavascriptEncode

**在CSS中输出**
在CSS和style, style attribute中形成的方式非常多样化
```html
<style>@import 'http://ha.ckers.org/xss.css'</style>
```
一般来说，尽可能禁止用户可控制的变量在`<style>标签`、`HTML标签的style属性`、`CSS文件`中输出
在地址中输出
在地址中输出也比较复杂。一般来说，在URL的path或者search中输出，使用URLEncode即可

**在地址中输出**
一般来说，在URL的path或者search中输出，使用URLEncode即可。URLEncode会将字符串转化为`%HH`形式，比如空格就是`%20`, `<`就是`%3c`
```html
<a href="http://www.evil.com/?test=$var"></a>
<!-- 可能的攻击方法 -->
<a href="http://www.evil.com/?test=" onclick="alert(1)""></a>
```
一般来说如果变量是整个URL，则应该先检查变量是否以HTTP开头(如果不是则自动添加)，以保证不会出现伪协议
```html
<a href="$var"></a>
```

**处理富文本**
有些时候，网站需要允许用户提交一些自定义的HTML代码，称之为`富文本`
如何区分安全的富文本和有攻击性的XSS？
过滤富文本时，事件应该被严格禁止，因为富文本的展示需求里不应该包括事件这种动态效果，而一些危险的标签，比如`<iframe> <script> <base> <form>`也是应该严格禁止的
在标签的选择上，应该使用白名单，避免使用黑名单。比如只允许`<a> <img> <div>`等比较安全的标签存在

**防御DOM BASED XSS**
DOM Based XSS是一种比较特别的XSS漏洞，前文提到的几种防御方法都不太适用，需要特别对待
```html
<script>
    function test(){
        var str = document.getElementById("text").value;
        document.getElementById('t').innerHTML = "<a href='"+str+"'>testLink</a>"
    }
    <div id="t"></div>
    <input type="text" id="text" value="" />
    <input type="button" id="s" value="write" onclick="test()" />
</script>
```


















