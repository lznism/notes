为什么webpack的性能需要优化
- 代码全量构建速度过慢，即使很小的改动，也要等很长时间才能看到新的编译结果
- 随着项目业务复杂度的增加，工程模块体积也会急剧增大，构建后的模块通常要以M为单位
- 多个项目之间的共用基础资源存在重复打包
- node的单进程实现在耗CPU计算型loader中表现不佳

### 方案一 合理配置CommonsChunkPlugin
当多`entry`共存时，`CommonsChunkPlugin`的作用就发挥出来，对所有依赖的chunk进行公共部分的提取，会以`module`为单位

通常的提取方式有以下四种
#### 传入字符串参数，由chunkplugin自动计算提取
```js
new webpack.optimize.CommonsChunkPlugin('commons.js');
```

#### 有选择的提取公共代码
```js
// 只提取entry1, entry2中的公共代码
new webpack.optimize.CommonsChunkPlugin('commons.js', ['entry1', 'entry2']);
```

#### 将entry下所有的模块公共部分提取到一个通用的chunk中
```js
new webpack.optimize.CommonsChunkPlugin({
    name: 'vendors',
    minChunks: function(module, count) {
        return (
            module.resource &&
            /\.js$/.test(module.resource) &&
            module.resource.indexOf(path.join(__dirname, '../node_modules')) === 0
        )
    }
})
```

#### 抽取entry中的lib到vendors中
```js
entry = {
    vendors: ['fetch', 'loadsh']
};
new webpack.optimize.CommonsChunkPlugin({
    name: 'vendors',
    minChunks: Infinity
})
```

### 方案二 通过externals配置类提取常用库
在实际项目开发过程中，我们并不需要实时调试各种库的源码，这个时候就可以考虑`external`选项了
简单来说external就是把我们的依赖资源声明为一个外部依赖，然后通过script外链脚本引入
只是通过配置后可以告知webapck遇到此类变量名时就可以不用解析和编译至模块的内部文件中
而改用从外部变量中读取，这样能极大的提升编译速度，同时也能更好的利用CDN来实现缓存。

```html
<head>
<script src="//cdn.bootcss.com/jquery.min.js"></script>
<script src="//cdn.bootcss.com/underscore.min.js"></script>
<script src="/static/common/react.min.js"></script>
<script src="/static/common/react-dom.js"></script>
<script src="/static/common/react-router.js"></script>
<script src="/static/common/immutable.js"></script>
</head>
```

此类文件在配置之前，构建这些资源包时需要采用`amd/commonjs/cmd`相关模块化进行兼容封装，即打包好的库已经是`umd`模式包装过的，这样我们才能使用`import * from '**'`;
首先需要在config中加入

```js
externals: {
    'react-router': {
        amd: 'react-router',
        root: 'ReactRouter',
        commonjs: 'react-router',
        commonjs2: 'react-router'
    },
    'react': {
        amd: 'react',
        root: 'React',
        commonjs: 'react',
        commonjs2: 'react'
    }
}
```

然后在`output`中加入

```js
output: {
    libraryTarget: 'umd'
}
```

### 方案三 利用DllPlugin 和 DllReferencePlugin预编译资源模块
我们的项目依赖中通常会引用大量的npm包，而这些包在正常的开发过程中并不会进行修改，但是在每一次构建过程中却需要反复的将其解析，如何来规避此类损耗呢？这两个插件就是干这个用的。

`DllPlugin`就是预先编译一些模块
`DllReferencePlugin` 就是把这些预先编译好的模块引用起来

相对于`externals`，`DllPlugin`有以下优势
- dll预编译出来的模块可以作为静态资源链接库可被重复使用，尤其适合多个项目之间的资源共享，如同一个站点pc和手机版等；
- dll资源能有效地解决资源循环依赖的问题
- 由于externals的配置项需要对每个依赖库进行逐个定制，所以每次增加一个组件都需要手动修改，略微繁琐，而通过dllPlugin则能完全通过配置读取，减少维护的成本































