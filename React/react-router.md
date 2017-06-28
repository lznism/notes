### <BrowserRouter>

- basename:String 基准的url
- getUserConfirmation:Func 当导航需要确认时执行的函数
- forceRefresh:Bool 当设置为true是，在导航的过程中整个页面将会刷新
- keyLength:Number `location.key`的长度
- children:node 渲染单一的子组件

### <HashRouter>

- basename:String 基准的url
- getUserConfirmation:Func 当导航需要确认时执行的函数
- hashType:String `location.hash`使用的hash类型
- children:node 渲染单一的子组件

### <Link>

- to:String 跳转路径
- to:Object 需要跳转的地址
    + pathname
    + search
    + hash
    + state
- replace:Bool 当设置为true时，点击链接后将使用新地址替换掉访问历史记录里面的原有地址。默认是false

### <NavLink>
<Link>的一个特定版本，会在匹配上当前URL时给已渲染的元素添加样式

- activeClassName: String 匹配上当前路由时添加的class
- activeStyle: String 效果同activeClassName
- exact: Bool 只有地址完整匹配,class, style才会应用
- strict: Bool
- isActive: Func 添加用于确定链接是否活动的额外逻辑功能

### <Prompt>
当用户离开当前页面时做出提示

- message: String 用户尝试离开时，提示用户的消息
- message: Func 会与用户试图前往下一个地址（location） 和 action 一起被调用
- when: Bool 你可以随时渲染`<Prompt>`，而不是有条件的在警戒后渲染

### <Redirect>
渲染<Redirect>的时候会导航到一个新的地址(location)
- to: String
- to: Object
    + pathname
    + search
    + state
- push: Bool 当设置为true时，重定向将会把新地址加入访问历史记录里面，而不是替换掉当前的地址
- from: String 需要被重定向的地址

### <Route>

三大方法
- component 渲染的组件
- render 效果其实和component差不多,但是不能将component和render混用
- children 不管地址栏是不是匹配都会调用，都会渲染一些内容

三大props
- match 该对象包含了<Route path>如何与URL匹配的信息
    + params 路径参数
    + isExact 是否精确匹配
    + path 用来做匹配的路径格式
    + url URL的匹配部分，需要嵌套<Link>的时候调动
- location
- history

属性
- path
- exact
- strict 当设置为true是，有结尾斜线的路径只匹配有斜线的

### <Switch>
只匹配第一个Route或者Redirect

