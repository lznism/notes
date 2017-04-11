### webpack处理字体文件

```js
{
    test: /\.(woff|svf|ttf|eot)\??.*$/,
    loader: 'url?limit=819'
}
```

### webpack中的url-loader和file-loader
`url-loader`实际上是对`file-loader`的封装,直接使用`url-loader`即可

### html-webpack-plugin
- title 生成html文件的标题
- filename 生成的html文件的文件名
- inject `script`标签注入的位置
- minify 压缩html文件
- hash 给生成的js文件一个特定的`hash`值
- chunks 主要针对的是多入口(`entry`)文件，当你的文件有多个入口时，对应的就会生成多个编译后的js文件

```js
module.exports = {
    entry: {
        index: path.resolve(__dirname, 'src/index.js'),
        index1: path.resolve(__dirname, 'src/index1.js'),
        index2: path.resolve(__dirname, 'src/index2.js')
    },
    plugins: [
        new HtmlWebpackPlugin({
            chunks: ['index', 'index1']
        })
    ]
}
```
执行`webpack`命令之后就可以生成如下文件

```html
<script src="index.js"></script>
<script src="index1.js"></script>
```

### 实现按需加载
`code splitting`就是将这些`chunks`打断，然后对打包文件进行分割，实现按需加载

**第三方类库分割**
使用`CommonsChunkPlugin`插件
`CommonsChunkPlugin`提供两个参数，第一个参数是`chunk`名，第二个参数是文件名
进行第三方代码分割的前提是需要在`entry`中配置vendor，即第三个类库

```js
entry: {
    index: './index.html',
    vendor: ['jquery', 'bootstrap']
}

new webpack.optimize.CommonsChunkPlugin("vendor", "vendor.bundle.js");
```

这样处理处理之后再`index.js`中就只存在业务代码了

**按需加载**

```js
// 如果a文件依赖a.js和c.js
// 如果b文件依赖b.js和c.js
```
这种情况下就需要使用`按需加载`
加载的基本的函数

```js
require.ensure(dependencies, callback, chunkName);
```

因此我们需要给打包之后的文件命名

```js
output: {
    chunkFilename: '[name].[chunkhash:5].chunk.js'
}
```

一个简单的demo

```js
// a.js
console.log('a.js');
// b.js
console.log('b.js');
// c.js
console.log('c.js');
// entry
require.ensure([], () => {
    require('./a');
    require('./b');
}, 'chunk1');
if(condition) {
    require.ensure([], () => {
        require('./c')
    }, 'chunk2');
}
```

将会打包出3个文件，`基础包、chunk1和chunk2`，如果condition为false,那么chunk2虽然打包但是永远不会被加载

```js
const CourseRoute = {
    path: 'course/:courseId',
    getChildRoutes(location, callback) {
        require.ensure([], function(require) {
            callback(null, [
                require('./routes/Announcements'),
                require('./routes/Assignments'),
                require('./routes/Grades'),
            ])
        })
    },
    getIndexRoute(location, callback) {
        require.ensure([], function(require) {
            callback(null, require('./components/Index'))
        })
    },
    getComponents(location, callback) {
        require.ensure([], function(require) {
            callback(null, require('./components/Course'))
        })
    }
};
```

React更优雅的`code splitting`解决方案

```jsx
import React from 'react';
import {Route, IndexRedirect} from 'react-router';

const index = (location, callback) => {
    require.ensure([], require => {
        callback(null, require('components/Index').default);
    }, 'index');
};

const routes = <Route path="/" component={rootNode}>
    <IndexRedirect to="index" />
    <Route path="index" getComponnet={index} />
</Route>

export default routes;
```






























