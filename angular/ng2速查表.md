### import {platformBrowserDynamic} from '@angular/platform-browser-dynamic'
`platformBrowerDynamic().bootstrapModule(App)` 从指定的NgModule中使用跟组件引导应用

### import {NgModule} from '@angular/core'
```js
@NgModule({
    declarations: ..., 
    imports: ...,
    exports: ..., 
    providers: ..., 
    bootstrap: ...
})
```

- `declarations` 一个数组，包含从属于当前模块的组件，指令，管道
- `imports` 一个数组，包含被导入到当前模块中的所有模块
- `exports` 一个数组，该模块输出的课件组件、管道、指令
- `providers` 一个数组，对当前模块及子模块指令，管道可见的依赖注入提供商
- `bootstrap` 一个数组，包括有当前模块引导时应该引导的组件