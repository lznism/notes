## 图片地图
将超链接关联到图片上，例如导航栏的按钮。这个能减少HTTP请求，同时可以保证外观没有太大的变化。
图片地图有两种类型，
- 服务端图片地图 将所有点击提交到同一个URL，并传递x, y地图。通过x, y来进行适当的操作
- 客户端图片地图 通常通过MAP标签来实现

```html
<img src="img.png" usemap="#map1">
<map name="map1">
	<area shape="rect" coords="0,0,21,21" href="home.html" alt="home" title="home">
	<area shape="rect" coords="36,0,66,31" href="about.html" alt="about" title="about">
</map>
```

## CSS Sprites
将多个背景图片添加到同一个大的背景图片上
原理就是使用`background-position`来定位所需要的背景图片位置

```html
<style>
	.div {
		background-image: url(bg.png);
		background-position: -260px -90px;
	}
</style>
```

这里需要注意的是`background`的第一个参数表示横向的移动，负数为向左移动；第二个参数是竖向的移动，负数是向上移动。

## 内联图片的使用
通过`data: URL`模式可以在web页面中包含图片但无需任何额外的HTTP请求
缺陷在于IE7-均不支持`data: URL`模式，并且无法添加到缓存中。

## 合并脚本和样式表