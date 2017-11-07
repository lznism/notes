### 变量声明
普通类型的变量声明如下

```js
let isDone: boolean = false;
let num: number = 1234;
let str: string = 'hello world';
let arr:number[] = [1,123,3];
let x:[string, number] = ['hello', 1234];
```

枚举类型

```js
enum Color {Red, Green, Blue};
let c:Color = Color.Blue;
```

任意值
`any`类型可以调用方法
`Object`类型不能调用方法

```js
let notSure: any = 1234;
let notSureArr: any[] = [1,2,3, 'hello'];
```

空值
函数表现为没有任何返回值

```js
function test(): void {
    alert('this is my waining message');
}
```

类型断言

```js
let someValue: any = 'hello world';
let strLen: number = (<string>someValue).length;

let someValue2: any = 'hello world2';
let strLen2: number = (someValue2 as string).length;
```

`typescript`也可以使用解构赋值

```js
function test({a, b}: {a:number, b?:string}): void {}
```

### 接口

```js
interface LabelledObj {
    label: string
};
function printLabel(labelledObj: LabelledObj) {
    console.log(labelledObj.label);
}
```
代表了LabelledObj中必须要有一个label的属性，且其类型为`string`，通俗的说就是给这个变量制定了一定的规则，被接口描述的对象必须是有满足条件的这些属性

接口中的可选属性
可选属性的好处是可以对可能存在的属性进行预定义，也可以捕获引用了不存在的属性时的错误

```js
interface SquareConifg {
    color?: string;
    width?: number;
}
function createSquare(config: SquraConfig) : {color: string; area: number} {
    let newSquare = {color: 'white', area: 100};
    if(config.color) {
        newSquare.color = config.color;
    }
    if(config.width) {
        newSquare.area = config.width * config.width;
    }
    return newSquare;
}
```

只读属性

```js
// Cannot assign to 'x' because it is a constant or a read-only property.
interface Point {
    readonly x: number;
    readonly y: number;
}

var p: Point = {
    x: 10,
    y: 10
}

p.x = 20;
```

只读数组
```js
let a: number[] = [1,2,3,4];
let a1: ReadonlyArray<number> = a;
a1.push(5); // error
```

接口描述函数类型

```js
interface SearchFunc {
    (source: string, subString: string) : boolean;
}

let search: SearchFunc;
// 参数的名称不必鱼接口定义的名字相匹配
search = function(source: string, subString: string) {
    let result = source.search(subString);
    return result > -1;
}
```

可索引类型
与使用接口描述函数类型差不多，我们也可以描述那些能够`通过索引得到`的类型

```js
interface StringArray {
    [index: number]: string
}
let myArr: StringArray;
myArr = ['aaa', 'bbb'];
let myStr: string = myArr[0];
```

当使用`number`类型的去索引时，`JavaScipt`会将它转换成`string`然后再索引对象

类类型接口实现

```js
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h:number, m:number) {}
}
```

类的静态部分和实例部分的区别

```js
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface {
    tick();
}
function creatClock(ctor: ClockConstructor, hour: number, minute: number) {
    return new ctor(hour, minute);
}
class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) {}
    tick() {}
}
class AnglogClock implements ClockInterface {
    constructor(h:number, m: number) {}
    tick() {}
}
let digital = creatClock(DigitalClock, 12, 17);
let analog = creatClock(AnglogClock, 2, 12);
```

接口扩展

```js
interface Shape {
    color: string;
}
interface Square extends Shape {
    sideLen: number;
}
```

混合类型
一个对象可以同时作为函数和对象使用，并且带有额外的属性

```js
interface Counter {
    (start: number): string;
    interval: number;
    reset():void;
}
function getCounter(): Counter {
    let count = <Counter> function(start: number) {};
    count.interval = 123;
    count.reset = function(){}
    return count;
}
```

### 类

```js
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return 'hello + ' + this.greeting;
    }
}
```

继承

```js
class Animal {
    name: string;
    constructor(thename: string) {
        this.name = thename;
    }
    move(distanceInMeter: number = 0) {
        console.log(`${this.name} moved ${distanceInMeter}m`);
    }
}
class Snake extends Animal {
    constructor(name: string) {
        super(name);
    }
    move(distanceInMeter = 5) {
        console.log('Slithering...');
        super.move(distanceInMeter);
    }
}
let sam = new Snake('Sammy the python');
```

共有、私有与受保护的修饰符
默认`public`
可以自由的访问程序中定义的成员

`private`
当成员使用private修饰时，就不能在声明他的外部访问

```js
class Animal {
    private name: string;
    constructor(theName: string){
        this.name = theName;
    }
}
new Animal('Cat').name; // error
```

`protected`
不能在类的外部访问，但是可以被继承

`readonly`修饰符
将属性设置为只读的，只读属性必须在声明或者构造函数里被初始化

```js
class Animal {
    readonly name: string;
    constructor(theName: string){
        this.name = theName;
    }
}
let cat = new Animal('Cat');
cat.name = 'hhah'; // error
```

存取器

```js
let passcode = 'sercret passcode';
class Employee {
    private _fullname: string;

    get fullname(): string {
        return this._fullname;
    }

    set fullname(newName: string) {
        if(passcode && passcode == 'secret passcode') {
            this._fullname = newName;
        } else {
            console.log('Error: Unauthorized update of employee!')
        }
    }
}

let emploee = new Employee();
Employee.fullname = 'Bob Smith';
```

静态属性

```js
class Person {
    static name: 'lznism';
}
console.log(Person.name);
```

抽象类
抽象类作为其它派生类的基类使用，一般不会直接被实例化。不同于接口，抽象类可以包含成员的实现细节

```js

```






























