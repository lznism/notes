### 插值表达式
这个跟ng1中的`{{}}`很像，大括号内部可以防止任意有效的`js表达式`，将js表达式的结果`求值之后`再将结果输出出来

```html
<p>the sum of 1 + 1 is {{1 + 1}}</p>
<img src="{{url}}" />
```

插值表达式中可以调用宿主组件中的方法

```html
<p>the sum of 1 + 1 is {{getValue()}}</p>
```

### 模板表达式
模板表达式产生一个值。`Angular`执行这个表达式，并把它赋值给绑定目标的属性，这个绑定目标可能是`HTML元素，组件，指令`。类似于`[property] = "express"`

模板表达式不能引用全局命名空间中任何东西。不能引用`window`和`document`，不能调用`console.log`或`Math.max`。他们只能访问来自表达式上下文中的成员

```js
// isUnChanged 就是引用的上下文中的isUnChanged属性
[disabled] = "isUnChanged";
```

表达式需要遵循以下准则
- 没有可见的副作用 除了目标属性的属性值意外，不改变应用的任何状态
- 执行迅速 不应该包含大量的计算
- 非常简单 
- 幂等性 返回的东西完全相同，直到某个依赖发生变化

### 模板语句
类似于`(event)="onSave()"`
`onSave()`就是数据绑定组件实例中的方法，事件中事件对象是通过`$event`来传递的
语句的上下文可以引用模板自身上下文中的属性。比如`$event`，或者组件实例中的属性方法...

### 绑定语法
根据数据流的方向可以将绑定分为三类

- 单向，从数据源到视图目标
    + {{expression}}
    + [target] = 'expression'
    + bind-target = 'expression'

- 单向，从视图到数据源
    + (target) = 'statement'
    + on-target = 'statement'

- 双向
    + [(target)] = 'expression'
    + bindon-target = 'expression'

模板绑定是通过`DOM property`和事件来工作的，而不是`attribute`
在angular的世界里，attribute唯一的作用就是用来初始化元素和指令的状态。当进行数据绑定时，只是在与元素和指令的property和事件打交道，而attribute就完全靠边站了

### 绑定目标
数据绑定目标是DOM中的某些东西。这个目标可能是`元素|组件|指令 property`,也可能是`元素|组件|指定 事件`

- property

```html
    <!-- 属性绑定 将属性设置为组件属性的值，例如下面img的src属性 -->
    <img [src] = 'heroImageUrl' />
    <!-- 设置自定义组件的模型属性，这个是父子组件通讯的重要途径 -->
    <hero-detail [hero] = 'currentHero'></hero-detail>
    <div [ngClass] = '{selected: isSelected}'></div>
```

- event

```html
<button (click) = 'onSave()'>Save</button>
<hero-detail (deleteRequest) = 'deleteHero()'></hero-detail>
<!-- myClick是一个指定事件 -->
<div (myClick) = 'clicked = $event'>click me</div>
```

- 双向

```html
<input type="text" [(ngModel)] = 'heroName' />
```

- attribute

```html
<button [attr.arial-label] = 'help'>help</button>
```

- css 

```html
<!-- 给类名加上class前缀 -->
<!-- 给DIV添加一个special的类(在isSpecial为true的情况下) -->
<div [class.specail] = 'isSpecial'>special</div>
```

- style

```html
<!-- 给样式名加上style的前缀 -->
<button [style.color] = "isSpecial ? 'red' : 'green'">button</button>
<!-- 可以通过下列方法来设置字体大小的单位 -->
<button [style.font-size.em] = 'isSpecial ? 3 : 1'>button</button>
```

### 一次性字符串初始化
当下列条件满足时，应该省略中括号
- 目标属性接受字符串值
- 字符串是个固定值
- 这个值永远不可改变

当没有元素属性可以绑定时，就必须使用到`attribute`，没有对应的属性可以绑定

```html
<tr><td colspan="{{1 + 1}}">Three-Four</td></tr>
```

上面的这段表达式就会报错，因为`td`元素没有`colspan`属性。但是插值表达式和属性表达式只能设置属性，不能设置`attribute`

这时我们可以采取以下做法，加上`attr`前缀

```html
<tr><td [attr.colspan] = '1+1'>One-Two</td></tr>
```

