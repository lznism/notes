### 属性的简介表示法
ES6允许直接写入变量和函数，作为对象的属性和方法
```js
var foo = 'foo';
var baz = {foo};
baz; // {foo: 'foo'};
// 等同于
var baz = {foo: foo};
```
上面的代码表明，ES6允许在对象之中，只写属性名，不写属性值。这时，属性值等于属性名所代表的变量
```js
function f(x, y){
    return {x, y};
}
// 等同于
function f(x, y){
    return {x: x, y: y};
}
f(1, 2);
// 方法也可以简写
var o = {
    method(){
        return "hello";
    }
}
```
这种写法使得函数的返回值写法变得非常方便
```js
function getPoint(){
    let x = 1;
    let y = 10;
    return {x, y};
}
getPoint(); // {x:1, y:10}
```
commonJS模块输出变量，就非常适合使用简洁的写法
```js
var ms = {};
function fn1(){}
function fn2(){}
function fn3(){}
module.exports = {fn1, fn2, fn3};
```
简洁的写法属性名总是字符串，这回导致一些看上去比较奇怪的效果
```js
var obj = {
    class(){}
};
// 等同于
var obj = {
    'class': function(){}
}
```

### 属性名的表达式
ES5中定义对象属性，有两种方法
```js
// 直接使用标识符作为属性名
obj.foo = true;
// 用表达式作为属性名
obj['f' + 'oo'] = true;
```
ES6允许使用字面量定义对象是，用方法二作为对象的属性名，即把表达式放在方括号内部
```js
let propKey = 'foo';
let obj = {
    [propKey]: true,
    ['a' + 'bc']: 123 
}
```
表达式还可以用于方法名
```js
let obj = {
    ['he' + 'llo'](){
        return 'hi';
    }
}
obj.hello();
```

### 方法的name属性
函数的name属性，返回属性名
```js
var person = {
    sayName(){
        console.log(this.name);
    },
    get firstName(){
        return "Nicholas";
    }
};
person.sayName.name; // "sayName"
person.firstName.name; // "get firstName"
```
有两种特殊情况：bind方法创造的函数，`name`属性返回"bound"加上原函数的名字：`Function`构造函数创造的函数，`name`属性返回`anonymous`。
```js
(new Function()).name; // "anonymous"
var dosomthing = function(){
    // ...
}
dosomething.bind().name; // "bound dosomething"
```

### Object.is()
ES6提出同值相等算法，用来解决这个问题。`Object.is()`就是部署这个算法的新方法。它用来比较两个值是否严格相等
```js
Object.is('foo', 'foo'); // true
Object.is({}, {}); // false
Object.is(+0, -0) // false
Object.is(NaN, NaN) // true
```

### Object.assign()
该方法用于对象合并，将源对象所有的可枚举属性，复制到目标对象(`target`)
```js
var target = {a: 1};
var source1 = {b: 2};
var source2 = {c: 3};
Object.assign(target, source1, source2);
target; // {a: 1, b: 2, c: 3}
```
第一个参数是目标对象，后面的都是源对象
如果目标对象和源对象有共同的属性，那么后面的属性会覆盖前面的属性
如果只有一个参数，`Object.assign`会直接返回该参数
```js
var obj = {a: 1};
Object.assign(obj) === obj; // true
```
其他类型的值（如数字，字符串，布尔值）不在首参数，也不会报错。但是除了字符串以数组形式拷贝入目标对象，其他值都不会产生效果
```js
var v1 = 'abc';
var v2 = true;
var v3 = 10;
var obj = Object.assign({}, v1, v2, v3);
console.log(obj); // { "0": "a", "1": "b", "2": "c" }
```
`Object.assign`拷贝是有限制的，只拷贝源对象的自身属性（不拷贝继承属性），也不拷贝不可枚举属性`enumerable: false`
`Object.assign`方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。
```js
var obj1 = {a: {b: 1}};
var obj2 = Object.assign({}, obj1);
obj1.a.b = 2;
obj2.a.b // 2
```

### 属性的可枚举性
对象的每个属性都有一个描述对象`Descriptor`，用来控制该属性的行为。`Object.getOwnPropertyDescriptor`方法可以获取该属性的描述对象
```js
let obj = { foo: 123 };
Object.getOwnPropertyDescriptor(obj, 'foo')
//  {
//    value: 123,
//    writable: true,
//    enumerable: true,
//    configurable: true
//  }
```

### 属性的可枚举性
ES6一共有5种方法可以遍历对象的属性
- for...in
- Object.keys(obj)
- Object.getOwnPropertyNames(obj)
- Object.getOwnPropertySymbols(obj)
- Reflect.ownkeys(obj) 返回一个数组，包含对象自身的所有属性，不管是属性名,Symbol, 字符串

### __proto__
用来读取或者设置当前对象的`prototype`对象
```js
var obj = {
    method: function(){}
}
obj.__proto__ = someObj;
```

### Object.setPrototypeOf()和Object.getPrototypeOf()
`Object.setPrototypeOf`作用与`__proto__`相同，用来设置一个对象的`prototype`对象。它是ES6正式推荐的设置原型对象的方法
```js
Object.setPrototypeOf(object, prototype);
```
`Object.getPrototypeOf()`用于读取一个对象的原型对象

### Object.values()和Object.entries()
Object.values()返回一个数组，成员是参数对象自身的所有可遍历属性的键值
只返回可遍历的属性
```js
var Obj = Object.create({}, {p:{values: 42}});
Object.values(obj);
```
`Object.values()`还可以接受一个字符串，会返回各个字符串组成的一个数组
`Object.entries()`方法返回一个数组，成员是参数对象自身的（不含继承的）所有可遍历（enumerable）属性的键值对数组。

### 对象的扩展运算符
1. 解构赋值
```js
let {x, y, ...z} = { x: 1, y: 2, a: 3, b: 4 };
z // {a:3,b:4}
```
解构赋值也是浅拷贝，即如果一个键的值是复合类型的值（数组，对象，函数），那么解构赋值拷贝的是这个值的引用，而不是这个副本的值
解构赋值不会拷贝继承自原型对象的属性
```js
let o1 = { a: 1 };
let o2 = { b: 2 };
o2.__proto__ = o1;
let o3 = { ...o2 };
o3 // { b: 2 }
```
2. 扩展运算符
用于取出参数对象的所有可遍历属性，拷贝到当前对象之中
```js
let z = {a: 1, b:2};
let n = { ...z };
n // { a: 3, b: 4 }
```

