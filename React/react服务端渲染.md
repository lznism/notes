两个用于服务端渲染的API
- renderToString 将react元素转成一个HTML字符串并在服务端标识reactid。等到浏览器端再次渲染时,React做的只是事件绑定等前端工作。而不会再渲染整个界面
- renderToStaticMarkup 精简版的`renderToString`，没有reactid

在服务端，使用模板是为了做浏览器同步HTML请求。因此简单地说，同步页面的请求只需要有渲染HTML文本的功能就行了

`Koa`官方维护了一个`react-view`的插件。(其实就相当于jade/ejs之类的模板引擎)
- 配置

```js
const defaultOptions = {
    doctype: '<!DOCTYPE html>',
    beautify: false,
    extname: 'jsx',
    cache: process.env.NODE_ENV === 'production',
    writeResp: true,
    views: path.join(__dirname, 'views'),
    internals: false
}
```

- 渲染
- cache
- babel