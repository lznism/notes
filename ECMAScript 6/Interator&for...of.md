遍历器(Interator)是一种机制。它是一种接口，为各种不同的数据结构提供统一的访问机制。任何数据结构只要部署了`Interator`接口，就可以完成遍历操作
当使用`for...of`循环遍历某种数据结构时，该循环会自动去寻找`Interator`接口
ES6规定，默认的`Interator`接口部署在数据结构的`Symbol.iterator`属性，或者说，一个数据结构只要具有`Symbol.iterator`属性，就认为是可遍历的(iterable);
在ES6中，有三类数据结构原生具备`Iterator`接口：数组、某些类似数组的对象、Set和Map结构
```js
let arr = ['a', 'b', 'c'];
let iter = arr[Symbol.iterator]();
iter.next(); // {value: 'a', done: false}
iter.next(); // {value: 'b', done: false}
iter.next(); // {value: 'c', done: false}
iter.next(); // {value: undefined, done: true}
```
上面的代码中，变量arr是一个数组，原生就具有遍历器接口，部署在arr的`Symbol.iterator`属性上面。所以调用这个属性，就得到遍历器对象。
除了上面三种原生就具有遍历接口的数据接口，其他的数据结构的Iterator
一个对象如果要有可被`for...of`循环调用的Iterator接口，就必须在`Symbol.iterator`的属性上部署遍历器的生成方法
```js
class RangeIterator {
    constructor(start, stop){
        this.start = start;
        this.stop = stop;
    }

    [Symbol.iterator](){
        return this;
    }

    next(){
        var value = this.value;
        if(value < this.stop){
            this.value ++;
            return {done: false, value: value}
        }else{
            return {done: true, value: undefined}
        }
    }
}

function range(start, stop){
    return new RangeIterator(start, stop);
}

for(var val of range(0,3)){
    console.log(val);
}
```
上面的代码是一个类部署Iterator接口的方法。`Symbol.iterator`属性对应一个函数，执行后返回当前对象的遍历器对象

### 调用Iterator接口的场合
有一些场合会默认调用Interator接口(即`Symbol.iterator`方法)，除了下文会介绍的`for...of`循环，还有以下几个场合

1. 解构赋值，对数组和Set解构进行解构赋值时，会默认调用`Symbol.iterator`方法
```js
let set = new Set().add('a').add('b').add('c');
let [x, y] = set; // x='a'; y='b'
```
2. 扩展运算符
```js
var str = 'hello';
[...str]; // h e l l o
```
上面代码的扩展运算符内部就调用Iterator接口，实际上这还提供了一种简便机制，可以在任何部署了`Iterator`接口，就可以对他使用扩展运算符，将其转化为数组
3. yield* 后面跟的是一个可遍历的结构，他会调用该结构的遍历器接口
```js
let generator = function* () {
  yield 1;
  yield* [2,3,4];
  yield 5;
};

var iterator = generator();

iterator.next() // { value: 1, done: false }
iterator.next() // { value: 2, done: false }
iterator.next() // { value: 3, done: false }
iterator.next() // { value: 4, done: false }
iterator.next() // { value: 5, done: false }
iterator.next() // { value: undefined, done: true }
```

### 字符串的Iterator接口
字符串是一个类似数组的对象，也原生具有Iterator接口
```js
var someSthing = "hi";
typeof someString[Symbol.iterator]; // function
var iterator = someString[Symbol.iterator]();
iterator.next();  // { value: "h", done: false }
iterator.next();  // { value: "i", done: false }
```
上述代码中，调用`Symbol.iterator`方法返回一个遍历器对象，这个遍历器对象上可以调用next方法，实现对字符串的遍历

### Iterator接口与Generator函数
`Symbol.iterator`方法最简单实现，就是与`Generator`函数相结合
```js
var myIterable = {};
myIterable[Symbol.iterator] = function* (){
    yield 1;
    yield 2;
    yield 3;
};
[...myIterator] // [1,2,3]
```
上面代码中，`Symbol.iterator`方法几乎不用部署任何代码，只要用yield命令给出每一步的返回值即可

### 遍历器对象的return(), throw()
遍历器对象除了具有`next`方法，还具有return和throw方法。
`return`方法的适用场合是，如果`for...of`循环提前退出（通常是因为出错，或者有`break`语句或者`continue`语句），就会调用return方法。如果一个对象在完成便利签需要清理或者释放资源就可以部署`return`方法
```js
function readLinesSync(file){
    return {
        next(){
            if(file.isAtEndOfFile()){
                file.close();
                return { done: true };
            }
        },
        return(){
            file.close();
            return { done: true }
        }
    }
}
```
但是需要注意的是return方法必须返回一个对象，这是`Generator`规格决定的

### for...of循环
for...of循环内部是调用的数据结构的`Symbol.iterator`方法
1. 数组
```js
const arr = [1, 2, 3];
let iterator = arr[Symbol.iterator]();
for(let v of iterator){
    console.log(v);
}
```

2. Set和Map结构
```js
var engines = new Set(["Gecko", 'Trident', "Webkit", "Webkit"]);
for(var e in engiens){
    console.log(e);
}
// Gecko
// Trident
// Webkit

var es6 = new Map();
es6.set("edition", 6);
es6.set("committee", "TC39");
es6.set("standard", "ECMA-262");
for(var [name, value] of es6){
    console.log(name, value);
}
// edition: 6
// committee: TC39
// standard: ECMA-262
```
上面对Map和Set结构的遍历，有两个值得注意的地方，首先遍历的顺序是按照各个成员被添加进数据结构的顺序。其次，Set结构被遍历时，返回的是一个值，而Map结构遍历时返回的是一个数组，该数组的两个成员分别为当前Map成员的键名和键值
```js
let map = new Map().set('a', 1).set('b', 2);
for(let pair of map){
    console.log(pair);
}
// ['a', 1]
// ['b', 1]
```

3. 计算生成的数据结构
有些数据结构是在现有的数据结构的基础上，计算生成的。比如`entires`, `keys`, `values`，这三个方法调用后生成遍历器对象，所便利的都是计算生成的数据结构
```js
let arr = ['a', 'b', 'c'];
for(let pair of arr.entries()){
    console.log(pair);
}
```

4. 类似数组的对象
并不是所有类似数组的对象都具有`iterator`接口，一个简便的解决编发，就是使用`Array.from`方法将其转为数组
```js
let arrayLike = {length: 2, 0: 'a', 1: 'b'};
for(let x of Array.from(arrayLike)){
    console.log(x);
}
```

5. 对象
对于普通的对象，`for...of`结构不能直接使用，会报错，必须部署了iterator接口后才能使用。但是，这样情况下，`for...in`循环依然可以用来遍历键名
```js
var es6 = {
  edition: 6,
  committee: "TC39",
  standard: "ECMA-262"
};
```
上面的要想使用iterator就必须部署遍历接口，或者转化为数组，一种比较简单的方式就是转化为数组
```js
for(var key of Object.keys(someObject)){
    console.log(key + ": " + someObject[key]);
}
```
