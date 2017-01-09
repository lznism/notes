#### navigator相关属性
- userAgent     浏览器用户代理字符串
- vendor        浏览器的品牌
- plugins       浏览器中安装插件的相关数组

#### 检测插件
`navigator.plugins`返回的是一个数组，数组的每一项都包含下列属性
- name          插件的名字
- description   插件的描述
- filename      插件的文件名
- length        插件所处理的MIME类型数量
```js
function hasPlugin(name) {
    name = name.toLowerCase();
    for(var i=0; i<navigator.plugins.length; i++){
        if(navigator.plugins[i].name.toLowercase().indexOf(name) > -1){
            return true;
        }
    }
    return false;
}
```

#### 注册处理程序
让一个站点指明它可以处理特定类型的信息
- registerContentHandler()
- registerProtocolHandler()