### reducer什么时候可以复用
首先我们来看一个简单的reducer
```js
const count = (state=0, action) => {
    switch(action.type) {
        case 'add':
            return state+1;
        default:
            return state;
    }
}
```
如果现在有两个UI都需要共用上面的reducer, 那个你可以dispatch一个action,但是很显然这样的后果就是两个UI最终的数据会完全一至。可见这样的reducer完全不能复用
这里有一种做法就是，让发出的actionType唯一，那么如何做到唯一呢？这里有一种推荐的做法就是给actionType加上前缀，变换之后的reducer是这样的
```js
const generatorCount = (prefix, state) => {
    const add = prefix + 'add';
    const initialState={...state};
    return (state=initialState, action) => {
        switch(action.type) {
            case add:
                return state+1;
            default:
                return state;
        }
    }
}
```
如此一来就能够解决复用的问题，而对于prefix，可以根据自己的项目来定，例如`${页面名}_${模块名}`

### reducer增强
增强reducer的方法主要有以下三种
- 能够处理额外的action
- 能够维护更多的state
- 能将不能处理的action委托给原始的reducer处理

```js
const undoable = (reducer) => {
    const initialState = {
        // 用来记录过去的state
        past: [],
        // 用一个空的action调用reducer来产生当前的初始值
        present: reducer(undefined, {}),
        // 记录后续的state
        futrue: [],
    };
    return (state = initialState, action) => {
        const {past, present, futrue} = state;
        switch(action.type) {
            case '@@redux-undo/UNDO':
                const previous = past[past.length - 1];
                const newPast  = past.slice(0, past.length - 1);
                return {
                    past: newPast,
                    present: previous,
                    future: [preset, ...future],
                }
            default:
                const newPresent = reducer(present, action);
                if(present === newPresent) {
                    return state;
                }
                return {
                    past: [...past, present],
                    present: newPresent,
                    future: []
                }
        }
    }
}
```