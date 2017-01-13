在所有的web UI中，CSS和JS是同等重要的。比如JavaScript的正确运行不应当以来CSS，在缺少CSS的条件下也要能够正确运行，尽管两者间会有一些互动

#### 将JavaScript从CSS中抽离
CSS表达式允许你将JavaScript直接插入到CSS中，这样就可以在CSS代码中直接运算或其它操作

```css
.box {
    width: expression(document.body.offsetWidth + 'px');
}
```

上述的代码，直接将JS代码写入CSS中，浏览器会以高频率重复计算css表达式，这将严重影响性能，而IE9不再支持CSS表达式的写法

#### 将CSS从JavaScript中抽离
通过脚本修改样式最流行的一种方式是直接修改DOM元素的style属性。style属性是一个对象，包含了可以读取和修改的css属性

```js
// 以下这种是不好的写法
el.style.color = "red";
el.style.left = "10px";
el.style.top = "10px";
el.style.visibility = "visible";

// 另外一种不好的写法
el.style.cssText = "color:red; left:10px; top:10px; visibility:hidden";
```

以上两种写法均带来了严重的可维护性

合理的做法是将他们抽离到CSS样式类中，使用JS直接操作样式类

```css
.revel {
    color: red,
    left: 10px;
    top: 10px;
    visibility: visible;
}
```
一下是js代码

```js
el.className += " revel";
```

js不应该直接操作样式，以保证和CSS的松耦合

#### 将JS从HTML中抽离
禁用行内的JS

#### 将HTML从JavaScript中抽离出来
```js
// 不好的写法
var div = document.getElementById('div');
div.innerHTML = "<h2>Error</h2><p>Invalid e-mail address</p>";
```

有很多方式解决上述问题

* 从服务端加载
第一种方法是将模板放置于远程服务器，通过`XMLHttpRequest`对象来获取外部标签

```js
function loadText(name, oncomplete) {
    var xhr = new XMLHttpRequest();
    xhr.open('get', '/js/dialog' + name, true);
    xhr.onreadystatechange = function(){
        if(xhr.readyState == 4 && xhr.status == 200){
            var div = document.getElementById('div');
            div.innerHTML = xhr.responseText;
            oncomplete();
        }
    }
    xhr.send(null);
}
```

* 客户端模板

类似于C语言中的`sprintf()`操作

```js
function sprintf(text) {
    var i = 1; args = arguments;
    return text.replace(/%s/g, function(){
        return (i < args.length) ? args[i++] : "";
    })
}
```

* 复杂的客户端模板




















