Angular-Cli 集成工具
ng generator
ng serve --prod --aot aot编译
ng build --prod --aot 编译


- @Component
- @View
- selector      组件选择器
- template      模板
- templateUrl   模板URL
- styles        样式
- styleUrl      样式URL
- properties    属性
- directives    指令
    
    如果要在模板中使用自定义指令（组件的一种指令），必须在`Component注解`的`directives`属性中提前声明


### 模块
angular应用是模块化的，并且`Angular`有自己的模块系统，被称为`Angular模块或NgModules`。
每一个angular应用至少包含一个根模块，习惯上命名为`AppModule`
大多数应用中可能会包含很多特性模块，每个模块都是一个内聚的代码块专注于某个应用领域，工作流或紧密相关的功能
模块都有一个`@NgModule`的装饰器，装饰器负责把元数据附加到类上，以了解那些类的设计意图以及他们应该如何工作
`@NgModule`的重要属性
- declarations 声明本模块中拥有的视图类，包括`组件`，`指令`，`管道`。不要将NgModule添加到这里面
- exports 可用于其它模块的组件模板
- imports 声明本组件模板需要的类所在的其它模块。只能包含NgModule类。不要放置其他类型的类。
- providers 引入服务
- bootstrap 指定应用的主视图，只有根模板才能设置`bootstrap`属性，将组件插入到DOM中

```js
import {NgModule} from '@angular/core';
import {BrowserModule} from '@angular/platform-browser';
@NgModule({
    imports: [BrowserModule],
    providers: [Logger],
    declarations: [AppComponent],
    exports: [AppComponent],
    bootstrap: [AppComponent]
})
export class AppModule { }
```

#### imports数组
Angular模块把特性合并成离散单元的一种方式
- HttpModule
- RouterModule
- BrowserModule
- 自定义模块

不要将js中的`import`和angular中的`imports`混淆。`imports`数组告诉angular特定的angular模块信息。使用`@NgModule`装饰的类-应用需要他们来正常工作

#### declarations数组
每个组件必须在且仅在一个`NgModule`类中声明。通过将其列到`AppModule`模块的`declarations`数组中，告诉angular哪个组件属于`AppModule`。在创建的过程中，逐步将它们添加到`declarations`中

#### bootstrap数组
通过引导根AppModule来启动应用。在启动过程中，其中一步是创建列在`bootstrap`数组中的组件，并将它们每一个都插入到浏览器的DOM中
每个引导的组件都是自己的组件树的根，插入一个被引导的组件通常触发一系列组件的创建并形成组件树

#### 在main.ts中引导
引导的方法很多，取决于你想如何编译应用以及应用将在哪儿运行
开始时，你讲使用即时(JIT)动态编译应用。然后在浏览器中运行它

```js
import {platformBrowserDynamic} from '@angular/platform-brower-dynamic';
import {AppModule} from './app/app.module';
platformBrowserDynamic().bootstrapModule(AppModule);
```

### Angular模块库
每个Angular库的名字都带有`@angular`前缀
可以像下面这样引入模块

```js
import {Component} from '@angular/core';
import {BroswerModule} from '@angular/platform-browser';
```

要使用上面的模块就必须引入，放入`imports`中

```js
imports:    [BrowerModule]
```

### 组件
负责控制屏幕上一小块区域，我们称之为视图
angular会创建，更新，销毁组件。应用可以通过生命周期钩子在组件生命周期的各个时间点上插入自己的操作

### 模板
我们可以通过组件自带的模板来定义组件视图。模板以HTML形式存在，告诉Angular如何渲染组件
模板除了使用HTML标签之外，还能使用其他的元素，例如`*ngFor, {{}} (click) [hero] <hero-detail>`
其中`<hero-detail>`就是一个用来表示新组件`HeroDetailComponent`的自定义元素

### 元数据
在`Typescript`中我们使用装饰器来附加元数据

```js
@Component({
    moduleId: module.id,
    selector: 'hero-list',
    templateUrl: './hero-list.component.html',
    providers: [HeroService]
})
export class HeroListComponent implements OnInit {}
```

`@Component`的配置项包括
- moduleId 为模块相关的url提供基地址
- selector 告诉angular在父级HTML中查找相关标签，创建并插入该组件
- templateUrl
- providers 为组件注入服务。这就是在告诉angular该组件的构造函数需要一个`HeroService`服务，这样组件就可以从服务中获取数据

Angular支持数据绑定，一种让模板各部分与组件的各部分相互合作的机制。我们往模板的HTML中添加绑定标记，来告诉angular如何把两者联系起来
数据绑定的四种语法
- {{value}}
- [property]='value' 将父级的值传递到子组件中
- (event)='handler'
- [(ng-model)]='property' 双向数据绑定

三种表现形式

```html
<li>{{name}}</li>
<hero-detail [hero]='selectedHero'></hero-detail>
<li (click)='selectHero(hero)'></li>
<input [(ngModel)]='name'>
```

### 指令
angular模块是动态的。当angular渲染它们时，他会根据指定提供的操作对DOM进行转换
`结构型的指令`通过在DOM中添加移除和替换元素来修改布局

```html
<li *ngFor="let hero of heroes">{{hero.name}}</li>
<hero-detail *ngIf='selectedHero'></hero-detail>
```

`属性型指定`修改一个现有的外观行为。在模板中他们看起来像标准的HTML属性

```html
<input type="text" [(ngModel)]="hero.name">
```

### 服务
组件类应保持精简。组件本身不从服务器获得数据，不进行验证输入，也不直接往控制台写日志。他们把这些任务委托给服务
组件的任务就是提供用户体验，仅此而已
服务让我们能轻易地把应用逻辑拆分到服务，并通过依赖注入来在组件中使用这些服务
组件是服务最大的消费者

```js
// 下面是一个简单的服务示例
export class Logger{
    log(msg: any) { console.log(msg) }
    error(msg: any) { console.error(msg) }
    warn(msg: any) { console.warn(msg)}
}
```

### 依赖注入
是提供类的新实例的一种方式，还负责处理好类所需的全部依赖。大多数依赖都是服务。angular使用依赖注入来提供新组件以及组件所需要的服务
当angular创建组件时，会首先为组件所需的服务请求一个注入器`injector`
在`typescript`中，可以使用构造器来实例化一个服务

```js
constructor(private service: HeroService) {}
```

要使用服务，必须将服务声明在`providers`中，可以将providers写到根模块或者组件中

### 用户输入
要获取用户的输入，angular推荐的一种方式就是`使用模板引用变量`。这种变量提供了直接访问元素的能力

```html
<input #box (keyup)="0" />
<p>{{box.value}}</p>
```