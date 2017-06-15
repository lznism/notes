### 13.1 客户端JS

### 13.2 HTML中的JS

### 13.3 JavaScript程序的执行
`defer`和`async`属性都像在告诉浏览器链接进来的脚本不会使用`document.write()`,因此浏览器可以在下载脚本时继续解析和渲染文档。

`defer`属性使得浏览器延迟脚本的执行，知道文档的载入和解析完成

`async`属性使得浏览器可以尽快执行脚本，而不用在下载脚本时阻塞文档解析

浏览器在事件发生时执行调用。用事件进行异步编程会经常涉及嵌套函数，也经常要在函数里面定义函数

JavaScript语言并不包含任何线程机制，单线程执行是为了让编程更加简单。编写JS程序时永远不需要担心锁，死锁和竟态条件

客户端JS时间线
- web浏览器创建`Document`对象，并且开始解析web页面
- 下载和执行脚本
- 处理async类脚本
- 文档完成解析，`document.readyState === 'interactive'`
- 处理defer脚本
- 浏览器Document对象上触发`DOMContentLoaded`事件
- 图片载入完成`document.readyState === 'complete'`
- 事件处理

### 13.4 兼容性和互用性
处理方案
- 添加兼容库，最常见的如jQuery
- 分级浏览器支持，对不同的版本功能不同，放弃部分低版本浏览器的部分功能支持和测试
- 功能测试，在脚本中添加相应的代码来检测是否在浏览器中支持该功能
- 标准模式和怪异模式，渲染模式的选择依赖于HTML文件顶部的DOCTYPE声明。怪异模式和标准模式之间的差异对于HTML和CSS开发者影响最大

```js
// 渲染模式的特性检测
document.compatMode; // "CSS1Compat" 表示按照标准模式渲染
```

- 浏览器测试
- IE条件注释

```html
<!--[if IE 6]>this will be displayed in IE 6<![endif]-->
<!--[if lte IE 7]>this will be displayed in ie 7 or earlier<![endif]-->
```

### 13.5 可访问性

### 13.6 安全性
同源策略负责管理窗口或者窗体中JS代码以及其他窗口的交互，具体的来说，脚本只能读取所有文档来源相同窗口和文档的属性

### 客户端框架
