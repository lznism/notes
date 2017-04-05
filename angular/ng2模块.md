很多angular库都是模块，比如说我们熟知的`FormsModule`, `HttpModule`, `RouterModule`
angular将组建，指令管道打包成内聚的功能块，每个模块聚焦于特性区域，业务领域等等
模块可以主动加载，也可以由路由器进行异步惰性加载

### NgModule
angular模块是由一个`@NgModule`装饰器提供元数据的类，元数据包括
- declarations
- exports
- imports
- providers
- bootstrap

我们可以随着业务的增在，从根模块中重构出一些特性模块，来代表一组相关功能集合，然后再根模块中导入
`@NgModule.bootstrap`属性把这个AppComponent标记为引导组件，当angular引导应用时，它会在DOM中渲染AppComponent。

在`main.ts`文件中，我们通过引导`AppModule`来启动应用
两种引导方式
- JiT动态引导
- AOT静态引导 此种方案可以生成更小、启动更快的应用，建议优先使用，由于整个应用都是预编译的，所以我们不用吧`angular编译器`一起发布到浏览器中，也不用在浏览器中变异

无论是JiT还是AOT都会从同一份`AppModule`源码中生成一个`AppModuleNgFactory`类。JIT编译器动态的在内存中创建这个工厂类。而AOT时把工厂输出成一个物理文件

### 声明指令和组件
下面是一个属性型指令的例子

```js
import { Directive, ElementRef } from '@angular/core';
@Directive({
	selector: '[highlight]'
})
export class HighlightDirective {
	constructor(el: ElementRef){
		el.nativeElement.style.backgroundColor = 'gold';
	}
}
```
要想这个高亮指令可以在组件中顺利使用，必须声明，方法就是采用前面提到的`declarations`

### 服务提供商
根模块是为所有组件提供服务的最佳途径，一个简单版的服务类似于下面的例子

```js
import { Injectable } from '@angular/core';
@Injectable()
export class UserService {
	name="xxx";
}
```
在声明了服务之后我们如何使用这个服务呢，typescript为我们提供了非常方便的构造器实例化服务的方式

```js
import { Component, Input } from '@angular/core';
import { UserService } from './user.service';
@Component({
	moduleId: module.id,
	selector: 'app-title',
	templateUrl: './title.component.html'
})
export class TitleComponent {
	@Input() subtitle ='';
	constructor(private userservice: UserService) {}
}
```

最后我们需要在根模块中的`providers`中注入这个服务
如果有两个同名指令都叫做`HightlightDirective`，那么我们只要在import时使用as关键字来为第二个指令创建别名即可

```js
import {HightlightDirective as ContactHighlightDirective} from './concat/highlight.directive';
```

### 构建特性模块解决指令冲突
特性模块是带有`@NgModule`装饰器及其元数据的类，就行根模块一样。特性模块的元数据和根模块的元数据的属性是一样的
- 我们通过引导根模块来启动应用，通过导入特性模块来扩展应用
- 特性模块可以对其他模块暴露或隐藏自己的实现

使用惰性加载语法告诉路由器要到哪里去找这些模块

```js
{path: 'crisis', loadChildren: 'app/crisis.module#CrisisModule'},
{path: 'heroes', loadChildren: 'app/hero/hero.module#HeroModule'}
```
在特性模块中路由应该这样写`RouterModule.forChild`
当我们需要为根模块和特性模块分别提供不同的导入值时，forRoot和forChild时约定的方法名