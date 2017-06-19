### 16.1 CSS概览

### 16.2 重要的CSS属性
`z-index`只对兄弟元素(例如同一个容器的子元素)应用堆叠效果。如果两个元素不是兄弟元素之间的重叠，那么设置他们的z-index无法决定哪一个在上面

如果元素是动态定位，不是文档流中的一部分，那么外边距也就没用了

`clip`属性制定了元素的裁剪区域

### 16.3 脚本化内联样式
在JS中操作style，它的值不是字符串，而是一个`CSSStyleDecoration`对象。该Style对象的JS属性代表了HTML代码中通过style指定的CSS属性。

```js
el.style.fontSize = '14px';
el.style.fontWeight = 'bold';
```

`float`属性是JS的保留字，需要使用`el.style.cssFloat`来进行操作

以上元素的设置样式可以采用`el.style`来设置，但是如果需要查询元素的样式，则需要使用计算样式

可以使用`setTimeout()`和`setInterval()`来实现CSS的动画效果

```js
function shake(e, oncomplete, distance, time) {
    if(typeof e === 'style') e = document.getElementById(e);
    if(!time) time = 500;
    if(!distance) distance = 5;
    var originalStyle = e.style.cssText; // 保留e的原始的style
    var start = (new Date()).getTime(); // 记录动画开始时间
    animate();

    function animate() {
        var now = (new Date()).getTime(); // 记录当前时间
        var elapsed = now - start;
        var fraction = elapsed / time;
        if(fraction < 1) {
            var x = distance*Math.sin(fraction*4*Math.PI);
            e.style.left = x+'px';
            setTimeout(animate, Math.min(25, time - elapsed));
        } else {
            e.style.cssText = originalStyle;
            if(oncomplete) oncomplete && oncomplete();
        }
    }
}
```

### 16.4 查询计算出的样式
查询元素的计算之后的样式可以使用`getComputedStyle()`

```js
var style = window.getComputedStyle(dom); // 返回的是一个CSSStyleDeclaration对象
```