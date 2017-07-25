* **map**
`map(func)`该函数对原来的流使用函数`func`进行转换，并生成新的流

```js
var responseStream = requestStream.map(url => {
	return Rx.Observable.fromPromise($.getJSON(url));
})
```

* **buffer**

* **fromEvent**
将事件转化为流(可观测对象)，接受两个参数，第一个是DOM，第二个是事件名

```js
const clickStream = Rx.Observable.fromEvent(button, 'click');
```

* **filter**
接受一个函数做参数，过滤流

```js
// 获取值大于2的流
stream.filter(x => x > 2);
```

* **startWith**

* **flatMap**
可以将枝干的流的值发射到主流上

```js
var responseStream = requestStream.flatMap(url => {
	return Rx.Observable.fromPromise($.getJSON(url));
});
```

* **combineLatest**
通过处理函数总是将指定的可观察对象序列中最新发射的值合并为一个可观察对象
接受两个参数，第一个是一系列的可观察对象或可观察对象的数组
第二个在所有可观察对象都发设置后调用的处理函数

* **merge**

* **just**


* **fromPromise**
将Promise对象转化为流(可观测对象)，接受一个promise对象作为参数

```js
const stream = Rx.Observable.fromPromise(promise);
```

* **toPromise**

* **create**
创建一个流(可观测对象)

* **amb**
从一系列流中，订阅最先发射的值得stream，并忽略其余的stream

```js
// 以下的例子是先点击哪个按钮就先触发哪个流
const btn1 = $('#btn1'),
	  btn2 = $('#btn2');
const stream = Rx.Observable.amb(
	Rx.Observable.fromEvent(btn1, 'click'),
	Rx.Observable.fromEvent(btn2, 'click')
);
const subscription = stream.subscribe(
	x => console.log(x),
	err => console.error(err),
	() => console.log(`completed`)
);
```

* **of**
* **from**
* **interval**
* **empty**
* **case**
选择序列中特定的观察对象进行订阅，在特定的观察对象不存在的情况下，返回传入的默认观察对象
第一个参数是返回键的字符串的函数
第二个参数是包含可观察对象的JavaScript对象
第三个是默认可观察对象，如果没有匹配第一个参数，则该对象会返回，如果没有明确指定，将返回附加了指定scheduler的Rx.Observable.empty对象

```js
var sources = {
	hello: Rx.Observable.just('clx'),
	world: Rx.Observable.just('wxq')
};
var subscription = Rx.Observable.case(() => 'hello', sources, Rx.Observable.empty());
subscription.subscribe(x => console.log(x)); //=> clx
```
* **catch**
序列中的可观察对象因为异常而终止后，继续订阅序列中的其他可观察对象
`Rx.Observable.catch(...args)` 接受的参数是可观察对象序列

```js
var obs1 = Rx.Observable.throw(new Error('error'));
var obs2 = Rx.Observable.return(42);
var source = Rx.Observable.catch(obs1, obs2);
var subscription = source.subscribe(
	x => console.log(x),
	err => console.log(err),
	() => console.log('completed!')
); // => 42 z在obs1抛出错误之后继续执行obs2
```
* **take**

* **Subject**
多路推送的观测对象

```js
var subject = new Rx.Subject();
subject.subscribe({
	next: (x) => console.log(x)
});
subject.subscribe({
	next: (x) => console.log(x)
});
subject.next(1);
subject.next(2);
//1
//1
//2
//2
```
subject是一个Observer，可以将其作为普通的订阅对象

```js
var observable = Rx.Observable.from([1,2,3,4]);
observable.subscribe(subject);
```

* **do**
做一些不影响流的事情，比如日志输出,渲染DOM

```js
Rx.Observable.fromEvent(input, 'keyup')
    .map(() => input.val())
    .filter((text) => !!text)
    .do(val => console.log(val));
```

* **debounce**
当两次可观测对象的间隔不超过规定值时，后面的操作先不触发，当间隔操作规定值时，再触发

```js
Rx.Observable.from(input, 'keyup')
	// 间隔操作400ms再进行后面的动作
	.debounce(400)
	.map(() => input.val())
	.filter((text) => !!text)
	.do(val => console.log(val));
```

* **distinctUntilChanged**
对于相邻的可观察对象，如果它们的返回值一样，则只取一个

```js
Rx.Observable.from(input, 'keyup')
	// 间隔操作400ms再进行后面的动作
	.debounce(400)
	.map(() => input.val())
	.filter((text) => !!text)
	.distinctUntilChanged()
	.do(val => console.log(val));
```

* **switchMap**
当Observable触发某个时间返回新的Observable时，将取消之前触发的事件，并且不再关心并返回结果的处理，只关注当前这一个。
也就是说，发送多个请求时，不关心之前的处理请求，只处理最后一个请求

```js
const observable = Rx.Observable.fromEvent(input, 'keyup')
    .debounce(400)
    .map(() => input.val())
    .filter((text) => !!text)
    .do(val => console.log(val))
    // 只关注最新的请求
    .switchMap(getRepos);
```

* **range**
返回某个返回内的数值

```js
// 返回一个1-5的可观察对象
var obs = Rx.Observable.range(1,5);
```

* **takeWhile**
给予流一个判断，只有当`takeWhile`中的回调返回true时，流才会继续进行，否则将中断之后的事件

```js
var obs = Rx.Observable.range(1,5)
	.takeWhile(x => x>3); // 4,5
```
