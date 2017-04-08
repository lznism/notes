* **map**
`map(func)`该函数对原来的流使用函数`func`进行转换，并生成新的流

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

* **combineLatest**

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
