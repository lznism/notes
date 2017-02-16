在`async`官方文档中，主要功能有三个部分：`流程控制`， `集合处理`，`工具`
常用的方法
### series(tasks, [callback])
多个任务依次执行，返回的结果严格按照`tasks`中的执行顺序

```js
async.series([
    function(callback) {
        callback(null, 1)
    },
    function(callback) {
        callback(null, 2)
    }
], function(err, results) {
    if(err) throw err;
    console.log(results);
})
```

### parallel(tasks, [callback])
多个任务并行执行，返回的结果没有顺序

```js
async.parallel({
    one: function(callback) {
        callback(null, 1);
    },
    two: function(callback) {
        callback(null, 2);
    }},
    function(err, results) {
        console.log(results);
    }
)
```

### waterfall(tasks, [callback])
多个任务依次执行，但是和`series`不同的是，他们之间可以有数据传递。并且`task`只支持数组形式的传入，不支持`Object`

```js
async.waterfall([
    function(callback) {
        callback(null, 'one', 'two');
    },
    function(args1, args2, callback) {
        // args1 === 'one'
        // args2 === 'two'
        callback(null, 'three');
    },
    function(args3, callback) {
        // args3 === 'three'
        callback(null, 'done');
    }], function(err, result) {
        // 此时result为最后一个回调结果done
    }
)
```

### whilst(test, fn, callback)
当test条件为`true`时，重复执行方法`fn`。执行完毕之后，`callback`结束循环

```js
let count = 0;
async.whilst(function() {
    return count > 5
}, function(callback) {
    count++;
    setTimeout(callback, 1000);
}, function(err) {
    // 5秒执行完毕
})
```

### auto(tasks, [callback])
根据传入的类型选择最佳的执行方式。不依赖其它任务的方法将并发执行，依赖其它任务的方法将在其执行完毕之后执行
`tasks`中传入的任务类型为`function`时将并发执行，为数组时，将等待相关方法完成后再执行

```js
async.auto({
    get_data: function(callback){
        console.log('in get_data');
         //一个取数据的方法
        // 与make_folder方法并行执行
        callback(null, 'data', 'converted to array');
    },
    make_folder: function(callback){
        console.log('in make_folder');
        // 一个创建文件夹的方法
        // 与make_folder方法并行执行
        callback(null, 'folder');
    },
    write_file: ['get_data', 'make_folder', function(results, callback){
        console.log('in write_file', JSON.stringify(results));
        // 此方法在等待get_data方法和make_folder执行完成后执行
        callback(null, 'filename');
    }],
    email_link: ['write_file', function(results, callback){
        console.log('in email_link', JSON.stringify(results));
        // 等待write_file执行完成后执行
        callback(null, {'file':results.write_file, 'email':'user@example.com'});
    }]
}, function(err, results) {
    console.log('err = ', err);
    console.log('results = ', results);
});
```


























