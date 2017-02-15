### 创建一个连接

```js
var mysql = require('mysql');
var connection = mysql.createConnection({
    host: 'localhost',
    user: 'root',
    password: '123456',
    database: 'lznism'
});
connection.connect();
```

其中`connect()`方法可以接受一个函数，如果连接过程中发生错误就会调用这个函数。

### 进行查询

```js
connection.query(SQL, function(err, rows, fields) {

});
```

`query()`方法可以接受两个参数，第一个参数是要查询的`sql语句`，第二个参数是回调函数。
也可以在查询中使用占位符

```js
connection.query('update users set foo=?, bar=?, baz=? where id=?', ['a', 'b', 'c', userId], function(){
    // 使用这种方法的好处是可以自动转义输入的参数
})
```

### 终止连接

```js
connection.end(function(err){});
```

`end()`方法也可以接受一个回调函数，如果在终止mysql连接时，就会触发这个回调函数

### 连接池连接

```js
var pool = mysql.createPool({
    connectionLimit: 10,
    host: 'localhost',
    user: 'root',
    password: '123456',
    database: 'lznism'
});
// 使用连接
pool.getConnection(function(err, connection) {
    // 使用连接执行查询
    connection.query(SQL, function(err, rows) {
        
        // 连接不在使用，返回连接池
        connection.release();
    })
})
pool.query(SQL, function(err, rows, fields) {
    // ...
})
```

### 查询值转义
为了防止SQL注入，每当需要在SQL查询中使用用户数据时，都应该对这些值进行转义。转义可以通过`mysql.escape()`, `connection.escape()`, `pool.escape`来实现

```js
let sql = 'select * from users where id = ' + connection.escape(id);
```

### 查询标识符转义
如果用户提供了不可信的查询标识符(数据库名、表名、列名)，就应该使用`connection.escapeId()`来进行转义

```js
var sort = 'date';
var sql = 'select * from posts order by '+connection.escapeId(sort);
```






















