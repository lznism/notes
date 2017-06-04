### 什么是数组

### 访问数组的内容
PHP中数组的定义也不需要初始化
```php
$arr[0] = 1;
$arr[1] = 2;
var_dump($arr);
```

使用foreach循环

```php
$arr = [1,2,3];
foreach($arr as $current) {
    echo $current;
}
```

### 使用不同索引的数组
```php
$arr = array('a' =>　１，　'b' => 2, 'c' => 3);
```

访问数组元素

```php
$arr['a'];
```
foreach循环访问不同索引的数组

```php
foreach($arr as $key => $val){
    echo "$key => $val <br >";
}
```

while搭配list()和each()访问不同索引的数组

```php
while(list($key, $val) = each($arr)){
    echo "$key => $val <br >";
}
```

使用each()函数的副作用：数组会记录当前元素。如果希望在相同的脚本中两次使用该数组就必须使用`reset()`将当前元素重新设置到数组开始处

### 数组操作符
+   联合
==  等价
=== 恒等，如果$a,$b中包含相同顺序和类型的元素，返回true
!=  不等价，相同功能的还有`<>`
!== 如果$a和$b中不包含相同顺序类型的元素，返回true

### 多维数组
```php
$mutiArr = array(
    array(1,2,3,4),
    array(4,5,6,7)
);
```

使用循环来访问多维数组

```php
for($row = 0; $row < 2; $row++){
    for($column = 0; $column < 4; $column++){
        echo '|'.$mutiArr[$row][$column];
    }
    echo '<br>';
}
```

### 数组排序
`sort()`函数
```php
$a = array(2,1,3);
sort($a);
var_dump($a); // => 1,2,3
```
sort()函数还接收第二个参数`SORT_NUMERIC`, `SORT_STRING`,表示按照数字格式比较还是按照字符串格式比较

asort()和ksort()对关联数组排序
- asort 按照value排序，升序
- ksort 按照key排序。升序

```php
$arr = array('b' =>　2，　'a' => 1, 'c' => 3);
asort($arr);
var_dump($arr);
```

反向排序
arsort()和krsort()
- arsort 按照value排序，降序
- krsort 按照value排序，降序

### 多维数组排序
usort()告诉PHP如何比较各个元素。要实现此功能，需要编写自己的比较函数
```php
function compare($x, $y){
    if($x[1] == $y[1]){
        return 0;
    }elseif($x[1] < $y[1]){
        return -1;
    }else{
        return 1;
    }
}
usort($mutiArr, 'compare');
```

### 对数组进行重新排序
shuffle() 将数组进行重新排序
array_reverse() 将数组逆序

### 从文件载入数组


#### 数组的其余操作函数
- each()
- current()     数组的指针被初始化，返回数组的第一个元素
- reset()       返回指向数组第一个元素的指针
- end()         返回指向数组最后一个元素的指针
- next() 
- pos()
- prev()
- array_walk()  对数组每一个元素都应用任何一个函数
- count()       统计数组元素个数
- sizeof()      和count()方法的作用类似
- array_count_values()  这个函数会统计每个特定的值在数组中出现的次数
- extract()     对于一个非数组索引数组，该数组又有许多键值对，可以使用extract()将他们转化成一系列标量变量

```php
# 反向显示数组的元素
$value = end($array);
while($value) {
    echo $value. '<br />';
    $value = prev($array);
}
```