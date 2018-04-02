指令和组件的实例有一个生命周期：新建、更新和销毁

每个接口都有唯一一个钩子方法，只有指令、组件中定义过的那些钩子方法才会被调用

- ngOnChanges() 当angular重新设置数据绑定输入属性时的响应
- ngOnInit() 当angular第一次显示数据绑定和设置指令组件的输入属性之后
- ngDoCheck() 在每个angular变更检测周期中调用
- ngAfterContentInit() 当把内容投影进组件之后调用
- ngAfterContentChecked() 每次完成投影组件内容的变更检测后调用
- ngAfterViewInit() 初始化组件视图及其子视图之后调用
- ngAfterViewChecked() 组件视图和子视图的变更检测之后调用
- ngOnDestory() angular每次销毁指令和组件之前调用并清扫

ngOnInit()是组件获取初始数据的好地方，在指令的构造函数完成之前，那些被绑定的输入属性都还没有值。如果我们需要基于这些值来完成指令的初始化，这种情况就会出问题

一旦检测到组件或者指令的`输入属性`发生变化，angular就会调用`ngOnChanges()`方法

`DoCheck()`钩子用来检测angular自身无法捕获的变更并采取行动

`AfterView`钩子关心的是`ViewChildren`，这些子组件的元素标签会出现在组件的模板里


`AfterContent`钩子关心的是`ContentChildren`这些子组件被angular投影进该组件中