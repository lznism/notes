### Promise含义
Promise是异步编程的一种解决方案。
所谓的Promise，简单的说就是一个容易，里面保存着某个未来才会结束的事件的结果
Promise特点
1. 对象的状态不受外界影响。`Promise`对象代表一个异步操作，有三种状态`pending`, `resolved`, `rejected`.只有异步操作的结果可以决定当前是哪一种状态
2. 一旦状态改变，就不会再变，任何时候都可以得到这个结果

### 基本用法
ES6规定，Promise对象是一个构造函数，用来生成Promise实例
```js
var promise = new Promise(function(resolve, reject){
    if(/*异步操作成功*/){
        resolve(value);
    }else{
        reject(error);
    }
});
```
Promise构造函数接受一个函数作为参数，该函数的参数分别是`resolve`, `reject`。他们是两个函数，有JavaScript引擎提供，不用自己部署
`promise`实例生成后，可以用`then`方法分别指定`resolved`状态和`reject`状态的回调函数
```js
promise.then(function(value){
    // success
}, function(error){
    // failture
})
```
then方法可以接收两个函数作为参数。第一个回调函数是promise对象的状态变为resolve时调用，第二个回调函数是promise状态变为reject时调用
```js
function timeout(ms){
    return new Promise((resolve, reject) => {
        setTimeout(resolve, ms, 'done');
    })
}
timeout(100).then((value) => {
    console.log(value);
});
```
上面代码中,`timeout`方法返回一个Promise实例，表示一段时间以后才会发生结果。过了指定的时间以后，Promise实例的状态变为Resolved, 就会触发then方法指定的回调函数
```js
let promise = new Promise(function(resolve, reject){
    console.log("Promise");
    resolve();
});
promise.then(function(){
    console.log('Resolve');
});
console.log('hi');
// Promise
// hi
// Resolved
```
异步加载图片的例子
```js
function loadImageAsync(url){
    return new Promise(function(resolve, reject){
        image.onload = function(){
            resolve(image);
        }
        image.onerror = function(){
            reject(new Error('could not load image at ' + url));
        }
        image.src = url;
    })
}
```
下面是两个promise实例嵌套的例子
```js
var p1 = new Promise(function(resolve, reject){
    setTimeout(() => reject(new Error('fail')), 3000);
});
var p2 = new Promise(function(resolve, reject){
    setTimeout(() => resolve(p1), 1000);
});
p2.then(result => console.log(result))
  .catch(error => console.log(error));
//fail
```
上面的p1是promise, 3s之后被rejected. p2的状态在1秒之后改变，`resolve`方法返回的是p1. 由于p2返回的是另一个Promise，所以后面的then语句都变成针对后者（p1）。又过了2秒，p1变为rejected，导致触发catch方法指定的回调函数。

### Promise.prototype.then()
Promise实例具有then方法，也就是说，`then`方法是定义在原型对象`Promise.prototype`上的。它的作用是为Promise实例添加状态改变时的回调函数。
```js
var promise = new Promise((resolve,reject) => {});
promise.then((json) => json.post)
    .then(post => {...});
```
`promise`实例的`then`方法返回值会作为下一个`then`方法的参数

### Promise.prototype.catch
`catch`方法用于当promise的状态变为`reject`是发生的回调函数，另外如果`then`中发生了错误，`catch`也会捕获这个错误
如果promise的状态已经`resolve`那么在这个resolve后面抛出的错误将不再被`catch`捕获
以下代码等价
```js
p.then(val => cnosole.log('fulfilled', val))
    .then(null, err=> console.log('rejected', err));

p.then(val => console.log('fulfilled', val))
    .then(null, err => console.log('rejected', err));
```

