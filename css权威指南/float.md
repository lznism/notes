浮动元素会脱离正常的文档流，不过浮动依旧会影响布局
浮动元素的特性
### 边距不会合并

```html
<style>
	.div1, .div2 {
		width: 100px; height: 100px;
		float: left;
		margin: 20px;
		background: #ccc;
	}
</style>
<div class="div1"></div>
<div class="div2"></div>
```

结果如图

![图片](http://i2.muimg.com/588926/8b9331181e5240ed.png)

### 不浮动
float的默认值是`none`,可以简单的理解为不浮动
换句话说，要得到正常的非浮动行为可以使用`float: none`

```css
.div {
	float: none;
}
```

### 浮动元素的边界不能超出其包含的边界

- 左浮动的终点是父级元素的左边界
- 右浮动的终点是父级元素的右边界

### 浮动元素的不会相互覆盖
元素左浮动，其左边界一定是父级元素的左边界或者前一个浮动元素的右边界
这个规则的好处就是，防止浮动元素互相层叠

### 左右浮动元素也不会层叠

```html
<style>
	.div1, .div2 {
		width: 200px; height: 100px;
		float: left;
		background: #ccc;
	}
	.div2 {
	 	float: right;
	}
</style>
<div class="div">
	<div class="div1">div1</div>
	<div class="div2">div2</div>
</div>
```

如下图所示

![float02](http://i4.buimg.com/588926/f8b460a2e0d9821c.png)

### 浮动元素的顶端不能超过父元素的顶端

### 浮动元素不能比之前浮动元素的顶端高

### 浮动元素不能草除其包含元素的边界
除非这个元素太宽，本身无法放下
如果没有足够的空间，浮动元素会被挤到新的一行上面

```html
<style>
	.div {
		width: 300px; height: 300px;
		background: pink;
	}
	.div1, .div2, .div3 {
		width: 100px; height: 100px;
		float: left;
		background: #ccc;
		border: 1px solid;
	}
	.div1 {
	 	height: 120px;
	}
	.div3 {
		width: 120px;
	}
</style>
<div class="div">
	<div class="div1">div1</div>
	<div class="div2">div2</div>
	<div class="div3">div3</div>
</div>
```

如图所示

![float03](http://i2.muimg.com/588926/7a19dd99092ed791.png)

### 负的外边距
负的外边距可以让浮动元素移动到父元素的外面

```html
<style>
	.div {
		width: 300px; height: 300px;
		background: pink;
		margin: 100px auto;
	}
	.div1 {
		width: 100px; height: 100px;
		float: left;
		margin-left: -10px;
		background: #ccc;
	}
</style>
<div class="div">
	<div class="div1">div1</div>
</div>
```

如图所示

![float04](http://i4.buimg.com/588926/f03fbfb3b6021c8b.png)

### 浮动元素可以伸到包裹元素的外部

```html
<style>
.container {
  width: 300px;
  height: 300px;
  background: #ccc;
}

.child {
  width: 100px;
  height: 100px;
  background: #eee;
  float: left;
  text-align: center;
  line-height: 100px;
}

.child1 {
  height: 400px;
}
</style>
<div class="container">
	<div class="child child1">child-1</div>
	<div class="child">child-2</div>
	<div class="child">child-3</div>
</div>
```

如下图所示

![float05](http://i1.piimg.com/588926/a94943ae28dad48a.png)

### 浮动元素和块级、行内元素重叠
当与行内元素重叠时，浮动元素会比行内元素层级高一级
当与块级元素重叠是，浮动元素会比块级元素层级低一级