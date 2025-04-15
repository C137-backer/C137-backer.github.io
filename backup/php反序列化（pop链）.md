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