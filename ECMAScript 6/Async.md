### 异步
所谓异步简单说就是一个任务分成两个阶段，先执行第一段，然后转而执行其他任务，等做好了准备再回过头执行第二段
JavaScript语言对异步编程的实现就是`回调函数`。所谓回调函数就是把任务的第二段单独写在一个函数中，等到重新执行这个任务时，就直接调用这个函数。
```js
fs.readFile('test.txt', function(err, data){
    if(err) throw err;
    console.log(data);
});
```

### Promise
允许将回调函数的嵌套，改成链式调用。采用Promise，连续读取多个文件
```js
var readFile = require('fs-readfile-promise');
readFile(fileA)
    .then(data => console.log(data.toString))
    .then(() => readFile(fileB))
    .then(data => console.log(data.toString()))
    .catch(err => console.log(err));
```

### Generator
```js
function* asyncjob(){
    // 执行到此处执行权将交给其他协程
    var f = yield readFile(fileA);
}
```
使用Generator函数，执行一个真实的异步任务
```js
var fetch = require('node-fetch');
function* gen(){
    var url = 'https://api.github.com/users/github';
    var result = yield fetch(url);
    console.log(result.bio);
}
var g = gen();
var result = g.next();

result.value
    .then(data => return data.json())
    .then(data => g.next(data));
```
上面的代码首先执行Generator函数，获取遍历器对象；然后使用next方法，执行异步任务的第一阶段

### Thunk函数
`Javascript`语言是传值调用，它的Thunk函数含义有所不同。Thunk函数替换的不是表达式，而是多参数函数，将其替换成单参数函数。
```js
// 正常版本的readFile
fs.readFile(fileName, callback);
// Thunk版本的readFile
var readFileThunk = Thunk(fileName);
readFileThunk(callback);
var Thunk = function(fileName){
    return function(callback){
        return fs.readFile(fileName, callback);
    }
}
```

### Generator函数的流程管理
Generator与Thunk合作，可以做成异步操作的自动化管理
```js
var fs = require('fs');
var thunkify = require('thunkify');
var readFile = thunkify(fs.readFile);

var gen = function* (){
    // yield用于将程序的执行权移出Generator函数
    var r1 = yield readFile('fileA');
    console.log(r1.toString());
    var r2 = yield readFile('fileB');
    console.log(r2.toString());
}

var g = gen();
var r1 = g.next();
r1.value((err, data) => {
    if(err) throw err;
    var r2 = g.next(data);
    r2.value((err, data) => {
        if(err) throw err;
        g.next(data);
    })
})
```

### Thunk函数的自动流程管理
```js
function run(fn){
    var gen = fn();

    function next(err, data){
        var result = gen.next(data);
        if(result.done) return;
        result.value(next);
    }
    next();
}
function* gen(){
    // code
}
run(gen);
```
上面的`run`函数就是一个Generator函数的自动执行器。内部的next()函数就是Thunk的回调函数