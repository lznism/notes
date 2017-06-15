### 14.1 计时器
如果将`setTimeout()`的延迟时间设置为0，它也不会立刻执行。相反会把它放到队列中，等到前面处于等待状态的事件处理程序全部处理完成后再调用

### 14.2 浏览器定位和导航

```js
document.location === window.location; // true
```

document对象也有一个URL属性，是文档首次载入后保存该文档URL的静态字符串。`document.URL`属性不会改变。而`document.location`会更新。

URL的组成
- protocol
- host
- port
- hostname
- pathname
- search
- hash

```js
function urlArgs() {
    var args = {};
    var query = location.search.substring(1);
    var pairs = query.split('&');
    for(var i=0; i<pairs.length; i++) {
        var pos = pairs[i].indexOf('=');
        if(pos === -1) continue;
        var name = pairs[i].substring(0, pos);
        var value = pairs[i].substring(pos+1);
        args[name] = value;
    }
}
```

- location.assign() 可以使窗口载入并显示你指定的URL中的文档
- location.replace() 载入新文档之前会从浏览器历史中把当前文档删除。replace()可以传入一个相对的URL，相对于当前页面所在的目录来解析
- location.reload()

```js
location = '#top';
location.search = `?page=${pagenum+1}`;
```

### 14.3 浏览历史
```js
history.go(number);
history.forward();
history.back();
```

### 14.4 浏览器信息和屏幕信息

`navigator.onLine`属性表示浏览器是否连接到网络

`screen.width` 屏幕的宽度
`screen.availWidth` 屏幕的有用的宽度

### 14.5 对话框
方法`confirm()`和`prompt()`都会产生阻塞

### 14.6 错误处理
window对象的onerror属性是一个事件处理程序，当未捕获的异常传播到调用栈上时，就会调用它

`window.onerror`的三个参数
- 描述错误的消息
- 引发错误的JS代码所在的文档
- 错误的行数

### 14.7 作为window对象属性的文档元素

### 14.8 多窗口和窗体
```js
var w = window.open();
w.alert('new window');
```

任何窗口或窗体引用为`window`或`self`

如果一个窗口是顶级窗口或者标签而不是窗体，那么parent属性引用的就是这个窗口本身

```js
parent === self; // 只有顶级窗口才会返回true
```

如果一个窗体包含在另一个窗体中，而后者又包含在顶级窗体中。`top`是访问顶级窗口的一种快捷方式

顶级窗口的window对象的`frameElement`属性为`null`;
窗体中的window对象的`frameElement`属性不是`null`.

`window.frames`是对子窗体的引用