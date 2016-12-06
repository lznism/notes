#### 间或异步函数
如果称一个函数是异步的，其意思是这个函数会导致将来再运行一个函数(这个函数取自事件队列)
间或异步函数：有些函数有时是异步的但有时不是，jQuery的同名函数$，可用于延迟函数直至DOM已经结束加载。但是，若DOM已经结束加载，则不存在任何延时，$的回调将会立即触发。
```js
// application.js
$(function(){
	utils.log('Ready');
});
// util.js
window.utils = {
	log: function(){
		if(window.console) console.log.apply(console, arguments);
	}
}
<script src="application.js"></script>
<script src="util.js"></script>
```
上述这段代码运行的很好，但前提是浏览器并未从缓存中加载页面，如果从缓存中加载，DOM早在脚本运行之前就已经加载完毕，这样`utils.log()`就会出现引用错误。

#### 缓存型异步函数
间或异步的函数有一个常见的变种是可缓存结果的异步请求类函数
```js
var calculationCache = {},
	calculationCallbacks = {},
	mathWorker = new Worker('calculation.js');
mathWorker.addEventListener('message', function(e){
	var message = e.data;
	calculationCache[message.formula] = message.result;
	calculationCallbacks[message.formula](message.result);
});
function runCalculation(formula, callback){
	if(formula in calculationCache){
		return callback(calculationCache[formula]);
	};
	if(formula in calculationCallbacks){
		return setTimeout(function() {
			runCalculation(formula, callback);
		}, 0);
	}
	mathWorker.postMessage(formula);
	calculationCallbacks[formula] = callback;
}
```
上面的函数中，当结果已经缓存，`runCalculation`函数是同步的，否则就是异步的

### 异步递归与回调存储
在`runCalculation`函数中，为了等待Worker对象完成自己的工作，或者通过延时而重复相同的函数调用(即异步递归)，或者简单的存储回调结果。
异步递归的可怕之处，在于等待任务完成期间，可触发之延时次数是不受限制的，此外异步递归还毫无必要的复杂化了应用程序的事件结构。
为了避免异步递归，可以为每个公式存储一个回调数组
```js
var calculationCache = {},
    calculationCallbacks = {},
    mathWorker = new Worker('calculation.js');
mathWorker.addEventListener('message', function(e){
    var message = e.data;
    calculationCache[message.formula] = message.result;
    calculationCallbacks[message.formula].forEach(function(callback){
        callback(message.result);
    });
});
function runCalculation(formula, callback){
    if(formula in calculationCache){
        return callback(calculationCache[formula]);
    };
    if(formula in calculationCallbacks){
        return calculationCallbacks[formula].push(callback);
    }
    mathWorker.postMessage(formula);
    calculationCallbacks[formula] = [callback];
}
```

### 返值与回调混搭
```js
var webSocketCache = {};
function openWebSocket(serverAddress, callback){
    var socket;
    if(serverAddress in webSocketCache){
        socket = webSocketCache[serverAddress];
        if(socket.readyState === WebSocket.OPEN){
            callback();
        } else {
            socket.onopen = _.compose(callback, socket.onopen);
        }
    }else{
        socket = new WebSocket(serverAddress);
        webSocketCache[serverAddress] = socket;
        socket.onopen = callback;
    }
    return socket;
}
```
上面代码的问题是如果在缓存中打开，则会在函数返回值之前就运行回调函数，这样会使以下代码崩溃
```js
var socket = openWebSocket(url, function(){
    socket.send('hello server');
});
```
如何解决？可以讲回调函数封装在`setTimeout`中
```js
if(socket.readyState === WebSocket.OPEN){
    setTimeout(callback, 0);
}else{
    // ....
}
```

### 嵌套式回调的解嵌套
```js
function checkPassword(username, passwordGuess, callback) {
    var passwordHash;
    var queryStr = 'SELECT * FROM user WHERE username = ?';
    db.query(qyeryStr, username, queryCallback);
    function queryCallback(err, result) {
        if (err) throw err;
        passwordHash = result['password_hash'];
        hash(passwordGuess, hashCallback);
    }
    function hashCallback(passwordGuessHash) {
        callback(passwordHash === passwordGuessHash);
    }
}
```