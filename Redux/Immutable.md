### fromJS

`fromJS(jsValue, reviver)`可以接受两个参数。
第一个是js Object或者是Array
第二个是函数，该函数可以遍历jsValue

```js
fromJS({a:1, b:[1,2,3]});
```

### is
用于判断两个immutable数据是否相等

```js
import { Map, is } from 'immutable';
const map1 = Map({a:1, b:2});
const map2 = Map({a:1, b:2});
is(map1 === map2);
```

### hash
产生一个31位的随机数

```js
hash(value: any): number;
```

### List
将数组类型的js数据结构转化为immutable类型

#### set()
设置一个值

#### delete()
删除一个值

#### insert()
插入一个值

#### clear()
清楚list中的所有数据

#### push()
从尾部插入一个值

#### pop()
从尾部删除一个值

#### unshift()
从头部插入一个值

#### shift()
从头部删除一个值

#### update()
更新一个值

#### merge()
合并两个List对象，从下标为1的开始

#### setIn()
#### deleteIn()
#### updateIn()
#### mergeIn()
#### mergeDeepIn()

#### concat()
将两个List对象连接起来

#### map()
#### filter()

### zip() 
将两个List对象组合在一个

```js
const l1 = List([1,2,3,4]);
const l2 = List([1,2,3,4]);
const l3 = li.zip(l2); // List [[1,1], [2,2], [3,3], [4,4]]
```

### zipWith()
将组合在一个的List对象运行一个计算函数
```js
const l1 = List([1,2,3,4]);
const l2 = List([1,2,3,4]);
const l3 = l1.zipWith((a,b) => a+b, b);
```