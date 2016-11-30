ES6的模块自动采用严格模式，不管有没有在头部加上`use strict`
### export命令
模块的功能主要有两个命令构成`export`和`import`。`export`命令用于规定模块的对外接口，`import`用于输入其他模块提供的功能
```js
export var fisrtName = 'aaa';
export var lastName = 'bbb';
// 另外一种写法
var fisrtName = 'aaa';
var lastName = 'bbb';
export {firstName, lastName};
// 输出函数
export function person(){}
// 输出类
export class Person  {}
```
需要特别注意的是，`export`命令规定的是对外的接口，必须与模块内部的变量建立一一对应的关系
```js
export 1; // error
var m = 1;
export m; // error
```
**export语句输出的接口，与其对应的值是动态绑定的关系，即通过接口，可以取得模块内部实时的值**
```js
export var a = 1;
setTimeout(() => a = 2, 500);
// 输出的值开始为1，后来变为2
```
export必须出现在顶层位置，处在块级作用与中就会报错

### import
使用`import`命令来加载模块文件
```js
import {firstName, lastName, year} from './profile';
```

### 模块整体加载
除了指定加载某个输出值，还可以使用整体加载，即用`*`指定一个对象，所有的输出值都加载在这个对象上
```js
// a.js
export function a(){}
export function b(){}
// b.js
import * as A from './a';
A.a();
A.b();
```

### export default
为模块指定默认输出
```js
export default function(){
    console.log('aaa');
}
```
其它模块加载该模块时，import命令可以为该匿名函数指定任意名字，一个模块只能有一个默认输出，因此`export default`命令只能又一次

### 模块继承
假设有一个`circleplus`模块，继承了`circle`模块
```js
// circleplus.js
export * from 'circle';
export var e = 2.71828182846;
export default function(x){
    return Math.exp(x);
}
```
上面的代码中的`export *`，表示再输出circle模块的所有属性和方法。注意，`export *`会忽略circle模块中的default方法，然后代码又输出了自定义的e变量和默认方法

### ES模块加载的实质
CommonJS模块输出的是一个值的拷贝，而ES6模块输出的是值的引用
由于ES6输入的模块变量只是一个符号链接，所以这个变量是只读的，对它进行重新赋值会报错
```js
// lib.js
export let obj = {}
// main.js
import {obj} from './lib';
obj.prop = 123;
obj = {}; // error
```

### 循环加载
指的是a脚本的执行依赖b脚本，而b脚本的执行又依赖a脚本
通常情况，`循环加载表示存在强耦合`，如果处理的不好，还可能导致递归加载，是的程序无法执行，因此应该避免出现
CommonJS模块重要特性就是加载时执行，即脚本代码在require的时候，就会全部执行。一旦出现某个模块被`循环加载`，就只输出已经执行的部分，还未执行的部分不会输出
```js
// a.js
exports.done = false;
var b = require('./b.js');
console.log('在 a.js 之中，b.done = %j', b.done);
exports.done = true;
// b.js
exports.done = false;
var a = require('a.js');
console.log('在 a.js 之中，a.done = %j', a.done);
console.log('b.js执行完毕');
```

ES6处理循环加载与CommonJS有本质的不同，ES6模块是动态引用，如果使用`import`从一个模块加载变量，那些变量不会被缓存，而是成为一个执行被加载模块的引用
```js
// a.js
import {bar} from './b.js';
console.log('a.js');
console.log(bar);
export let foo = 'foo';
// b.js
import {foo} from './a.js';
console.log('b.js');
console.log(foo);
export let bar = 'bar';
// node a.js
b.js
undefiend
a.js
bar
```
上面代码中，由于a.js的第一行是加载b.js，所以先执行b.js。而b.js的第一行又是加载a.js，这时由于a.js已经开始执行了，所以不会重复执行，而是继续往下执行b.js,所以第一行执行b.js

### 跨模块常量
`const`声明的常量只是在当前代码块有效。如果想设置跨模块的常量，可以采用下列写法
```js
export const A = 1;
export const B = 1;
export const C = 1;
```