### 基本概念
Generator函数是ES6提供的一种异步编程解决方案，语法行为与传统的函数完全不同。`Generator`函数是一个状态机，封装了多个内部状态。执行Generator函数会返回一个遍历器对象，也就是说Generator函数除了状态机还是一个遍历器对象生成函数。返回遍历器对象，可以一次遍历`Generator`函数内部的每一个状态
一个基本的Generator函数示例
```js
function* helloWorldGenerator(){
    yield 'hello';
    yield 'world';
    return ending;
}
var hw = helloWorldGenerator();
```
调用Generator函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向`内部状态`的指针对象，也就是上一章介绍的`遍历器对象`
```js
hw.next(); // {value: 'hello', done: false}
hw.next(); // {value: 'world', done: false}
hw.next(); // {value: 'ending', done: false}
hw.next(); // {value: 'undefined, done: true}
```
第一次调用，Generator函数开始执行，直到遇到第一个`yield`语句为止
第二次调用，Generator函数从上次`yield`语句停下的地方一直执行到下一个yield语句为止
....
总结一下，调用Generator函数，返回一个遍历器对象，代表`Generator`函数的内部指针。以后，每次调用遍历器的对象的`next`方法，就会返回一个有着`value`和`done`两个属性的对象，`value`属性表示当前内部状态的值，是yield语句后面那个表达式的值；`done`属性是一个布尔值，表示是否遍历结束
#### yield
由于Generator函数返回的遍历器对象，只有在调用`next`方法才会遍历下一个内部状态，所以其实提供了一种可以暂停执行的函数，`yield`语句就是暂停标志
yield语句后面的表达式，只有当调用`next`方法、内部指针指向该语句时才会执行，因此等于为js提供了“惰性求值”的语法功能
```js
function* gen(){
    yield 123 + 456;
}
```
yield语句如果用在一个表达式中，必须放在圆括号内
```js
console.log('hello' + (yield 123));
```

#### 与Iterator接口的关系
上一章说过，任意一个对象的`Symbol.iterator`方法，等于该对象的遍历器生成函数，调用该函数会返回一个遍历器对象
由于`Generator`函数就是遍历器生成函数，因此可以吧`Generator`赋值给对象的`Symbol.iterator`属性，从而使得该对象具有`Iterator`接口
```js
var myIterable = {};
myIterable[Symbol.iterator] = function* (){
    yield 1;
    yield 2;
    yield 3;
};
[...myIterable]; // [1,2,3]
```
上面的代码中，Generator函数赋值给`Symbol.iterator`属性，从而使得`myIterable`对象具有了Iterator接口，可以被`...`运算符遍历了

### next方法的参数
`yield`句本身没有返回值，或者说总是返回`undefined`。`next`方法可以带一个参数，该参数就会当做上一个`yield`语句的返回值
通过`next`方法的参数，就有办法在Generator函数开始运行之后，继续向函数体内部注入值。也就是说，可以在Generator函数运行的不同阶段，从外部向内部注入不同的值，从而调整函数的行为
```js
function* foo(x){
    var y = 2 * (yield (x + 1));
    var z = yield (y / 3);
    return (x + y + z);
}
var a = foo(5);
a.next();   // Object{value:6, done:false}
a.next();   // Object{value:NaN, done:false}
a.next();   // Object{value:NaN, done:true}

var b = foo(5);
a.next();   //  { value:6, done:false }
a.next(12); //  { value:8, done:false }
b.next(13)  // { value:42, done:true }
```
注意，由于`next`方法的参数表示上一个`yield`语句的返回值，所以第一次使用`next`方法时，不能带有参数

### for...of循环
`foo...of`循环可以自动遍历Generator函数时生成的`Iterator`对象，且此时不再需要调用`next`方法
```js
function* foo(){
    yield 1;
    yield 2;
    yield 3;
    yield 4;
    return 5;
}
for(let v of foo()){
    console.log(v);
}
// 1,2,3,4
```
上面代码使用`for...of`循环，依次显示4个yield语句的值。这里需要注意，一旦`next`方法返回的对象的`done`是`true`， `for ... of`循环就会终止，且不包含该返回对象，所以`return`的值不会被返回。

### Generator.prototype.throw()
Generator函数返回的遍历器对象，都有一个`throw`方法，可以在函数体外抛出错误，然后在Generator函数体内捕获
```js
var g = function* (){
    try{
        yield;
    }catch(e){
        console.log("内部捕获", e);
    }
}
var i = g();
i.next();

try{
    i.throw('a');
    i.throw('b');
}catch(e){
    console.log("外部捕获", e);
}
```
上面的代码中，遍历器对象i连续抛出两个错误。第一个错误被Generator函数体内的catch语句捕获。i第二次抛出错误，由于Generator函数内部的catch语句已经执行过了，不会再捕捉到这个错误了，所以这个错误就被抛出了Generator函数体，被函数体外的catch语句捕获。
如果Generator函数内部没有部署`try...catch`代码块，那么throw方法抛出的错误，将被外部`try...catch`代码块捕获
如果Generator函数的内部和外部，都没有部署`try...catch`代码快，那么程序将报错，直接中断执行

### Generator.prototype.return()
Generator函数返回遍历器对象，还有一个`return`方法，可以返回给定的值，并且`终结`遍历Generator函数
```js
function* gen(){
    yield 1;
    yield 2;
    yield 3;
}
var g = gen();
g.next();   // {value: 1, done: false}
g.return('foo');    // {value: 'foo', done: true}
g.next();   // {value: undefined, done: true}
```
如果`return`方法调用时，不提供参数，则返回的value值是`undefined`

### yield* 语句
如果在`Generator`函数内部，调用另一个`Generator`函数，默认情况下是没有效果的。这里就需要用到`yield*`语句，用来在一个`Generator`函数里面执行另一个Generator函数
```js
function* foo() {
    yield 'a';
    yield 'b';
}
function* bar(){
    yield 'x';
    yield* foo();
    yield 'y';
}
// 等同于
function* bar() {
  yield 'x';
  yield 'a';
  yield 'b';
  yield 'y';
}
for (let v of bar()){
  console.log(v);
}
// "x"
// "a"
// "b"
// "y"
```

### 作为对象属性的Generator函数
如果一个对象的属性是`Generator`函数，可以简写成下面的形式
```js
let obj = {
    * myGeneratorMethod(){
        ...
    }
}
```

### Generator函数的this
`Generator`函数总是返回一个遍历器，ES6规定这个遍历器是`Generator`函数的实例，也继承了`Generator`函数的`prototype`对象的方法上