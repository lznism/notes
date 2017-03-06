事件绑定时获取事件对象

```js
@Component({
    selector: 'my-app',
    template: `
        <input type="text" (keyup)="onKey($event)" />
        <p>{{values}}</p>
    `
})
export class AppComponent {
    values: string;
    constructor() {
        this.values = '';
    }
    onKey(e:any) {
        this.values = e.target.value;
        console.log(e.key);
    }
}
```

`$event`对象的属性取决于DOM事件类型。例如，鼠标事件与输入框事件包含了不同的信息
`$event.key`表示哪个按键

但是如果将`$event`的类型设置为`any`。这样简化代码，但是有成本。没有任何类型信息能够揭示事件对象的属性，防止简单的错误

```js
export class AppComponent {
    values: string;
    constructor() {
        this.values = '';
    }
    onKey(e:KeyboardEvent) {
        this.values = (<HTMLInputElement>e.target).value;
        console.log(e.key);
    }
}
```

但是传入$event是靠不住的
类型化事件对象揭露了重要的一点，即反对把整个DOM事件传到方法中，因为这样组件会知道太多的模板信息。只有当它知道更多它本不应该了解的HTML实现细节时，他才能提取信息。这就违反了模板和组件之间分离的原则

### 从一个模板引用变量中获取用户输入
使用angular的`模板引用变量`。这些变量提供了从模块中直接访问元素的能力
在标识符前加上`#`就能声明一个模板变量引用

```js
@Component({
    selector: 'my-app',
    template: `
        <input type="text" #box (keyup)="0" />
        <p>{{box.value}}</p>
    `
})
export class AppComponent {}
```

上面的代码中需要注意的是必须绑定一个事件`(keyup)="0"`

用变量来获取用户输入

```js
@Component({
    selector: 'my-app',
    template: `
        <input type="text" #box (keyup)="onKey(box.value)" />
        <p>{{values}}</p>
    `
})
export class AppComponent {
    values: string;
    constructor() {
        this.values = '';
    }
    onKey(val: string) {
        this.values = val;
    }
}

```

`keyup.enter`只有当用户敲击回车键时，angular才会调用事件处理器

### 失去焦点事件(blur)

```js
class Hero {
    constructor(public name: string) {}
}
@Component({
    selector: 'my-app',
    template: `
        <h1>{{title}}</h1>
        <input #box 
            (blur)="handleBlur(box.value); box.value=''" 
            (keyup.enter)="handleBlur(box.value); box.value=''"/>
        <ul>
            <li *ngFor="let hero of heroes">{{hero.name}}</li>
        </ul>
    `
})
export class AppComponent {
    public title: string;
    public heroes: Hero[];
    constructor() {
        this.title = 'heroes';
        this.heroes = [
            new Hero('aaa'),
            new Hero('bbb'),
            new Hero('ccc'),
            new Hero('ddd')
        ];
    }
    handleBlur(val: string) {
        this.heroes.push( new Hero(val) );
    }
}
```


























