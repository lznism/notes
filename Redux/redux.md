### 单一数据源
整个应用的state被储存在一棵object tree中，并且这个object tree只存在于一个store中
``` javascript
console.log(store.getState());
```
### State是只读的
唯一改变state的方法是触发action,action是用于描述已发生事件的普通对象
``` javascript
store.dispatch({
	type: 'COMPLETE_TODO'
})
```
### 使用纯函数来执行修改
为了描述`action`如何改变`state tree`,你需要编写`reducer`
``` javascript
function visibilityFilter(state = 'SHOW_ALL', action){
    switch(action.type){
        case 'SET_VISIBILITY_FILTER':
            return action.filter;
        default:
            return state;
    }
}
```
reducer只是纯函数，接收`state`和`action`。reducer可以拆分成多个小的reducers,独立操作state tree的不同部分,然后将这些reducers再次组合起来
```
import {combineReducers} from 'redux';
let reducer = combineReducers({reducer1, reducer2, ...});
```
### Action
action的本质是javascript对象
```
{
    type: 'ADD_TODO',
    text: 'build my first redux app'
}
```
一般情况下，由于数据存放在数组中，所以我们可以通过下标`index`来引用特定的任务。而实际项目中一般会在新建数据时生成一个唯一的ID作为数据的引用标识
```
{
    type: 'ADD_TODO',
    index: 5
}
```
**我们应当尽量减少action中数据传递**,上面的例子中，传递`index`就比把整个任务传递过去要好
### action创建函数
action创建函数就是生成action的方法，`action`和`action`创建函数这两个概念很容易混在一起.在redux中创建函数只是一个简单的返回`action`
```
function addTodo(text){
    return {
        type: 'ADD_TODO',
        text
    }
}
```
### reducer
action只是描述了事件的对象，并没有指明如何更新state。而这个正是reducer要做的事情
```
{
visivilityFilter: 'SHOW_ALL',
todos: [
    {
        text: 'Considering using redux',
        completed: true
    },{
        text: 'Keep all state in a single tree',
        completed: false
    }
]
}
```
如何把state描述出来无疑是个难点。state一般分为两种，一种是UI相关的state，还有一种是与UI间接相关的数据；因此我们需要尽量把这些东西分开。
把所有的数据存放到一个对象中，每个数据以ID为主键，不同实体或者列表通过ID引用数据。把应用的state想象成数据库。
#### 处理action
保持reducer纯净非常重要，永远不要在reducer中做这些操作
* 修改传入参数
* 执行有副作用的操作，如API请求，路由跳转
* 调用非纯函数，如`Math.now()`和`Math.random`等

```
function todoApp(state = initialState, action){
    swith(action.type){
        case SET_VISIBILITY_FILTER: 
            return Object.assign({}, state, {
                visibilityFilter: action.filter
            });
        case ADD_TODO:
            // 不直接修改state中的字段，而是返回新的对象。新的todos对象就相当于在末尾加上新建的todos
            // 而这个新的todo又是基于action中的数据创建
            return Object.assign({}, state, {
                todos: [
                    ...state.todos,
                    {
                        text: action.text,
                        completed: false
                    }
                ]
            });
        default: 
            return state;
    }
}
```
拆分reducer
todos和visibilityFilter看起来是相互独立的
### store
* 维持应用的`state`
* 提供`getState()`方法获取state
* 提供`dispatch(action)`方法更新state
* 通过`subscribe(listener)`注册监听器
* 通过`subscribe(listener)`返回函数注销监听器

**redux应用只有一个单一的store**
根据reducer创建store非常容易。在上面创建reducers的的过程中，我们使用`combineReducers()`将多个reducer合并为一个。现在我们将其倒入，并传递给`createStore()`
```
import {createStore} from 'redux';
import todoApp from './reducers';
let store = createStore(todoApp);
```


















