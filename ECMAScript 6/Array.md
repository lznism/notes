### Array.from()
用于将`两类对象`转化为`真正的数组`:`类似数组的对象`和`可遍历的对象`
```js
let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
}
let arr = Array.from(arrayLike);
```
实际应用中常见的类似数组的对象是DOM返回的`NodeList`集合，以及函数内部的`arguments`对象，`Array.from`都可以将他们转化为真正的数组，比如`querySelectorAll`方法返回的是一个类似数组的对象，只有将这个对象转化为真正的数组才能使用`forEach`方法
只要是部署了遍历接口的数据结构，`Array.from`都能将其转化为数组
```js
Array.from('hello'); // ['h', 'e', 'l', 'l', 'o']
```
如果参数是一个数组，那么会返回一个一模一样的数组
```js
Array.from([1,2,3]); // [1,2,3]
```
除此之外扩展运算符也可以将某些数据结构转化为数组
```js
[...document.querySelectorAll('div')]
```
扩展运算符背后调用的是遍历接口`Symbol.iterator`,如果一个对象没有部署这个接口，就无法转换。
`Array.from()`还可以接受第二个参数，作用类似于数组的`map`方法，用来对每个元素进行处理。将处理后的值放入返回的数组
```js
Array.from(arrayLike, x => x * x);
```

### Array.of()
该方法将一组值，转化为数值
```js
Array.of(1,2,3); // [1,2,3]
```
`Array.of`基本上可以代替`Array()`或`new Array()`，并且不存在由于参数不同而导致的重载。行为非常统一
```js
Array.of() // []
Array.of(undefined) // [undefined]
Array.of(1) // [1]
Array.of(1, 2) // [1, 2]
```
### copyWithin()
在数组内部，将制定的位置的成员复制到其他位置，然后返回当前数组。也就是说使用这个方法，会修改当前数组
```js
// 第一个参数表示从该位置开始覆盖数据
// 第二个参数表示从该位置读取数据，默认为0
// 第三个参数表示从该位置开始停止读取数据
[1,2,3,4,5].copyWithin(0,3); //[4,5,3,4,5]
```
上面的代码表示将3号位知道数组结束的成员，复制到从0号位开始的位置，结果覆盖了原来的1，2

### find()和findIndex()
`find()`用于找出第一个符合n条件的数组成员。参数是一个回调函数，所有的数组成员依次执行该回调函数，直到找出第一个返回值未true的成员，然后返回该成员，如果没找到符合条件的成员则返回`undefined`
```js
[1,2,-5,10].find(n => n<0);
```
上面的代码中，`find`方法的回调函数可以接收三个参数，`value, index, arr`
`findIndex()`顾名思义就是找到第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回-1

### fill()
`fill`方法使用给定值，填充一个数组
```js
[1,2,3].fill(1); // [1,1,1]
```
`fill`方法可以接收第二个和第三个参数，用于指定填充起始位置和结束位置
```js
['a', 'b', 'c'].fill(7,1,2); // ['a', 7, 'c']
```

### entries() keys() values()
ES6提供了三个新方法，`entries()`, `keys()`, `values()`,用于遍历数组。可以使用`for...of`循环进行遍历，唯一的区别是`keys()`是对键名的遍历，`values()`是对值的遍历，`entries()`是对键值的遍历
```js
for(let index of [1,2].keys()){
	console.log(index);
}
for(let item of [1,2].values()){
	console.log(item);
}
for(let [index, item] of [1,2].entries()){
	console.log(index, item);
}
```

### includes()
判断数组内是否包含指定的值
```js
[1,2,3].includes(1); // true
```
改方法的第二个参数标识搜索的起始位置，默认为0，如果第二个参数为负数，则表示倒数的位置
```js
[1,2,3].includes(3,3); // false
```

### 数组的空位
数组的空位指，数组的某一个位置没有任何值。比如`Array`构造函数返回数组都是空位。