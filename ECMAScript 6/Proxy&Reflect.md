### Proxy概述
`Proxy`用于修改某些操作的默认行为，等同于在语言层面作出修改，相当于一种元编程，即对编程语言进行编程
```js
var obj = new Proxy({}, {
    get: function(target, key, receiver){
        console.log(`getting ${key}`);
        return Reflect.get(target, key, receiver);
    },
    set: function(target, key, value, receiver){
        console.log(`setting ${key}`);
        return Reflect.set(target, key, value, receiver);
    }
})
```
上面的代码对一个空对象假设了一层拦截，重新定义了属性的读取和设置行为。
当使用`obj`这个对象时就会出现
```js
obj.count = 1;
// setting count!
++obj.count;
// getting count!
// setting count!
// 2
```
上面的代码说明，Proxy实际上重载了点运算符，即用自己的定义覆盖了语言原始的定义
```js
var proxy = new Proxy(target, handler);
```
Proxy对象的所有用法，都是上面的形式，不同的只是`handler`参数的写法。其中`new Proxy()`表示生成一个Proxy实例。target参数表示所要拦截的目标对象，handler参数也是一个对象，用来定制拦截行为
```js
var proxy = new Proxy({}, {
    get: function(target, property){
        return 35;
    }
});
proxy.time; // 35
proxy.name; // 35
proxy.title;// 35
```
上面的代码中，作为构造函数，Proxy接受两个参数。第一个参数是所要代理的目标对象，第二个参数是一个配置对象，对于每个被代理的操作需要提供一个对应的处理函数，该函数用来拦截对应的操作。上面代码中，配置对象有一个`get`方法，用来拦截对目标对象属性方位请求
注意要使得Proxy起作用，必须针对Proxy实例进行操作，而不是针对目标对象进行操作
对于可以设置但是没有设置拦截的操作，则直接落在目标对象上，按照原先的方式产生结果
- get(target, propKey, receiver) 拦截对象的读取
- set(target, propKey, value, receiver) 拦截对象属性的设置
- has(target, propKey) 拦截`propKey in proxy`的操作
- deleteProperty(target, propKey) 拦截delete proxy[propKey]的操作，返回一个布尔值
- ownKeys(target) 
- getOwnPropertyDescriptor(target, propKey)
- defineProperty(target, propKey, propDesc)
- preventExtensions(target)
- getPrototypeOf(target)
- isExtensible(target)
- setPrototypeOf(target, proto)
- apply(target, object, args)
- construct(target, args)

### Proxy实例方法
#### get()
该方法用于拦截某个属性的读取操作
```js
var person = {
    name: 'lznism'
};
var proxy = new Proxy(person, {
    get: function(target, property){
        if(property in target){
            return target[property];
        }else{
            throw new ReferenceError("Property \"" + property + "\" does not exist.");
        }
    }
});
proxy.name; // 'lznism'
proxy.age; // 'error'
```
上面的代码表示，如果访问目标对象不存在的属性，会抛出一个错误，如果没有这个拦截函数，访问不存在的属性，只会返回`undefined`

#### set()
set方法用来拦截某个属性的赋值操作
```js
let validator = {
    set: function(obj, prop, value){
        if(prop === 'age'){
            if(!Number.isInteger(value)){
                throw new TypeError('the age is not an integer');
            }
            if(value > 200){
                throw new RangeError('the age seems invalid');
            }
            obj[prop] = value;
        }
    }
}
let person = new Proxy({}, validator);
person.age = 100;
person.age; // 100
person.age = 'young'; //Error
person.age = 300; // Error
```
上面的代码中由于设置了存值函数`set`任何不符合要求的age属性赋值，都会抛出一个错误

#### apply()
该方法拦截函数的调用
```js
var handler = {
    apply(target, ctx, args){
        return Reflect.apply(...arguments);
    }
}
```

