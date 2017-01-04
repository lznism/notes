### 事件调度
如果想让JavaScript中某段代码，在将来再运行，可以将它放在回调中
调用`setTimeout`的时候，会有一个延时事件排入队列。然后setTimeout调用之后的那行代码，接着再运行下一行代码，直到再也没有任何代码。这是才会运行

### 异步函数的类型
JavaScript环境提供的一步函数通常可以分为两大类:I/O函数和计时函数，就要使用这两类异步函数作为基本构造块

### 缓存性异步函数
```js
var calculationCache = {},
    calculationCallbacks = {},
    mathWorker = new Worker('calculator.js');
mathWorker.addEventListener('message', function(e){
    var message = e.data;
    calculationCache[message.formula] = message.result;
    calculationCallbacks[message.formula](message.result);
});
function runCalculation(formula, callback){
    if(formula in calculationCache){
        return callback(calculationCache[formula]);
    }
    if(formula in calculationCallbacks){
        return setTimeout(function(){
            runCalculation(formula, callback);
        }, 0);
    }
    mathWorker.postMessage(formula);
    calculationCallbacks[formla] = callback;
}
```