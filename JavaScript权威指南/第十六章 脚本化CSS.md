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

IE中采用的是`currentStyle`属性

```js
document.getElementById('test').currentStyle;
```

### 16.5 脚本化CSS类
HTML属性的class在JS中应该使用`className`

HTML5为了解决`className`的问题，推出了`classList`属性，该属性是`DOMTokenList`对象

```js
function classList(e) {
    if(e.classList) return e.classList;
    else return new CSSClassList(e);
}
function CSSClassList(e) {
    this.e = e;
}
CSSClassList.prototype.contains = function(c) {
    if(c.length === 0 || c.indexOf('') !== -1) {
        throw new Error('Invalid class name ' + c);
    }
    var classes = this.e.className;
    if(!classes) return false;
    return classes.search('\\b'+c+'\\b') != -1;
}
CSSClassList.prototype.add = function(c) {
    if(this.contains(c)) return;
    var classes = this.e.className;
    if(classes && classes[classes.length-1] !== '') {
        c = ' ' + c;
        this.e.className += c;
    }
}
```

### 16.6 脚本化样式表
`<style>`, `<link>`元素和`CSSStyleSheet`对象都定义了一个在JS中可以设置和查询的`disabled`属性，如果设置为`true`样式表就会被浏览器关闭并且忽略

```js
// 接受一个数字或者一个数组
function disableStylesheet(ss) {
    if(typeof ss === 'number') {
        document.styleSheets[ss].disabled = true;
    } else {
        var sheets = document.querySelectorAll(ss);
        for(var i=0; i<sheets.length; i++) {
            sheets[i].disabled = true;
        }
    }
}
```

现代的浏览器可以使用标准的DOM技术创建一个`<style>`，然后使用`innerHTML`来创建样式表

IE8以前创建新的样式表要使用`document.createStyleSheet()`