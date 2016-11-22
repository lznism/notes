### rest参数
ES6引入rest参数（形式为"...变量名"），用于获取函数的多余参数，这样就不需要使用`arguments`对象了。rest参数搭配的变量是一个数组，该变量将多余的参数放入数组中。
```js
function add(...values){
    let sum = 0;
    for(var val of values){
        sum += val;
    }
    return sum;
}
add(2,3,4); // 9
```
利用rest参数，可以向该函数传入任意数目的参数。`rest`参数中的变量代表一个数组，所以数组特有的方法都可以用于这个变量。
`rest`参数之后不能再有其他的参数，否则会报错
```js
function fn(a, ...b, c){}
```
函数的length属性，不包括rest参数
```js
(function(...a) {}).length; // 0
```

### 扩展运算符
好比是rest参数的逆运算，将一个数组转为用逗号分隔的参数序列
```js
console.log(...[1,2,3]);
```
该运算符主要用于函数调用
```js
function push(array, ...items){
    array.push(...items);
}
```

### 替代数组的apply方法
有了扩展运算符，无需使用apply方法就将数组转化为函数的参数了
```js
function f(x,y,z){
    // ...
}
var args = [1,2,3];
f(...args);
// 将一个数组添加到另一个数组的尾部
var arr1 = [1,2,3];
var arr2 = [4,5,6];
arr1.push(...arr2);
```

### 扩展运算符的应用
- 合并数组
- 与解构赋值结合
- 函数的返回值
- 字符串
- 实现了`Iterator`接口的对象

```js
[...'hello']; // ['h', 'e', 'l', 'l', 'o']
```
上面的写法，有一个重要的好处就是能够正确的识别32位的`Unicode`字符
```js
[...'x\uD83D\uDE80y'].length // 3
```

- Map, Set, Generator

扩展运算符内部调用的是数据结构的`Iterator`接口，因此只要具有遍历接口的对象，都可以使用扩展运算符，比如Map结构。
```js
let map = new Map({
    [1, 'one'],
    [2, 'two'],
    [3, 'three']
});
let arr = [...map.keys()];
```

### name属性
ES6中，函数表达式也能正确的返回函数名
```js
function foo(){}
foo.name // 'foo'
var foo = function(){}
foo.name // 'foo'

/*特殊情况*/
const bar = function baz(){}
bar.name // 'baz' 
```

### 箭头函数
```js
// 示例
var f = v => v;
var f = function(v){
    return v;
}
// 无参数
var f = () => 5;
// 多个参数
var f = (num1, num2) => num1 + num2;
// 如果有多条语句
var f = (num1, num2) => {
    var sum = num1 + num2;
    return sum;
}
```
由于大括号被解释为代码块，所以如果箭头函数直接返回一个对象，必须在对象外面加上括号
```js
var sum = )(num1, num2) => ({num1 : num1, num2 : num2});
```

箭头函数配合解构赋值
```js
const full = ({first, last}) => first + '' + last;
```
**使用箭头函数的注意点**
1. 函数体内的`this`对象，就是定义时所在的对象，而不是使用时所在的对象
2. 不可以当做构造函数，也就是说不能使用new命令
3. 不可使用`arguments`对象，该对象在函数体内不存在
4. 不可以使用`yield`命令

```js
function foo(){
    setTimeout(() => {
        console.log('id', this.id);
    });
}
var id = 21;
foo.call({id : 42}); // id: 42
```
this的指向固定化，并不是因为箭头函数内部有绑定this的机制，实际原因是箭头函数根本没有自己的this，导致内部的this就是外层代码块的this。正因为他没有this，所以也就不能用作构造函数

### 绑定this
箭头函数可以绑定this对象，大大减少了显示绑定this对象的写法。但是箭头函数并不适用于所有的场合，所以ES7提出了函数绑定运算符(function bind)，用来取代call, apply, bind调用。虽然该语法还是ES7的提案但是babel已经支持
```js
foo::bar;
// 等同于 foo.bind(foo);
foo::bar(...arguments);
// 等同于 bar.apply(foo, ...arguments);
```

### 尾调用优化
尾调用：指某个函数的最后一步是调用另外一个函数
```js
function f(x){
    return g(x);
}
```
上面的函数中，函数f的最后一步是调用函数g，这就叫尾调用
尾调用之所以与其他调用不同，就在于他的特殊调用位置。
尾调用是函数的最后一步操作了，所以不需要保留外层函数的调用帧，因为调用位置内部变量等信息都不会再调用了，只要直接用内层函数的调用帧，取代外层函数的调用帧即可

### 尾递归
函数调用自身，称为递归，如果尾调用自身，就称为尾递归
递归非常耗费内存，因为需要同时保存成千上百个调用帧，很容易发生栈溢出错误。对于尾递归来说，由于只存在一个调用帧，所以永远不会发生栈溢出的错误
```js
function fatorial(n, total){
    if(n === 1) return 1;
    return factorial(n - 1, n * total);
}
```
