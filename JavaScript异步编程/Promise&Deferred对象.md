```js
$.get('/mydata', {
    success: onSuccess,
    failure: onFailure,
    always: onAlways
});
// 由于jQuery.ajax返回的是Promise对象，我们还可以这样写
var promise = $.get('/mydata');
promise.done(onSuccess);
promise.fail(onFailure);
promise.always(onAlways);
```

### 生成纯的Promise对象
Deferred对象也是Promise对象，那么，如何得到一个不是Deferred对象的Promise对象呢？很简单，对Deferred对象调用`promise`方法即可
```js
var promptPromise = promptDeferred.promise();
```
对同一个Deferred对象生成多个Promise对象是毫无意义的。事实上jQuery给出的只不过是同一个对象
```js
var promise1 = promptDeferred.promise();
var promise2 = promptDeferred.promise();
console.log(promise1 === promise2); // true
```
在jQuery中，任何动画方法都可以接受传入的回调，以便在完成动画时，发出通知
```js
$('.error').fadeIn(afterErrorShown);
```


