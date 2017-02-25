如果我们需要用额外的特性来支持标注或修改类及成员。`装饰器(decorator)`为我们在类的声明及成员上通过元编程添加标注提供一种方式

`Decorator`能够被附加到类声明，方法，访问符，属性或者参数上。

如果有一个`sealed`的装饰器，我们会这样定义`sealed`函数

```js
function sealed(target) {
    // do something with target...
}
```

### 类修饰器
装饰器在类声明之前被声明(`紧靠着类声明`)。类装饰器应用于类构造函数，可以用来监视，修改或者替换类定义。
类修饰器表达式会在运行时被当做函数被调用，类的构造函数作为其唯一的参数
如果类装饰器返回一个值，它会使用提供的构造函数作为其唯一的函数

```js
function sealed(constructor: Function) {
    Object.seal(constructor);
    Object.seal(constructor.prototype);
}
@sealed
class Greeter {
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return `Hello ${this.greeting}`;
    }
}
```

当`sealed`被执行的时候，它将密封此类的构造函数和原型

### 方法修饰器
方法装饰器声明在一个方法的声明之前`(紧靠着方法声明)`
方法修饰器表达式会在运行时当做函数被调用
- 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象
- 成员的名字
- 成员的描述符

```js
function enumerable(value: boolean) {
    return function(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
        descriptor.enumerable = value;
    }
}
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    @enumerable(false)
    greet() {
        return `Hello ${this.greeting}`
    }
}
```

这里的`@enumerable(false)`是一个装饰器工厂。当装饰器`@enumerable(false)`被调用时，它会修改属性描述符的`enumerable`

### 访问器修饰符
访问器修饰符声明在一个访问器声明之前(`紧靠着访问器声明`)

```js
function configuable(value:boolean) {
    return function(target:any, property:string, descriptor:PropertyDescriptor) {
        descriptor.configuable = value;
    }
}
class Point {
    private _x: number;
    private _y: number;
    constructor(x: number, y:number) {
        this._x = x;
        this._y = y;
    }

    @configruable(false)
    get x() {
        return this._x;
    }

    @configruable(false)
    set y() {
        return this._y;
    }
}
```
































