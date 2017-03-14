首先来看一个脆弱、缺乏弹性的类

```js
export class Car {
    public engine: Engine;
    public tires: Tires;
    public description = 'NO DI';
    constructor() {
        this.engine = new Engine();
        this.tires = new Tires();
    }
    drive() {
        return `${this.description} car with ${this.engine.cylinders} cylinders and ${this.tires.make} tires.`;
    }
}
```

`Car`类需要一个引擎和轮胎，他没有去请求现成的实例，而是在构造函数中用具体的`Engine`和`Tires`类实例化出自己的副本

显然，这个时候如果`Engine`或者`Tires`升级了，要求构造函数多传入一个参数，该怎么办？我们也许首先想到的就是在`Car`类中重写代码，但是这样是不可取的，代码的耦合度过高。这个会让`Car`类过于脆弱，受制于`Engine`类的影响
现在又有另外一个需求，我们需要更换一个`Tires`类的实例怎么办？我们的`Car`类是完全锁定在`Tires`类上面的。这里我们有需要修改`Car`类,这使得`Car`类缺乏弹性
当给`Car`类写测试时，我们被它那些隐藏的依赖所摆布。需要在测试环境中创建`Engine`类，但是如果`Engine`类又依赖于别的类呢，这样就会一层一层的追加下去。

那么我们如何使`Car`类变得健壮，有弹性可测试呢？那我们就需要用到`Angular 2`的依赖注入
其实这个也是`typescript`构造器的语法来同事定义参数和属性

```js
class Car {
    public description = 'DI';
    constructor(
        public engine: Engine,
        public tires: Tires
    ){}
}
```

这里的`Car`类不在创建`Engine`和`Tires`的实例，而仅仅是消费他们
这里我们就可以通过在构造函数中传入参数来新建一个`Car`类

```js
let car = new Car(new Engine(), new Tires());
```

如果有人扩展了`Engine`类，那就不再是`Car`类的烦恼了

用一个服务来把获取英雄数据的代码封装起来

```js
import {Injectable} from '@angular/core';
import {HEROES} from './mock-heroes';
@Injectable()
export class HeroService{
    getHeroes() { return HEROES }
}
```

服务只是`Angular`中的一个类。有`Angular`注入器注册它之前，没有任何特别之处

我们可以在NgModule中注册`provider`

```js
@NgModule({
    providers: [UserService, LoginService]
})
```

我们也可以在组件中注册`provider`

```js
@Component({
    prividers: [HeroService]
})
```

那么我们如何分辨`privider`是注册到组件还是注册到`NgModule`，其实这里的区别很明显，注册到`NgModule`的`provider`可以被整个应用访问；注册到`Component`的`provider`可以被该组件及其子组件使用

现在我们就可以使用依赖注入模式的要求，组件必须在构造函数中请求这些服务

```js
class HeroListComponent {
    heroes: Hero[];
    constructor(heroService: HeroService) {
        this.heroes = heroService.getHeroes();
    }
}
```

这里构造函数的参数是`HeroService`，并且`HeroListComponent`类有一个`@Component`的装饰器。这里是通过父组件`HeroesComponent`有`HeroService`的`providers`信息

在一个注入器的范围内，依赖都是单例的。这个例子中，`HeroesComponent`和`HeroListComponent`共享一个`HeroService`实例

那么当服务需要别的服务时怎么办

```js
import {Injectable} from '@angular/core';
import {HEROES} from './mock-heroes';
import {Logger} from '../logger.service';
@injectable()
export class HeroService {
    constrcutor(private logger: Logger) {}
    getHeroes() {
        this.logger.log('getting heroes...');
        return HEROES
    }
}
```

我们需要在构造器中注入一个`Logger`的实例，并把它存到`logger`的私有属性中

`@injectable()`标识一个类可以被注入器实例化。通常，在试图实例化没有标识为`@injectable()`的类时，注入器会报错

把日志服务注入到`HeroService`中需要两步
- 创建日志服务
- 把它注册到应用中

```js
import {Injectable} from '@angular/core';
@Injectable()
export class Logger {
    logs: string[] = [];
    log(message: string) {
        this.logs.push(message);
        console.log(message);
    }
}
```

应用的每个角落可能都需要用到日志服务，所以把它放到`app`目录。并且在应用模块`AppModule`的元数据`providers`数组中注册它

```js
providers: [Logger];
```

其实`provider`是一个拥有两个属性的对象

```js
[{provider: Logger, useClass: Logger}]
```

第一个是令牌，用于定位依赖值和注册`provider`
第二个是provider定义对象。可以将其看作是指导如何创建依赖值的配方

带有依赖的provider
假设`BetterLogger`可以在日志消息中显示用户名。这个日志服务从注入`UserService`中取得用户，`UserService`通常也会在应用级注入

```js
@Injectable()
class BetterLogger extends Logger {
    constructor(private userService: UserService) {
        super();
    }
    log(message: string) {
        let name = this.userService.user.name;
        super.log(`Message to ${name}: ${message}`);
    }
}
```


有时提供一个预先做好的对象回避请求注入器从类中创建它更容易

```js
let silentLogger = {
    logs: ['Silent logger says "Shhhhh!". Provided via "useValue"'],
    log: () => {}
}
```

注册`provider`时我们可以用`useValue`的方式来注册

```js
[{provider: Logger, useValue: silentLogger}]
```
