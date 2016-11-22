### Symbol概念
ES6引入了一种新的原始数据类型`Symbol`，表示独一无二的值。它是JS的第七种数据类型
`Symbol`值通过`Symbol()`函数生成。这就是说对象的属性名可以有两种类型，一种就是原来的字符串，另外一种就是新增的`Symbol`类型。凡是属性名为`Symbol`类型就是独一无二的，可以保证不会与其他属性名产生冲突
```js
let s = Symbol();
typeof s; // "symbol"
```
`Symbol`函数可以接受一个字符串作为参数，表示对`Symbol`实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易区分。
```js
let s1 = Symbol("foo");
let s2 = Symbol("bar");
```
在`Symbol`加上参数之后，就变得容易区分，Symbol()的数据类型，不存在两者相等的情况
```js
var s1 = Symbol("foo");
var s2 = Symbol("foo");
s1 === s2 // false
```
Symbol类型的值不能与其他类型的值进行运算，会报错
```js
var sym = Symbol('My symbol');
"your symbol is " + sym
// TypeError: can't convert symbol to string
```
但是Symbol可以显式转化为字符串
```js
sys.toString();
```

### 作为属性名的Symbol
用Symbol来做属性名，这意味着Symbol值可以作为标识符，用于对象的属性名，就能保证不会出现同名属性
```js
var mySymbol = Symbol();
var a = {};
a[mySymbol] = 'hello!';
```
但是需要注意的是，Symbol用作对象属性名时，不能用点运算符；
同理在对象的内部，使用Symbol值定义属性时，Symbol值必须放在方括号内
```js
let s = Symbol();
let obj = {
    [s]: function(arg){...}
};
obj[s](123);
```
Symbol类型还可以用于定义一组常量，保证这组常量的值都是不相等的
```js
let consts = {
    DEBUG: Symbol("debug"),
    INFO: Symbol("info"),
    WARN: Symbol('warn')
}
```
常量使用Symbol值最大的好处就是其他任何值都不可能有相同的值了

### 属性名的遍历
Symbol作为属性名，该属性不会出现在`for...in`, `for...of`循环中，也不会被`Object.keys()`, `Object.getOwnPropertyNames()`返回。但是，它也不是私有属性，有一个`Object.getOwnPropertySymbols`方法可以获取指定对象的Symbol属性名
另一个新的API，`Reflect.ownkeys`方法可以返回所有类型的键名，包括常规键名和`Symbol`键名
```js
let obj = {
    [Symbol('key')]: 1,
    enum: 2
}
Reflect.ownKeys(obj)
// [Symbol(key), 'enum'];
```
由于Symbol值作为名称的属性，不会被常规的方法遍历得到。我们可以利用这个特性，为对象定义一些非是有的，但是又希望用于内部的方法
```js
var size = Symbol('size');
class Collection{
    constructor(){
        this[size] = 0;
    }
}
```

### Symbol.for(), Symbol.keyFor()
有时候我们希望使用同一个Symbol值，`Symbol.for`方法可以做到这一点，它接收一个字符串作为参数，然后搜索有没有以该参数作为名称的`Symbol`值。如果有，就返回这个`Symbol`值，否则就新建并返回一个以该字符串为名称的`Symbol`值
```js
var s1 = Symbol.for('foo');
var s2 = Symbol.for('foo'); 
s1 === s2; // true
```
Symbol.keyFor方法返回一个已登记的Symbol类型值的key
```js
var s1 = Symbol.for('foo');
Symbol.keyFor(s1);  // 'foo'
```

### 内置的Symbol值
除了定义自己使用的Symbol值以外，ES6还提供了11个内置的Symbol值，指向语言内部使用的方法

**Symbol.hasInstance**
对象的Symbol.hasInstance属性指向一个内部方法。当其他对象使用`instanceof`运算符，判断是否为该对象实例时，会调用这个方法。
```js
class MyClass {
    [Symbol.hasInstance](foo){
        return foo instanceof Array;
    }
}
[1,2,3] instanceof new MyClass; // true
```

**Symbol.isConcatSpreadable**
对象的Symbol.isConcatSpreadable属性等于一个布尔值，表示该对象使用`Array.prototype.concat()`时，是否可以展开
```js
let arr1 = [1,2];
[3,4].concat(arr1, 5); // [1,2,3,4,5]
arr1[Symbol.isConcatSpreadable]; // undefined

let arr2 = ['c', 'd'];
arr2[Symbol.isConcatSpreadable] = false;
['a', 'b'].concat(arr2, 'e') // ['a', 'b', ['c','d'], 'e']
```

**Symbol.species**
对象的`Symbol.species`属性指向一个方法。该对象作为构造函数创造实例时，会调用这个方法。即如果`this.constructor[Symbol.species]`存在，就会使用这个属性作为构造函数，来创造新的实例对象

**Symbol.match**
对象的Symbol.math属性指向一个函数。当执行`str.match(myObj)`时，如果该属性存在就会调用它，返回该方法的返回值

**Symbol.replace**
对象的Symbol.replace属性，指向一个方法，当该对象被`String.prototype.replace`方法调用时，会返回该方法的返回值

**Symbol.search**
对象的Symbol.search属性，指向一个方法，当该对象被`String.prototype.search`方法调用时，会返回该方法的返回值

**Symbol.split**
对象的Symbol.split属性，指向一个方法，当该对象被`String.prototype.split`方法调用时。会返回该方法的返回值

**Symbol.iterator**
对象的Symbol.iterator属性，指向该对象默认遍历器方法

**Symbol.toPrimitive**
对象的`Symbol.toPrimitive`属性，指向一个方法，该对象被转为原始类型的值时，会调用这个方法返回该对象对应的原始类型值

**Symbol.toStringTag**
对象的Symbol.toStringTag属性，指向一个方法。在该对象上面调用Object.prototype.toString方法时，如果这个属性存在，它的返回值会出现在toString方法返回的字符串之中，表示对象的类型