#### has()
该方法用来拦截`HasProperty`操作，即判断对象是否具有某个属性时，这个方法会失效。
下面的例子用`has`方法隐藏某些属性，不被`in`运算符发现
```js
var handler = {
    has(target, key){
        if(key[0] === '_'){
            return false;
        }
        return key in target;
    }
}
var target = {_prop: 'foo', prop: 'foo'};
var proxy = new Proxy(target, handler);
'_prop' in proxy; // false;
```
上面的代码中， 如果原对象的属性名的第一个字符是下划线，`proxy.has`就会返回`false`,从而不会被`in`运算符发现

#### construct()
`construct`方法用于拦截`new`命令，下面是拦截对象的写法
```js
var handler = {
    construct(target, args, newTarget){
        return new target(...args);
    }
}
```

#### deleteProperty()
`deleteProperty`方法用于拦截`delete`操作，如果这个方法抛出错误或者返回false，当前属性就无法被`delete`命令删除
```js
var handler = {
    deleteProperty(target, key){
        invariant(key, 'delete');
        return true;
    }
}
```

#### defineProperty()
该方法拦截了`Object.defineProperty`操作
```js
var handler = {
    defineProperty(target, key, descriptor){
        return false;
    }
}
```

#### getOwnPropertyDescriptor()
该方法拦截`Object.getOwnPropertyDescriptor`返回一个属性描述对象或者`undefined`
```js
var handler = {
    getOwnPropertyDescriptor(target, key){
        if(key[0] === '_'){
            return;
        }
        return Object.getOwnPropertyDescriptor(target, key);
    }
}
```

#### getPrototypeOf()
该方法主要用来拦截`Object.getPrototypeOf()`操作
```js
var handler = {
    getPrototypeOf(target){
        return proto;
    }
}
```

#### isExtensible()
这个方法拦截`Object.isExtensible`操作
```js
var handler = {
    isExtensible(target){
        return true;
    }
}
```

#### ownKeys()
`ownKeys`方法用来拦截`Object.keys()`操作
```js
var handler = {
    ownKeys(target){
        return ['hello', 'world'];
    }
}
```

#### preventExtensions()
这个方法拦截`Object.preventExtensions`。该方法必须返回一个布尔值
```js
var handler = {
    preventExtensions(target){
        return true;
    }
}
```
但是只有当`Object.isExtensible(proxy)`为false(即不可扩展)时，`proxy.perventExtensions`才能返回true;

#### setPrototypeOf()
`setPrototypeOf()`方法用来拦截`Object.setPrototypeOf`方法
```js
var handler = {
    setPrototypeOf(target, proto){
        throw new Error('changing the prototype is forbidden');
    }
}
```

### Proxy.revocable()
该方法返回一个可取消的`Proxy`实例
```js
let target = {};
let handler = {};
let {proxy, revoke} = Proxy.revocable(target, handler);
proxy.foo = 123;
proxy.foo; //123
revoke();
proxy.foo; // TypeError: Revoked
```
Proxy.revocable方法返回一个对象，该对象的`proxy`属性是`proxy`实例，`revoke`属性是一个函数，可以取消`Proxy`实例，上面的代码中，当执行`revoke`函数之后，在访问`Proxy`实例，就会抛出一个错误

### Reflect
`Reflect`对象与`proxy`对象一样，也是ES6为了操作对象而提供的新的API，`Reflect`对象的设计目的
1. 将Object对象上的一些明显属于语言内部的方法(比如Object.defineProperty)，放到`Reflect`对象上。x
2. 修改某些Object方法的返回结果，让其变得合理。比如`Objct.defineProperty(obj, name, desc)`在无法定义属性时，会抛出一个错误`Reflect.defineProperty(obj, name, desc)`则会返回false
3. 让`Object`操作都变成函数行为。某些`Object`操作是命令，比如`name in obj`和`delete obj[name]`
4. Reflect对象上的方法与`proxy`对象的方法意义对应，只要是`Proxy`对象的方法就能在`Reflect`对象上找到对应的方法。这就让`Proxy`对象可以方便的调用对应的`Reflect`方法，完成默认行为，修改行为的基础


