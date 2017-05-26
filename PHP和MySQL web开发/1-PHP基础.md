使用PHP获取表单传值三种方式对比，比如从表单POST过来一个叫aaa的变量,一下三种方法都可以获取这个值
```php
$aaa;
$_POST['aaa'];
$HTTP_POST_VARS['aaa'];
```
- 第一种：需要将`php.ini`中`register_globals`配置项设置为on,但是由于安全性的原因，这个选项一直默认的是false,因此本方法不是最好的方法
- 第二种：是推荐使用的，不会产生安全性问题
- 第三种：已经弃用

`$_POST`, `$_GET`, `$_REQUEST`都是一个数组，这些数组被称为`超级全局变量`

变量可以写到双引号中，单引号不会解析变量
`heredoc`写法允许插入一个长字符串
```php
echo <<<theEnd
    line 1
    line 2
    line 3
theEnd
```

需要注意的是最后的结束标记必须顶格写。

### 理解标识符
PHP标识符区分大小写

### 变量类型检查
- integer
- float
- string
- boolean
- array
- object
- null
- resource

变量的类型是由声明的时候确定的

### 声明和使用常量

```php
define('NAME', 'lznism');
```

### 理解变量的作用于
- 超全局变量
- 常量
- 脚本可见
- “函数内部使用的变量声明为全局变量时，其名称要与全局变量名称一致。”
- 静态变量(执行过程中保持该值)
- 函数内部创建的变量

### 使用操作符
- 复制操作符
- 字符串连接操作符
- 算术操作符(如果运算数是字符串，PHP试图将字符串转化成数字)
- 复合赋值操作符(`+=`, `-=`) (引用操作符`&`)

`number_format(float_num, 保留的小数位)`

```php
number_format(1.111, 2); // 1.11
```

### 理解操作符的优先级和结合性

### 使用可变函数
两个最常见的函数
- gettype
- settype

测试变量状态`isset`
查看变量是否定义

```php
# 如果变量$a存在则返回true，否则返回false;
isset($a);
```

销毁一个变量`unset()`

函数empty()可以用来检查一个变量是否存在`empty($var)`

### 根据条件进行决策
- if/else
- elseif
- switch/case

### 通过迭代实现不同的动作
- while
- for
- foreach 专用于数组的循环
- do...while 至少执行一次

### 从控制结构或者脚本中跳出
- break 停止循环
- continue 跳到下一次循环
- exit 退出整个PHP脚本

### 使用可替换的控制结构语法
一种奇怪的语法

```php
if(condition):
$name = 'lznism';
echo $name;
endif;
```

### 使用declare

基本用法

```php
declare(directive) {
	// block
}
```

这种结构用来设置代码块的执行指令--也就是，关于后续代码如何执行的规则