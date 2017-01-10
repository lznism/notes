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