通过class来绑定CSS样式类，下面的代码中如果isSpecial的值为真，那就会绑定special这个类

```html
<div [class.special]="isSpecial"></div>
```

同理样式绑定通过`[style.color]`进行绑定

### `$event`和事件处理语句
绑定会通过`$event`的事件对象传递关于此事件的信息(包括数据值)

```html
<input [value] = 'currentHero.firstname' (input) = 'currentHero.firstname = $event.target.value'/>
```

### 使用EventEmitter实现自定义事件
指令创建一个`EventEmitter`实例，并且把它作为属性暴露出来。指定调用`EventEmitter.emit(payload)`来触发事件

### 使用NgModel进行双向数据绑定

```html
<input type="text" [(ngModel)] = 'currentHero.name'>
```

在使用`ngModel`做数据绑定时，得先导入`FormsModule`

```html
<hero-detail [hero]="currentHero" (deleteRequest)="deleteHero($event)"></hero-detail>
```

上面的代码中hero是属性绑定的目标，deleteRequest是事件绑定的目标
在HeroDetailComponent内部，这些属性被装饰器标记成了输入和输出的属性

```js
@Component({
    inputs: ['hero'],
    outputs: ['deleteRequest'],
})
export class HeroDetailComponent {
    @Input() hero: Hero;
    @Output() deleteRequest = new EventEmitter<Hero>();
}
```

### 内置指定

1. ngClass

```js
@Component({
    selector: 'app-root',
    template: `
        <div [ngClass] = 'currentClasses'>This div is initially saveable, unchanged, and special</div>
    `
})
class Test {
    currentClasses: {};
    setCurrentClasses() {
        this.currentClasses = {
            savable: this.canSave,
            modified: !this.isUnChanged,
            special: this.isSpecial
        };
    }
}
```

2. ngStyle

```js
@Component({
    selector: 'app-root',
    template: `
        <div [ngStyle] = 'currentStyle'>This div is initially saveable, unchanged, and special</div>
    `
})
class Test {
    currentStyles: {};
    setCurrentStyles() {
        this.currentStyles = {
            // CSS styles: set per current state of component properties
            'font-style':  this.canSave      ? 'italic' : 'normal',
            'font-weight': !this.isUnchanged ? 'bold'   : 'normal',
            'font-size':   this.isSpecial    ? '24px'   : '12px'
        };
    }
}
```


3. ngIf
如果`*ngIf`表达式中的值为`false`，那么会从DOM中移除元素子树

```html
<div *ngIf="currentHero">Hello, {{currentHero.firstname}}</div>
```

`ngIf`和`[class.hidden]`是不同的, `[class.hidden]`和`[class.display]`并不会将其从DOM树中移除

```html
<div [class.hidden] = 'isSpecial'>show with class</div>
```

4. 当需要从一组可能的元素树中根据条件显示一个

```html
<span [ngSwitch]='toeChoice'>
    <span *ngSwitchCase = "'Eenie'">Eenie</span>
    <span *ngSwitchCase = "'Meanie'">Meanie</span>
    <span *ngSwitchCase = "'Miney'">Miney</span>
    <span *ngSwitchDefault>other</span>
</span>
```

5. ngFor

```html
<span *ngFor="let hero of heroes">{{heroes.fullname}}</span>
```

带索引的ngFor

```
<div *ngFor="let hero of heroes; let i=index;">{{i+1}} - {{hero.fullname}}</div>
```

创建模板引用变量

```html
<input type="text" #box>
<button (click)="callPhone(box.value)">button</button>
```

#### NgForTrackBy
避免无脑渲染所有的DOM,可以构造一个追踪函数，比如有一万个`id`唯一的用户，id相同的是同一个人
如果从服务器中请求这个列表，请求之后默认的是全部渲染所有的用户，而我们现在不想让那些原有的用户重新渲染，增加性能压力

```html
<div *ngFor="let user of users; trackBy: trackByHeroes">{{user.name}}</div>
```

```js
trackByHeroes(id: number, user: User) {
    return user.id;
}
```

#### 安全导航操作符和空属性路径
下面的代码中如果没有加上`?`cerrentHero的值是undefined,会抛出一个错误，但是如果加上了`?`就不会抛出

```html
the current hero's name is {{currentHero?.name}}
```
