### 概述
```js
class Point{
    // 构造方法
    constructor(x, y){
        this.x = x;
        this.y = y;
    }
    toString(){
        return '(' + this.x + ',' + this.y + ')';
    }
}
// 类的使用
var point = new Point(10, 20);
point.toString();  // (10,20)
```
ES6中所有类的方法都是定义在类的原型上(`prototype`),调用类上的方法其实就是调用类的原型方法
可以使用`Object.assign()`方便的方便的向类上添加方法

```js
Object.assign(Point.prototype, {
    toString() {}
    toValue() {}
})
```

```js
point.constructor === point.prototype.constructor;
```

### constructor
`constructor`方法是默认方法，在通过`new`命令生成对象实例时，自动调用这个方法。如果没有显式ß的定义这个方法，则会默认被添加；该方法默认返回的是对象的`this`
所有的实例都共享一个原型

```js
var p1 = new Point(1,2);
var p3 = new Point(1,3);
p1.__proto__ === p2.__proto__;
```

`class`不存在变量提升，必须先声明，后使用

```js
var f = new Foo();
class Foo{}
```

### 模拟私有方法
1. 命名上加以区别

```js
class Test{
    foo(baz){
        this._bar(baz);
    }
    // 私有方法前面加一个下划线
    _bar(baz){
        return this.snaf = baz;
    }
}
```

2. 将私有方法移除模块
```js
class Test{
    foo(baz){
        bar.call(this, baz);
    }
}
function bar(baz){
    return this.snaf = baz;
}
```
3. 将私有方法的名字命名为一个`Symbol`值
```js
class Test{
    foo(baz){
        this._bar(baz);
    }
    // 私有方法前面加一个下划线
    [Symbol('baz')](baz){
        return this.snaf = baz;
    }
}
```

### this的指向
类的方法内部如果含有`this`，它默认指向类的实例。
```js
class Logger{
    printName(name = 'there'){
        this.print(`Hello ${name}`);
    }
    print(text){
        console.log(text);
    }
}
const logger = new Logger();
const { printName } = logger;
// this默认指向的是类的实例
// 单独取出来时，指向的是运行时的环境
printName(); // TypeError: Cannot read property 'print' of undefined
```
绑定构造方法中的this指向，这样就不会找不到该方法了
```js
class Logger{
    constructor(){
        this.printName = this.printName.bind(this);
    }
}
// 或者使用箭头函数
class Logger{
    constructor(){
        this.printName = (name = 'there') => {
            this.print(`Hello ${name}`);
        }
    }
}
```

### 继承
ES6的继承通过`extends`关键字实现
```js
class colorPoint extends Point{
    constructor(x, y, color){
        super(x, y); // 调用父类的constructor(x, y)
        this.color = color;
    }
    toString(){
        return this.color + ' ' + super.toString(); // 调用父类的toString()方法
    }
}
```
子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。这是因为子类没有自己的this对象，而是继承的父类的`this`对象，然后对其进行加工。如果不调用`super`方法，子类就得不到`this`对象；并且在子类中只有调用super方法之后，才可以使用`this`关键字，否则会报错
```js
class colorPoint extends Point{
    constructor(x, y, color){
        this.color = color;  // 报错
        super(x, y); // 调用父类的constructor(x, y)
    }
}
```

### prototype和__proto__
大多数浏览器的ES5实现之中，每一个对象都有`__proto__`属性，指向对应的构造函数的`prototype`属性。 class同时具有`__proto__`， `prototype`
1. 子类的__proto__属性，表示构造函数的继承，总是指向父类
2. 子类的prototype属性的__proto__属性，表示方法的继承，总是指向父类的`prototype`属性

```js
class A{}
class B extends A {}
B.__proto__ === A;
B.prototype.__proto__ === A.prototype;
```

`Object.getPrototypeOf()`该方法可以用来从子类上获取父类，使用这个方法可以检验一个类是否继承了另外一个类

```js
Object.getPrototypeOf(ColorPoint) === Point;
```

### super关键字
这个关键字即可以当做函数使用，也可以当做对象使用
1. 当做函数调用时，表示父类的构造函数
2. 作为对象时，指向父类的原型对象

```js
class A{
    p(){
        return 2;
    }
}
class B extends A {
    constructor(){
        super();
        console.log(super.p()); // 2
    }
}
let b = new B();
```

`super`指向的是父类的原型对象，所以定义在父类实例上的方法或属性，是无法通过super调用的
特别规定：通过`super`调用父类方法时，`super`会绑定子类的`this`

```js
class A {
    constructor() {
        this.x = 1;
    }
    print() {
        console.log(this.x);
    }
}
class B extends A {
    constructor() {
        super();
        this.x = 2;
    }
    m() {
        super.print();
    }
}
let b = new B();
b.m(); // 2
```

### 原声构造函数的继承
ES6允许继承原生构造定义子类，因为ES6是先新建父类实例对象`this`,然后在用子类构造函数修饰`this`,使父类所有的行为都可以继承

```js
class MyArray extends Array {
    constructor(...args) {
        super(...args);
    }
}
```

### Class的静态方法
类相当于实例的原型，所有的类中定义的方法，都会被继承
加上`static`关键字就表示该方法不会被`实例`继承，而是通过类来调用，这个就是`静态方法`

```js
class Foo {
    static classMethod() {
        return 'hello';
    }
}
Foo.classMethod(); // 注意该方法不可以在实例上调用
```

目前ES7已经支持累的实例属性

```js
class Foo {
    myProp = 100;
    // 读取类的实例属性
    constructor() {
        console.log(this.myProp); // 100
    }
}
```

ES7也支持静态属性

```js
class MyClass {
    static myStaticProp = 100;
    constructor() {
        console.log(MyClass.myStaticProp);
    }
}
```

### new.target属性
ES6中`new.target`属性返回`new`命令作用于的那个构造函数。如果构造函数不是通过`new`命令调用的，`new.target`就会返回`undefined`,因此这个属性可以用来确定构造函数是怎么调用的

```js
class Point {
    constructor(x, y) {
        console.log(new.target === Point); // true
        this.x = x;
        this.y = y;
    }
}
```





























