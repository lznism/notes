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

`$('selector').index()`
- 接受一个jQuery对象
- 字符串(选择器)
- 如果什么都不传入，返回的是索引值

`$('selector').is()`

### 19.2 jQuery的setter和getter
jQuery使用同一个方法既当getter也当setter使用

- attr() 获取/设置HTML属性的getter和setter
- css() 获取/设置CSS的样式，值得注意的是不能获取复合样式的值
- addClass()/removeClass()/toggleClass()
- val() 获取/设置表单值
- html()/text() 获取/设置元素的内容
- offset() 获取/设置元素的位置。该方法相对于文档来计算位置
- position() 返回元素的位置是相对其偏移元素的，而不是相对于文档的
- width()/height() 返回基本的宽高。不包含内外边距，边框
- outerWidth()/outerHeight() 通常返回的是包含元素内边距和边框尺寸
- innerWidth()/innerHeight() 返回的是包含元素内边距的尺寸
- scrollTop()/scrollLeft() 滚动的距离
- data() 获取/设置元素的数据

```js
$('div').data('x', 1); // 设置一些数据
$('div').removeData('x'); // 删除一些数据
$('div').data('x'); // 获取一些数据
```

### 19.3 修改文档结构
五个用于添加元素的方法

- append() 结尾添加
- prepend() 首部添加
- before() 前面添加
- after() 后面添加
- replaceWith() 替换

克隆元素

- clone()

包装元素

- wrap() 
- wrapInner() 
- wrapAll() 

删除元素

- empty() 
- remove() 直接从文档元素中移除

### 19.4 使用jQuery处理事件
- click()
- toggle()
- bind()
- one() 调用一次之后自动注销
- unbind() 
- trigger() 传入事件类型字符串作为第一个参数

```js
$('form').trigger('submit');
```

如果想调用事件处理程序，但是不执行默认操作可以使用`triggerHandler()`

可以使用bind()定义自定义事件，使用trigger()触发自定义事件

### 19.5 动画特效
- fadeIn()
- fadeOut()
- animate()
- slideDown()
- slideUp()
- slideToggle()

禁用动画`$.fx.off = true;`

动画还可以设置相对值

```js
$('p').animate({
    'margin-left': '+=.5px',
    'opacity': '-=.1'
});
```

在选项对象中，queue属性指定动画是否需要队列化--是否需要等到尚未发生的动画都在完成后再执行该动画。默认所有的动画都是队列化的。`queue: false`可以取消队列化

```js
$('img').fadeIn(500)
        .animate({width: '+=100px'}, {queue: false, duration: 1000})
        .fadeOut(500);
```

动画的取消、延迟和队列
- stop() 
- delay()

```js
$('img').fadeTo(100, 0.5).delay(200).slideUp();
```

### 19.6 jQuery中的ajax
load()

```js
$('div').load('demo.html');
```

$.getScript(url) 获取全局代码文件的URL

$.getJSON(url) 调用脚本后，将返回的结果解析为JSON

$.get(), $.post()

$.ajax()

### 19.7 工具函数
$.bowser

- msie
- mozilla
- webkit
- opera

$.contains()

$.each()

$.extend()

$.globalEval()

$.grep()

$.inArray()

$.isArray()

$.isEmptyObject()

$.isFunction()

$.isPlainObject()

$.makeArray()

$.map()

$.merge()

$.parseJSON()

$.proxy()

$.support 可移植性的特性探测

### 19.7 jQuery选择器和选取方法
- 简单选择器
- 过滤选择器
- 组合选择器
- 选择器组

```js
$('h1, h2, h3')
```

### 19.9 jQuery的插件扩展
`$.fn.method`
这种方式扩展的对象可以用如下方式使用
`$('div').method()`

给jQuery自身添加方法`$.method`

### 19.10 jQuery UI类库