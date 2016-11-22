### Set
ES6提供了新的数据结构Set。它类似于数组，但是成员的值都是唯一的，没有重复的值
Set本身是一个构造函数，用来生成Set数据结构
```js
var s = new Set();
[2, 3, 5, 4, 5, 2, 2].map(x => s.add(x));
s; // [2,3,5,4]
```
上面的代码通过`add`方法向Set结构加入成员，结果表明Set结构不会添加重复的值
Set函数可以接受一个数组作为参数，用来初始化
```js
var set = new Set([1,2,2,3,4]);
// [1,2,3,4];
```
上述的代码也是一种去除数组中重复成员的方法
```js
// 去除数组的重复成员
[...new Set(Array)]
```
向Set加入值的时候不会发生类型转换，如果向Set中加入两个空对象那么他们总是不相等的

### Set的属性和方法
四个操作方法
- add(value) 添加某个值，返回Set结构本身
- delete(value) 删除某个值，返回一个布尔值，表示删除是否成功
- has(value) 返回一个布尔值，表示该值是否为Set成员
- clear() 清除所有成员

Array.from可以讲Set结构转为数组
```js
var items = new Set([1,2,3,4]);
var arr = Array.from(items);
```
遍历操作
- keys() 
- values()
- entries()
- forEach()

需要特别指出的是，Set的遍历顺序就是插入顺序，对于set结构而言，没有键名，只有键值（或者说键名和键值是同一个值）
```js
let set = new Set(['red', 'blue', 'green']);
for(let item of set.keys()){
    console.log(item);
}
```

### WeakSet
WeakSet结构与Set类似，也是不重复值的集合。但是，他与Set有两个区别
1、WeakSet成员只能是对象，不能使其他类型的值
2、WeakSet中对象都是弱引用，即垃圾回收机制不考虑`WeakSet`对该对象的引用，也就是说如果其他的对象都不再引用该对象，那么垃圾回收机制会自动回收该对象所占用的内存，不考虑该对象还存在于WeakSet之中
```js
var ws = new WeakSet();
ws.add(1); // TypeError: Invalid value used in weak set
```
作为构造函数，WeakSet可以接受一个数组或者类似数组的对象作为参数。（实际上，任何具有iterable接口的对象，都可以作为WeakSet的参数。）该数组的所有成员，都会自动成为WeakSet实例对象的成员。
```js
var a = [[1,2],[3,4]];
var ws = new WeakSet(a);
```

WeakSet结构有如下三个重要的方法
- add 向WeakSet实例添加一个新的成员
- delete 向WeakSet实例中删除一个成员
- has 返回一个布尔值，表示某个值是否在WeakSet实例之中

### Map
ES6提供了Map数据结构。它类似于对象，也是键值对的集合，但是键的范围不限于字符串，各种类型的值都可以当做键。也就是说，Object结构提供了`字符串-值`的对应，Map结构提供了`值-值`的对应，是一种更完善的Hash结构实现，如果你需要`键值对`的数据结构，Map比Object更合适
```js
var m = new Map();
var o = {p: 'Hello World'};
m.set(o, 'content')
m.get(o) // "content"
m.has(o) // true
m.delete(o) // true
m.has(o) // false
```
上面的代码使用Set方法，将对象o当做m的一个键，然后又使用get方法去读取这个键，接着使用`delete`方法删除了这个键
作为构造函数，Map也可以接受一个数组作为参数。该数组的成员是一个个表示键值对的数组
```js
var m = new Map([
    ['name', '张三'],
    ['title', 'Author']
]);
```
如果对同一个键多次赋值，后面的值将覆盖前面的值
如果读取一个未知的键，则返回`undefined`
Map的键实际上是跟内存地址绑定的，只要内存地址不一样就视为两个键。这就解决了同名属性碰撞的问题

### Map实例属性和操作方法
1. size 该属性返回Map结构的成员总数
2. set(key, value) 该方法设置key所对应的键值，饭后返回整个Map结构。如果key已经有了，则键值会更新，否则就生成该键
3. get(key) 获取
4. has(key) 该方法返回一个布尔值，表示某个键是否在Map数据结构中
5. delete(key) 该方法删除某个键，返回true，如果失败则返回false
6. clear() 清除所有成员，没有返回值

### Map遍历方法
Map原生提供了三个遍历器生成函数和一个遍历方法
- keys() 返回键名的遍历器
- values() 返回键值的遍历器
- entries() 返回所有成员的遍历器
- forEach() 遍历Map的所有成员

```js
let map = new Map([
    [1, 'one'],
    [2, 'two'],
    [3, 'three']
]);
[...map.keys()]; //[1,2,3]
```
结合数组的map方法，filter方法可以实现Map的遍历和过滤

### WeakMap
WeakMap结构与Map结构基本类似，唯一的区别是它只接收对象作为键名(null除外)，不接受其他类型的值作为键名，而且键名所指的对象不计入垃圾回收机制
