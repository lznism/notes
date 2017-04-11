### setState异步更新
setState通过一个队列机制实现state更新

```js
var nextState = this._processPendingState(nextProps, nextContext);
```

### setState循环调用风险
performUpdateIfNecessary方法会获取_pendingElement, _pendingStateQueue, _pendingForceUpdate, 并且调用recieveComponent和updateComponent方法
如果在shouldComponentUpdate和componentWillUpdate中调用setState方法，此时this._pendingStateQueue != null，此时又会调用shouldComponentWillUpdate方法，造成死循环

### 事物
事物就是将需要执行的方法使用wrapper封装起来，再通过事物提供的perform方法执行