BOM的核心是window，它表示浏览器的一个实例。在浏览器中window有双重角色，既是通过js访问浏览器窗口的一个接口，又是ECMAScript规定的Global对象
全局变量不能通过`delete`删除，而直接在`window`对象上定义属性是可以的
```js
var color = 'red';
window.age = 10;
delete window.age; // IE<9抛出错误， 其余的return false
delete window.color; // IE<9抛出错误，其余的return true
```

#### 窗口关系及框架
如果页面中包含多个框架，则每个框架都有自己的window对象，并且保存在`frames`集合中
`top`对象始终指向最高层的框架，也就是浏览器窗口
与框架有关的最后一个对象是self，它始终指向window；实际上self和window对象可以互换使用
引入self的目的只是为了与top和parent对象对应起来

#### 窗口位置
确定和修改window对象位置的属性和方法有很多：`screenLeft`和`screenTop`属性，但是也有例外的，在FF中支持的是screenX和screenY属性
```js
var leftPos = (typeof window.screenLeft == 'number')?window.screenLeft:window.screenX;
var topPos = (typeof window.screenTop == 'number')?window.screenTop:window.screenY;
```
