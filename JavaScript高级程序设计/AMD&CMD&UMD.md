### AMD
定义的第一个部分是依赖数组，第二部分是回调函数，只有当依赖的组件可用时，回调函数才会被执行

```js
define(['jquery'], function($) {
    function myFunc() {}
    return muFunc;
});
```

### CMD
典型的nodejs写法

```js
var $ = require('jquery');
function a() {}
function b() {}
function c() {}
module.export = {a,b,c};
```

### UMD
两种风格的通用模式

```js
(function(root, factory) {
    if(typeof define === 'function' && define.amd) {
        define(['jquery', factory])
    } else if(typeof exports === 'object') {
        module.exports = factory(require('jquery'));
    } else {
        root.returnExports = factory(root.jQuery);
    }
}(this, function($) {
    function myFunc() {}
    return myFunc;
}))
```