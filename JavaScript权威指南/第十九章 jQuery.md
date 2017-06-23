### 19.1 jQuery基础
`$`, `jQuery`在全局命名空间中定义的唯一两个变量

`jQuery()`是工厂函数，不是构造函数，它返回的是一个新创建的对象

```js
// 以下代码会给每一个匹配的元素都注册一个时间处理函数
$('.classname').click(function() {
    // ...
});
```

`$()`可以接受四种不同的调用方式, 返回一个jQuery对象
- 接收一个CSS选择器
- 接收一个Element, Document, Window对象
- 接收HTML文本字符串, 这种情况可以接受第二个参数，一个对象(css, html, text, width, offset)
- 接受一个函数，此时文档加载完毕并且DOM可以操作时，该函数就会被调用

jQuery对象是类数组: 有length属性和介于0~length-1之间数值属性

```js
$('body').length; // 1
```

使用`each()`方法来做遍历，但是jQuery的each()方法和forEach有一个明显的区别，`$().each()`在任意一个元素上返回false，该循环就会被终止

```js
$('div').each(function(idx) {
    $(this).prepend(idx+':');
    if(this.id === 'last') return false;
});
```

`$('selector').map()`