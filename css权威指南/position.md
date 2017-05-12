### 四种定位方式
* static    定位的默认值，正常的文档流
* relative  元素仍保持定位前的形状，原本的空间仍然保留
* absolute  脱离文档流，相对于其包含块定位
* fixed     类似于absolute, 但是其相对于浏览器窗口定位

### 包含块的定位
* 可能是html元素
* 如果position为relative或static, 包含块则是最近的父级元素
* 如果position为absolute, 包含块则是最近的position不是static的祖先元素

这里有一点需要注意的就是，定位的top/left/right/bottom的值如果是百分数，这个百分数都是相对于包含块的宽高来计算的。

定位的数值，正数会导致定位元素内移，负数会导致定位元素外移

偏移的默认值是auto。对于auto没有定义行为

### 定位元素的宽高

```css
top:0; left:0; bottom:0; right:50%;
```

效果如下图所示

![position01](http://i1.piimg.com/588926/3804f32d5b7a5cda.png)

### 限制高度和宽度

- min-width/min-height
- max-width/max-height

以上属性在IE7前都不支持

```css
top: 10%; bottom: 10%; left: 50%; right: 10%; 
min-width: 10em; min-height: 20em;
```

### 内容溢出

- visible 默认值，元素在边界之外也可以看见
- hidden
- scroll
- auto
- inherit