#### PHP中创建类，属性和操作

```php
class Classname {
    // 构造函数，构造函数名称必须是__constructor
    function __constructor($name){

    }

    // 类属性
    public $attr1;
    public $attr2;

    // 类方法
    function func1(){}
    function func2(){}

    // 析构函数, 该函数与构造函数相对应，名称必须是__destructor
    function __destruct(){
        // 允许在销毁一个类之前执行一些操作或者完成一些功能
    }
}
```

#### 类的实例化
```php
$a = new Classname('aaa');
$b = new Classname('bbb');
```

#### 使用类属性
```php
class ClassName {
    public $attr;

    function operate($param) {
        $this -> $attr = $param;
        echo $this -> $attr;
    }
}
```
在类中有一个特殊的指针`$this`，如果当前类有一个属性为`$attr`, 在类中我们可以通过`$this -> $attr`访问这个变量
通常在类的外部访问类的属性是很糟糕的想法。面向对象方法的一个优点就是鼓励使用封装。可以通过使用`__get()`和`__set()`函数来实现对属性的访问
```php
class Classname {
    public $attr;
    function __get($name) {
        return $this -> $name;
    }
    function __set($name, $value){
        $this -> $name = $value;
    }
}
```

#### 使用private和public关键字控制访问
- public 公有的属性和方法可以在类的内部和外部进行访问
- private 访问修饰符以为这被标记的属性或方法只能在类的内部进行访问，私有的属性和方法也不会被继承
- protected 被标记的属性或方法只能在类内部进行访问。它也存在于任何子类

#### 类操作的调用
```php
class ClassName {
    function func1(){}
    function func2(){}
}
var c = new ClassName();
c -> func1();
c -> func2();
```

#### 在PHP中实现继承
```php
class A {
    public $attr1;
    function func1(){}
}
class B extends A {
    public $attr2;
    function func2(){}
}
$b = new B();
$b -> func1();
$b -> $attr1 = 10;
$b -> func2();
$b -> $attr2 = 10;
```

#### 通过继承使用private和protected访问修饰符控制可见性
使用了private修饰属性或者方法之后将不可继承，使用了protected修饰的函数可以被继承但是只能在子类内部使用

#### 重载
再次在子类中声明相同的属性和操作也是有效的，而且在有些情况下这将会非常有用
```php
class A {
    public $attr;
    function func(){}
}
class B extends A {
    public $attr;
    function func(){}
}
```
上面的代码中B继承了A，但是B中的重新定义不会影响到A

#### 使用final关键字禁止继承和重载
```php
class A {
    public $attr;
    final function func(){}
}

// 要禁止一个类被继承，也可以使用final关键字
final class A {

}
```

#### 理解多重继承
每个类都只能继承一个父类

#### 实现接口
接口可以看做是多重继承问题的解决方法
接口的思想是指定一个实现了该接口的类必须实现的一系列的函数。
```php
interface Displayable {
    function display() ;
}
class webPage implements Disolayable {
    function display(){}
}
```

#### 使用Pre-Class常量
```php
class Math {
    const pi = 3.14;
}
echo Math :: pi;
```
可以通过使用`::`操作符指定常量所属的类来访问`Pre-Class`常量

#### 实现静态方法
```php
class Math {
    static function square($input){
        return $input * $input;
    }
}
echo Math::square(9);
```
在静态方法中不能使用this关键字。因为可能会没有可以引用的对象实例

#### 延迟静态绑定
该特性允许在一个静态继承的上下文中对一个调用类的引用。父类可以使用子类重载的静态方法
```php
class A {
    public static function who() {
        echo __CLASS__;
    }
    public static function test(){
        static::who();
    }
}
```


#### 克隆对象
```php
$c = clone $b;
```
将创建与对象$b相同类的副本，而且具有相同的属性值

##### 使用抽象类
```php
abstract operationX($param1, $param2)
abstract class A {
    abstract function operationX($param1, $param2);
}
```