location既是window对象的属性也是document对象的属性
- window.location
- document.location

location属性列举
- hash
- host
- hostname
- href
- pathname
- port
- protocol
- search

查询字符串参数
```js
function getQueryStringArgs(){
    var qs = location.search.length > 0 ? location.search.substring(1) : '';
    var args = {}, // 保存数据的对象
        items = qs.length ? qs.spilt('&') : [],
        item = null,
        name = null,
        value= null；
    for(var i=0; i<items.length;i++){
        item = items[i].split['='];
        name = decodeURIComponent(item[0]);
        value = decodeURIComponent(item[1]);

        if(name.length){
            args[name] = value;
        }
        return args;
    }
}
```

#### 位置操作
`location.assign()`改变浏览器位置的最常用的方法，接收一个URL作为参数，类似的还有`window.location`和`location.href`，上面三种方法都会更改浏览器的地址，并且会在历史记录中生成一条新的记录

使用`location.replace()`不会生成一条新历史纪录

重新加载浏览器
- location.reload()     // 重新加载，但可能从缓存中加载
- location.reload(true) // 直接从服务器端重新加载