### 类的修饰
修饰器是一个函数，用来改变类的行为。修饰器对类的行为的改变，是代码编译时发生的，而不是在运行时发生。这意味着修饰器能在编译阶段运行代码
```js
// 修饰器的第一个参数就是所要修饰的目标类
function testable(target){
    target.isTestable = true;
}

@testable
class MyTestableClass {}
console.log(MyTestableClass.isTestable); // true
```
上面的代码中，@testable就是一个修饰器。它修改了`MyTestableClass`这个类的行为
如果一个参数不够用，可以在修饰器外面再封装一层函数
```js
function testable(isTestable){
    return function(target){
        target.isTestable = isTestable;
    }
}
```
上面的例子给类添加了一个静态属性，如果想添加实例属性，可以通过目标类的`prototype`

### 给类添加一个静态属性
```js
function testable(target){
    target.prototype.isTestable = true;
}
@testable
class A {}
let a = new A();
a.isTestable; // true
```

### 方法的修饰
```js
function readonly(target, name, descriptor){
    // descriptor对象原来的值如下
    // {
    //   value: specifiedFunction,
    //   enumerable: false,
    //   configurable: true,
    //   writable: true
    // };
    descriptor.writable = false;
    return descriptor;
}
readonly(Person.prototype, 'name', descriptor);

class Person {
    @readonly
    name()[
        return `${this.first} ${this.last}`;
    ]
}
```

### core-decorator.js
这是一个第三方模块，提供了常见的几种修饰器
1. @autobind
使得类方法中的this绑定到原始对象
```js
import {autobind} from 'core-decorator.js';
class Person {
    @autobind
    getPerson() {
        return this;
    }
}
```
2. @readonly
修饰器使得属性和方法不可写
```js
import {readonly} from 'core-decorator.js';
class Person {
    @readonly
    entree = 'steak';
}
```
3. @override
检查子类的方法，是否覆盖了父类的同名方法，如果不正确就会报错
```js
import {override} from 'core-decorator.js';
class Parent {
    speak(first, second){}
}
class Child extends Parent {
    @override
    speak(){}
    // SyntaxError: Child#speak() does not properly override Parent#speak(first, second)
}
```
4. deprecate
该修饰器在控制台显示一条警告表示改方法将废除
```js
import {deprecate} from 'core-decorator.js';
class Person {
    @deprecate
    facepalm() {}
    @deprecate('we stopped facepalming')
    facepalmHard() {}
    @deprecate('We stopped facepalming', { url: 'http://knowyourmeme.com/memes/facepalm' })
    facepalmHarder() {}
}
person.facepalm();
person.facepalmHard();
person.facepalmHarder();
// DEPRECATION Person#facepalmHarder: We stopped facepalming
//
//     See http://knowyourmeme.com/memes/facepalm for more details.
//
```
5. suppressWarnings
该修饰器抑制`decorated`修饰器导致的`console.warn()`调用。但是异步代码发出的调用除外
```js
import {suppressWarnings} from 'core-decorator';
class Person {
    @deprecated
    facepalm() {}
    @suppressWarnings
    facepalmWithoutWarning(){
        this.facepalm();
    }
}
let p = new Person();
p.facepalmWithoutWarning(); // no warning is logged
```

### 使用修饰器实现自动发布事件
我们可以使用修饰器使得对象的方法被调用时，自动发布一个事件
```js
import postal from 'postal/lib/postal.loadsh';
export default function publish(topic, channel){
    return function(target, name, descriptor) {
        const fn = descriptor.value;
        descriptor.value = function(){
            let value = fn.apply(this.arguments);
            postal.channel(channel || target.channel || "/").publish(topic, value);
        }
    }
}
```