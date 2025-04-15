##### 出生中的出生，坐牢中的坐牢
序列化函数 serialize()
反序列化函数 unserialize()
先介绍魔术方法，以下是常见的魔术方法：

__construct() 类的构造函数
__destruct() 类的析构函数
__call() 在对象中调用一个不可访问方法时调用
__callStatic() 用静态方式中调用一个不可访问方法时调用
__get() 获得一个类的成员变量时调用
__set()设置一个类的成员变量时调用
__isset() 当对不可访问属性调用isset()或empty()时调用
__unset() 当对不可访问属性调用unset()时被调用。
__sleep() 执行serialize()时，先会调用这个函数
__wakeup() 执行unserialize()时，先会调用这个函数
__toString() 类被当成字符串时的回应方法
__invoke() 调用函数的方式调用一个对象时的回应方法
__set_state() 调用var_export()导出类时，此静态方法会被调用。
__clone() 当对象复制完成时调用
__autoload() 尝试加载未定义的类
__debugInfo() 打印所需调试信息

然后？然后你已经学完了，可以去打pop链了：）
~~啊？我？我打pop链？真的假的~~

在这里贴一份asalin师傅的触发样例吧：
```php
<?php
class A{
        public $a="hi";
        public $b="no";
        function __construct()
        {
            $this->a="hiiiii!";
            echo $this->a."\n";
            echo "this is construct\n";
        }
        function __wakeup()
        {
            echo "this is wakeup\n";
        }//反序列化之前
        function __destruct()
        {
            echo "this is destruct\n";
        }//反序列化时会最后才触发

        function __toString()
        {
            return "this is tostring\n";
        }
        function __call($name, $arguments)
        {
            echo "this is call\n";
        }
        function __get($a)
        {
            echo "this is get\n";
        }
        function __invoke()
        {
            echo "this is invoke\n";
        }//尝试当作函数
        

        function say_hi()
        {
            echo "hiuhiu\n";
        }
    }
    $aa=new A();// 所有最后都还要析构一次，对象的消失
    $aa->say_hi();
    $bb=serialize($aa);
    $cc=unserialize($bb); 
    echo $aa;// 作为字符串用时触发 tostring
    $aa->say_no(); //call
    $aa->c; //get
    $aa(); //invoke
```

一些绕过：
1.protected和private绕过

绕过的方法：
①：php7.1+反序列化对类属性不敏感，将protected改成public
②：手动将序列化后的形式改为protected或者private的标准形式，结合urlencode和base64编码进行操作

2.__wakeup绕过(比较常见)

原理：
当序列化字符串中表示对象属性个数的值大于真实的属性个数时会跳过__wakeup 的执行

示例：
O:4:"Dino":1:{s:1:"a";s:4:"misc";}改为O:4:"Dino":2:{s:1:"a";s:4:"misc";}

3.利用16进制绕过字符过滤

示例：序列化结果：O:4:"Dino":1:{s:3:"way";s:3:"web";}中含有字符web，但将s改成S后，O:4:"Dino":1:{S:3:"\\77ay";s:3:"web";}利用十六进制绕过了字符的过滤检测

4.利用加号绕过正则匹配

因为加号序列化时会被直接拼接，而正则匹配时不会，所以可以绕过；

5.好像还有一种添加元素进行绕过的，不记得是什么了，以后再碰到再补吧。